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
🔎 Diferença entre B-Tree e Hash
🌳 B-Tree (Árvore B)

A B-Tree organiza os dados de forma ordenada.

Ela é excelente para:

buscas exatas
intervalos
ordenação
prefixos
📖 Analogia com dicionário físico

Imagine um dicionário.

Você não procura palavra por palavra.
Você abre aproximadamente no meio, verifica a letra e vai refinando até encontrar a palavra desejada.

A B-Tree funciona exatamente assim:

divide os dados em blocos organizados
reduz leituras desnecessárias
encontra dados rapidamente
✅ Vantagens da B-Tree

Suporta:

=
>
<
BETWEEN
ORDER BY
LIKE 'Ana%'
#️⃣ Hash

O índice Hash funciona usando uma função matemática chamada hash.

Ela transforma o valor em um endereço específico.

🎯 Analogia simples

Imagine um armário com gavetas numeradas:

cada nome gera automaticamente uma gaveta específica
o banco vai direto nela
✅ Vantagens do Hash

Muito rápido para igualdade:

WHERE email = 'teste@gmail.com'
❌ Limitações

Não funciona bem para:

ordenação
intervalos
prefixos

Exemplo:

WHERE nome LIKE 'Ana%'

Hash não ajuda nesse caso.

2. Densidade de Índices
📌 Índice Denso

Possui uma entrada para cada registro da tabela.

Exemplo

Tabela:

ID	Nome
1	Ana
2	Carlos
3	João

Índice:

Chave	Ponteiro
1	Linha 1
2	Linha 2
3	Linha 3
Características
mais rápido
ocupa mais espaço
📌 Índice Esparso

Não possui entrada para todas as linhas.

Indexa apenas alguns blocos ou referências principais.

Exemplo
Chave	Ponteiro
1	Bloco A
100	Bloco B
200	Bloco C
Características
economiza espaço
busca um pouco mais lenta
📊 Quando usar cada um?
Tipo	Melhor uso	Vantagem	Desvantagem
Denso	buscas rápidas	alta velocidade	maior espaço
Esparso	tabelas gigantes	economiza armazenamento	menor velocidade
3. Tomada de Decisão e Cardinalidade
🔑 Chave Primária (ID)
Melhor índice: B-Tree
Justificativa

Porque:

IDs são únicos
consultas por igualdade são frequentes
ordenação funciona bem
inserções são eficientes
Exemplo
SELECT * FROM usuarios WHERE id = 150;

A B-Tree é o padrão do PostgreSQL para chave primária.

👤 Nome
Melhor índice: B-Tree

Especialmente para buscas por prefixo:

WHERE nome LIKE 'Ana%'

A B-Tree localiza rapidamente nomes iniciados por “Ana”.

🌍 Cidade

Depende da cardinalidade.

Alta cardinalidade

Muitas cidades diferentes:

índice pode ajudar
Baixa cardinalidade

Milhões de registros em poucas cidades:

índice pode não compensar
Exemplo
5 milhões de usuários
apenas 10 cidades

O banco provavelmente fará leitura completa da tabela.

⚧ Sexo
Não é recomendado indexar
Motivos
baixa cardinalidade
poucos valores possíveis

Exemplo:

Masculino
Feminino
Outro
Problema

O banco precisaria:

usar índice
acessar milhões de registros depois

Isso pode ser mais lento que um Full Scan.

4. Ecossistema PostgreSQL
🌳 B-Tree

Mais utilizado.

Ideal para:

igualdade
intervalos
ordenação
prefixos
📚 GIN (Generalized Inverted Index)

Muito usado para:

JSONB
arrays
Full Text Search

Excelente para buscas complexas em documentos.

📍 GiST (Generalized Search Tree)

Usado para:

dados geográficos
coordenadas
buscas espaciais

Muito comum com PostGIS.

🧱 BRIN (Block Range Index)

Ideal para tabelas gigantes.

Indexa blocos ao invés de linhas individuais.

Excelente para:

logs
séries temporais
dados ordenados por data

Consome pouco espaço.

🛠 Criando índice no PostgreSQL
Exemplo
CREATE INDEX idx_email
ON usuarios(email);
🔍 Verificando uso do índice
Comando
EXPLAIN ANALYZE
SELECT * FROM usuarios
WHERE email = 'teste@gmail.com';
Resultado esperado

Se o índice estiver sendo usado:

Index Scan using idx_email

Se não:

Seq Scan
5. Alta Performance com Elasticsearch
🔄 Índice Invertido

O Elasticsearch utiliza Índice Invertido.

Ao invés de armazenar:

documento → palavras

Ele armazena:

palavra → documentos
📌 Exemplo

Documentos:

ID	Texto
1	“Ana gosta de banco de dados”
2	“Carlos estuda Elasticsearch”

Índice invertido:

Palavra	Documentos
Ana	1
banco	1
Elasticsearch	2

Assim a busca textual fica extremamente rápida.

🚀 Quando usar Elasticsearch?
PostgreSQL é melhor para:
transações
integridade
consultas relacionais
Elasticsearch é melhor para:
busca textual avançada
autocomplete
relevância
milhões de documentos
pesquisas estilo Google
📌 Exemplos ideais

Sistemas como:

e-commerce
Netflix
Spotify
redes sociais

Onde usuários pesquisam:

frases
palavras
aproximações
relevância textual
🎤 Parte 2 — Defesa Oral
❓ Pergunta provável
“Por que não podemos indexar todas as colunas?”
✅ Resposta ideal

Porque índices também possuem custo.

Cada índice:

ocupa armazenamento
consome memória
precisa ser atualizado em:
INSERT
UPDATE
DELETE

Quanto mais índices:

mais lenta fica a escrita no banco.
⚖ Trade-off (Custo vs Benefício)
Benefício	Custo
consultas rápidas	maior uso de disco
melhor leitura	maior consumo de RAM
buscas eficientes	pior performance de escrita
