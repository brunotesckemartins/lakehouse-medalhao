# Bem-vindo ao Projeto Lakehouse Seguradora

Este projeto foi desenvolvido como parte da disciplina de Engenharia de Dados. O objetivo é demonstrar a implementação prática de um pipeline de processamento de Big Data focado no setor de seguros automotivos.

## 📌 Contexto do Negócio
Uma seguradora gera diariamente milhares de registros em seu sistema transacional (banco de dados operacional). Esses dados incluem cadastros de clientes, veículos segurados, apólices emitidas e o registro de acidentes (sinistros).

Para que os diretores da seguradora possam tomar decisões estratégicas — como calcular o risco por região ou por modelo de carro —, esses dados precisam ser extraídos, limpos e organizados em um formato analítico.

## 🎯 O Desafio Técnico
Integrar um banco de dados operacional externo (Supabase/PostgreSQL) com uma plataforma de Big Data (Databricks), criando um fluxo automatizado que garanta a qualidade e a governança dos dados.