# Modelo de Dados (Camada Gold)

Para o consumo final dos analistas de dados e ferramentas de BI (Business Intelligence), os dados foram estruturados em um **Modelo Estrela (Star Schema)**.

Esse formato separa o processo de negócio (as métricas numéricas) do contexto descritivo (quem, onde, quando).

## 🌟 Tabela Fato (Eventos)

A tabela central que registra as ocorrências de negócio da seguradora.

| Tabela | Coluna | Tipo | Descrição |
| :--- | :--- | :--- | :--- |
| **fato_sinistro** | `SK_TEMPO` | INT | Chave substituta para a data do acidente. |
| | `SK_CLIENTE` | INT | Chave substituta para o segurado. |
| | `SK_CARRO` | INT | Chave substituta para o veículo envolvido. |
| | `SK_LOCALIDADE`| INT | Chave substituta para o local do acidente. |
| | `QUANTIDADE` | INT | Métrica: Conta o número de ocorrências (valor padrão 1). |

---

## 🗂️ Tabelas Dimensão (Contexto)

Fornecem os detalhes descritivos para filtrar e agrupar os dados da tabela fato.

| Dimensão | Atributos Principais | Propósito para o Negócio |
| :--- | :--- | :--- |
| **dim_cliente** | Nome, CPF, Sexo, Data de Nascimento. | Permite analisar sinistros por faixa etária ou gênero do motorista. |
| **dim_carro** | Placa, Marca, Modelo, Ano, Cor. | Essencial para descobrir quais modelos de carros batem mais frequentemente. |
| **dim_localidade**| Município, Estado, Região. | Crucial para mapear zonas de alto risco e ajustar o preço do seguro por Estado/Região. |
| **dim_tempo** | Data, Mês, Ano, Trimestre. | Usada para analisar sazonalidade (ex: batidas aumentam nos meses de férias?). |