# Arquitetura do Sistema

A arquitetura lógica do projeto segue a **Medallion Architecture** (Arquitetura Medalhão), um padrão de design de dados que organiza de forma lógica os dados em um Lakehouse, com o objetivo de melhorar incrementalmente e progressivamente a estrutura e a qualidade dos dados à medida que fluem através de cada camada.

## 1. Topologia das Camadas

O pipeline de dados (ETL) foi estruturado em quatro estágios sequenciais e interdependentes:

### 1.1. Landing Zone (Zona de Pouso)
* **Objetivo:** Ingestão de dados brutos provenientes do PostgreSQL com o menor impacto possível no sistema de origem.
* **Formato:** Arquivos `CSV` delimitados.
* **Comportamento:** Atua como um *buffer* temporário. Os dados são extraídos das 11 tabelas transacionais via conexão JDBC e gravados em Volumes nativos do Databricks sem qualquer alteração de esquema ou tipagem.

### 1.2. Camada Bronze (Raw Data)
* **Objetivo:** Persistência do histórico e criação de uma réplica exata da fonte em um formato de alto desempenho.
* **Formato:** `Delta`.
* **Comportamento:** Os dados CSV são lidos, tipados primitivamente (como strings) e convertidos para o formato Delta. Esta camada é *append-only*, garantindo que nenhuma informação original seja perdida, permitindo reprocessamentos futuros caso as regras de negócio mudem na camada Silver.

### 1.3. Camada Silver (Cleansed Data)
* **Objetivo:** Higienização, validação e padronização. Esta é a camada de consolidação ("Enterprise View").
* **Formato:** `Delta`.
* **Comportamento:** Aplicação de lógicas de *Data Quality*. 
    * Tratamento de valores nulos (Null constraints).
    * Deduplicação de registros via chaves primárias.
    * Normalização de strings (funções `UPPER()` e `TRIM()`).
    * Padronização de CPFs e validação de formatos de Data (`DATE`).

### 1.4. Camada Gold (Curated Data)
* **Objetivo:** Entrega de valor para o negócio. Modelagem dimensional otimizada para sistemas de BI e algoritmos de Machine Learning.
* **Formato:** `Delta`.
* **Comportamento:** Os dados normalizados da camada Silver são unidos (JOINs) e desnormalizados em um esquema Estrela (*Star Schema*). Utiliza rotinas de `MERGE` (Upsert) para garantir a atualização incremental das Tabelas Fato e Dimensão, gerenciando inserções de novos registros e atualizações de registros existentes.

