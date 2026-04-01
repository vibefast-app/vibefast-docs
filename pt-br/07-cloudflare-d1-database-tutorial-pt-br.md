# Fundamentos do Banco de Dados D1: Usando IA para Projetar Sua Primeira Tabela

**Autor:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Atualizado:** Março de 2026  
**Tempo de leitura:** ~8 minutos

-----

## Seu App Precisa Lembrar das Coisas

Usuários se cadastram — você precisa armazenar as contas deles. Eles publicam um post — você precisa salvar. Eles fazem um pedido — você precisa registrar.

**Um banco de dados é a memória de longo prazo do seu app.**

Cloudflare D1 é o serviço de banco de dados do Cloudflare — tecnicamente SQLite, rodando na rede de edge do Cloudflare no mesmo lugar que seus Workers. Leituras e escritas são extremamente rápidas.

-----

## O Que É uma Tabela?

Os dados em um banco de dados são organizados em tabelas — pense nelas como planilhas:

**Tabela users:**

|id|email            |name |created_at|
|--|-----------------|-----|----------|
|1 |danko@example.com|Danko|2026-03-01|
|2 |user2@example.com|Alice|2026-03-02|

Cada coluna é um campo. Cada linha é um registro.

-----

## Usando IA para Projetar uma Tabela

Esse é um dos cenários mais práticos do Vibe Coding. Você não precisa conhecer sintaxe SQL — só diga à IA o que você precisa:

> "Preciso de uma tabela para armazenar posts de blog. Deve ter título, conteúdo, autor, status de publicação e timestamp de criação. Use a sintaxe SQLite do Cloudflare D1."

A IA gera algo assim:

```sql
CREATE TABLE posts (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  title TEXT NOT NULL,
  slug TEXT NOT NULL UNIQUE,
  content TEXT,
  author_id INTEGER NOT NULL,
  status TEXT NOT NULL DEFAULT 'draft',
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (author_id) REFERENCES users(id)
);
```

Você não precisa entender completamente cada linha, mas conhecer os conceitos básicos ajuda a avaliar o output da IA.

-----

## Conceitos-Chave Explicados

**PRIMARY KEY**: O identificador único de cada registro. Como um CPF — dois registros não compartilham o mesmo valor.

**NOT NULL**: Esse campo não pode ficar vazio. Um título de post não pode ser em branco, então adicionamos NOT NULL.

**DEFAULT**: O valor usado quando nada é fornecido. `status` tem padrão `'draft'`, `created_at` tem padrão o horário atual.

**FOREIGN KEY**: Vincula a outra tabela. `author_id` referencia o `id` na tabela `users` — registra "quem escreveu esse post."

-----

## Consultando o D1 no Cloudflare Workers

Após criar suas tabelas, é assim que você trabalha com dados no Workers:

```typescript
// Inserir um novo produto
await env.DB.prepare(`
  INSERT INTO products (name, price, stock)
  VALUES (?, ?, ?)
`).bind(name, price, stock).run()

// Ler todos os produtos em estoque
const products = await env.DB.prepare(`
  SELECT * FROM products
  WHERE stock > 0
  ORDER BY created_at DESC
  LIMIT 10
`).all()
```

Você não precisa escrever isso você mesmo — diga à IA "busque todos os produtos com estoque maior que zero, ordenados por data de criação decrescente" e ela gera o código correto.

-----

## Prompts Comuns de IA para Operações de Banco de Dados

Coisas que você pode dizer diretamente à IA:

**Ler:**

> "Busque os 10 posts publicados mais recentes da tabela posts, incluindo o nome do autor"

**Inserir:**

> "Adicione um novo registro de pedido na tabela orders com campos user_id, amount e status"

**Atualizar:**

> "Defina o status do post id 5 como publicado"

**Deletar:**

> "Delete todos os posts em rascunho do user_id 123"

Sempre use queries parametrizadas no código gerado (placeholders `?`) para prevenir SQL Injection — coberto no tutorial de segurança.

-----

## Folha de Referência SQL

Você não precisa memorizar sintaxe SQL — cole qualquer coisa que não entenda para a IA. Mas esses padrões aparecem constantemente:

### Ler (SELECT)

```sql
-- Pegar todos os produtos
SELECT * FROM products

-- Pegar apenas colunas específicas
SELECT id, name, price FROM products

-- Filtrar com uma condição
SELECT * FROM products WHERE stock > 0

-- Ordenar resultados
SELECT * FROM products ORDER BY created_at DESC

-- Limitar resultados
SELECT * FROM products LIMIT 10

-- Paginação
SELECT * FROM products LIMIT 10 OFFSET 20

-- Múltiplas condições
SELECT * FROM products WHERE stock > 0 AND price < 500
```

### Inserir (INSERT)

```sql
INSERT INTO products (name, price, stock)
VALUES ('Sabonete de Lavanda', 28, 50)
```

### Atualizar (UPDATE)

```sql
-- Atualizar um registro específico (sempre use WHERE — sem ele você atualiza todas as linhas)
UPDATE products SET stock = 45 WHERE id = 1

-- Atualizar múltiplos campos de uma vez
UPDATE orders SET status = 'shipped', updated_at = CURRENT_TIMESTAMP WHERE id = 5
```

### Deletar (DELETE)

```sql
-- Deletar um registro específico (sempre use WHERE)
DELETE FROM products WHERE id = 1
```

### Unir Duas Tabelas (JOIN)

```sql
-- Pegar pedidos com o e-mail do cliente
SELECT orders.id, orders.amount, users.email
FROM orders
JOIN users ON orders.user_id = users.id
WHERE orders.status = 'paid'
```

**Sempre use queries parametrizadas no Cloudflare Workers** (previne SQL Injection):

```typescript
// Use placeholders ? — nunca concatene strings diretamente
const product = await env.DB.prepare(
  'SELECT * FROM products WHERE id = ?'
).bind(productId).first()

const products = await env.DB.prepare(
  'SELECT * FROM products WHERE stock > ? ORDER BY created_at DESC LIMIT ?'
).bind(0, 10).all()
```

-----

## Limites do D1

**Plano gratuito:**

- Armazenamento: 5GB
- Leituras diárias: 100.000
- Escritas diárias: 50.000

Mais do que suficiente para a maioria dos apps começando.

**Quando o D1 não é o adequado:**

- Escritas distribuídas geograficamente em alto volume (D1 tem um único nó primário de escrita)
- Throughput muito alto de escritas concorrentes (milhares de escritas por segundo)
- Pesquisa full-text complexa (a pesquisa full-text do SQLite é limitada)

Para 90% dos apps indie, o D1 é mais do que suficiente.

-----

## Resumo

D1 é a memória de longo prazo do seu app — onde você armazena usuários, posts, pedidos e qualquer outro dado estruturado.

O fluxo prático do Vibe Coder:

1. Diga à IA em linguagem natural quais tabelas você precisa
1. IA gera o SQL — revise
1. Execute para criar as tabelas
1. Use `env.DB.prepare()` para queries, com a IA escrevendo o código de query

-----

👉 [Próximo: R2 vs S3 — Por Que Não Uso AWS para Armazenamento](./08-cloudflare-r2-vs-aws-s3-pt-br.md)

Dúvidas? Me encontre no X em [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, preço sobe para $199 em 1º de junho de 2026.
