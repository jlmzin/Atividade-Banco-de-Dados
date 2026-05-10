# Atividade-Banco-de-Dados
Estruturas de Indexação e Performance de Dados
# 📚 Atividade — Índices em Banco de Dados e Elasticsearch

---

# 📌 Parte 1 — Conteúdo Técnico

---

# 1. Fundamentos e Estruturas

## O que são índices?

Os índices em banco de dados funcionam como um “atalho” para encontrar informações rapidamente, sem precisar ler toda a tabela.

### Sem índice
O banco faz uma varredura completa (*Full Table Scan*), lendo linha por linha.

### Com índice
O banco encontra os dados muito mais rápido, acessando apenas os registros necessários.

---

## Exemplo simples

Imagine uma tabela com 10 milhões de usuários.

Consulta:

```sql
SELECT * FROM usuarios WHERE email = 'ana@gmail.com';
