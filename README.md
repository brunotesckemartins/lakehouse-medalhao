# Data Lakehouse - Pipeline de Dados para Seguradora (Trabalho 3)

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Apache Spark](https://img.shields.io/badge/Apache_Spark-E25A1C?style=for-the-badge&logo=apache-spark&logoColor=white)
![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)

Este repositório documenta a implementação de um pipeline de Engenharia de Dados fim-a-fim, utilizando a **Arquitetura Medalhão (Medallion Architecture)**. O projeto foi desenvolvido como requisito de avaliação (Trabalho 3) para demonstrar a construção de um ambiente analítico escalável.

O caso de estudo baseia-se no ecossistema de uma Seguradora de Veículos, contemplando o fluxo de extração de dados de um sistema transacional (OLTP), transformação distribuída via Apache Spark e carga em um modelo dimensional (OLAP) otimizado para Business Intelligence.

---

## 1. Arquitetura do Sistema

O pipeline de dados foi desenhado para processar dados de forma sequencial, garantindo rastreabilidade, governança e qualidade através das seguintes camadas lógicas:

* **Landing Zone:** Ponto de entrada dos dados extraídos do PostgreSQL (Supabase) via conexão JDBC. Os dados são persistidos no formato original (`.csv`) em Volumes nativos do Databricks.
* **Camada Bronze (Raw):** Ingestão dos dados da Landing Zone para tabelas no formato `Delta`. O objetivo desta camada é manter o histórico imutável (append-only) e a fidelidade em relação à fonte.
* **Camada Silver (Cleansed):** Aplicação de regras de qualidade de dados (Data Quality). Os processos incluem deduplicação, normalização de strings (upper case, remoção de acentos), padronização de CPFs, parsing de datas e definição estrita de *data types*.
* **Camada Gold (Curated):** Consolidação dos dados limpos em um Modelo Estrela (Star Schema). Camada voltada estritamente para o consumo analítico por ferramentas de BI e Cientistas de Dados.

---

## 2. Modelagem de Dados

O projeto executa a transição de um modelo relacional altamente normalizado para um modelo dimensional desnormalizado.

### 2.1. Origem: Modelo Transacional (OLTP / 3NF)
O banco de dados fonte (PostgreSQL) é composto por 11 tabelas relacionais em 3ª Forma Normal:
* **Entidades de Cliente:** `cliente`, `telefone`, `endereco`.
* **Entidades de Veículo:** `marca`, `modelo`, `carro`.
* **Entidades Geográficas:** `regiao`, `estado`, `municipio`.
* **Entidades de Negócio:** `apolice`, `sinistro`.

### 2.2. Destino: Modelo Dimensional (OLAP / Star Schema)
Na Camada Gold, os dados foram modelados utilizando chaves substitutas (*Surrogate Keys - SK*) para garantir a independência do sistema de origem e otimizar agregações.

#### Tabela Fato
| Tabela | Coluna | Tipo de Dado | Descrição |
| :--- | :--- | :--- | :--- |
| **fato_sinistro** | `SK_TEMPO` | BIGINT | Foreign Key para dim_tempo. |
| | `SK_CLIENTE` | BIGINT | Foreign Key para dim_cliente. |
| | `SK_CARRO` | BIGINT | Foreign Key para dim_carro. |
| | `SK_LOCALIDADE` | BIGINT | Foreign Key para dim_localidade. |
| | `QUANTIDADE` | INT | Métrica aditiva (Valor default = 1). |

#### Tabelas Dimensão
| Tabela | Colunas Principais | Objetivo Analítico |
| :--- | :--- | :--- |
| **dim_cliente** | `SK_CLIENTE`, `CODIGO_CLIENTE`, `NOME`, `CPF`, `SEXO`, `DATA_NASCIMENTO` | Análise de perfil demográfico e risco por faixa etária/gênero. |
| **dim_carro** | `SK_CARRO`, `PLACA`, `MARCA`, `MODELO`, `ANO`, `COR` | Análise de sinistralidade por montadora e depreciação temporal. |
| **dim_localidade** | `SK_LOCALIDADE`, `MUNICIPIO`, `ESTADO`, `REGIAO` | Análise geoespacial para precificação regionalizada de apólices. |
| **dim_tempo** | `SK_TEMPO`, `DATA`, `DIA`, `MES`, `ANO`, `TRIMESTRE` | Análise de sazonalidade e tendências temporais de acidentes. |

---

## 3. Estrutura do Repositório

```text
lakehouse-medalhao/
├── notebooks/
│   ├── 00_preparando_ambiente.py  # DDL (Catálogos, Schemas e Volumes)
│   ├── 001_extracao_landing.py    # Ingestão via JDBC (Supabase -> CSV)
│   ├── 002_camada_bronze.py       # Conversão estruturada (CSV -> Delta)
│   ├── 003_camada_silver.py       # Transformações Pyspark e Data Cleansing
│   ├── 004_camada_gold.py         # Modelagem Star Schema e rotinas de MERGE
│   └── 005_destroi_ambiente.py    # Teardown de ambiente (Manutenção)
├── docs/
│   ├── index.md                   # Especificações de requisitos e negócio
│   ├── arquitetura.md             # Documentação do fluxo de dados
│   └── modelo_dados.md            # Dicionário de dados detalhado
├── mkdocs.yml                     # Arquivo de configuração do gerador de sites
├── .gitignore                     # Controle de artefatos não rastreáveis
└── README.md                      # Documentação principal