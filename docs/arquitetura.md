# Arquitetura do Sistema

O projeto adota a **Arquitetura Medalhão**, uma das melhores práticas modernas de engenharia de dados. Ela garante que o dado seja refinado e ganhe valor agregado à medida que avança pelas camadas.

## O Fluxo de Processamento

| Camada | Formato | Objetivo e Transformações |
| :--- | :--- | :--- |
| **0. Origem** | PostgreSQL | Banco de dados transacional rodando no Supabase. Contém 11 tabelas relacionais normalizadas. |
| **1. Landing Zone** | `.csv` | Zona de pouso temporária. Os dados chegam brutos e idênticos à origem para não sobrecarregar o banco transacional. |
| **2. Camada Bronze** | `Delta` | Histórico fiel. Os dados são convertidos de CSV para o formato Delta Lake, mantendo a estrutura bruta, mas ganhando performance. |
| **3. Camada Silver** | `Delta` | A "fonte da verdade". Aqui os dados são higienizados: remoção de duplicatas, padronização de CPFs, correção de datas e tipagem de colunas. |
| **4. Camada Gold** | `Delta` | Camada analítica. As tabelas altamente normalizadas são unidas e convertidas em um Modelo Dimensional (Star Schema). |