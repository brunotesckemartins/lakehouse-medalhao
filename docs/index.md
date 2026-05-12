# Visão Geral do Projeto

## 1. Introdução e Contexto de Negócio
O presente projeto documenta a concepção e implementação de uma plataforma de processamento de dados baseada no paradigma **Data Lakehouse**, desenvolvida especificamente para o setor de Seguros de Automóveis.

Organizações do setor de seguros lidam com um volume crescente de dados heterogêneos originados de seus sistemas operacionais transacionais (OLTP). Estes dados incluem o registro contínuo de novos clientes, emissão de apólices e a notificação de sinistros (acidentes ou roubos). 

O desafio central que motivou a construção desta arquitetura é a ineficiência de realizar consultas analíticas complexas diretamente no banco de dados operacional, o que degrada a performance do sistema transacional e não oferece uma estrutura otimizada para ferramentas de *Business Intelligence* (BI).

## 2. Objetivos do Sistema
A plataforma foi arquitetada para solucionar este gargalo tecnológico através dos seguintes objetivos:

1. **Desacoplamento Analítico:** Isolar a carga de trabalho de leitura analítica (OLAP) do banco de dados operacional responsável pela escrita transacional (OLTP).
2. **Centralização (Single Source of Truth):** Consolidar informações espalhadas por diversas tabelas relacionais em um repositório unificado e escalável.
3. **Governança e Qualidade:** Implementar rotinas automatizadas de *Data Cleansing* (limpeza de dados), garantindo padronização de formatos e integridade referencial.
4. **Disponibilidade para Decisão:** Entregar os dados em um modelo dimensional (Star Schema) pronto para consumo, permitindo que gestores analisem índices de sinistralidade por região, perfil demográfico e características do veículo.

## 3. Escopo Tecnológico
Para atender aos requisitos de alta disponibilidade e processamento distribuído, a solução foi construída utilizando a stack de Big Data padrão de mercado:

* **Databricks & Apache Spark:** Motor de processamento distribuído utilizado para a orquestração e execução das rotinas de ETL/ELT.
* **Delta Lake:** Camada de armazenamento *open-source* que traz confiabilidade (transações ACID) aos data lakes.
* **PostgreSQL (Supabase):** Sistema Gerenciador de Banco de Dados Relacional (SGBDR) que atua como o sistema fonte das operações diárias da seguradora.