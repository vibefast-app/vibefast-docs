# D1 Database Basics: Using AI to Design Your First Table

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## Your App Needs to Remember Things

Users sign up — you need to store their accounts. They publish a post — you need to save it. They place an order — you need to record it.

**A database is your app’s long-term memory.**

Cloudflare D1 is Cloudflare’s database service — technically SQLite, running on Cloudflare’s edge network in the same place as your Workers. Reads and writes are extremely fast.

-----

## What is a Table?

Data in a database is organized into tables — think of them like spreadsheets:

**users table:**

|id|email            |name |created_at|
|--|-----------------|-----|----------|
|1 |danko@example.com|Danko|2026-03-01|
|2 |user2@example.com|Alice|2026-03-02|

Each column is a field. Each row is a record.

-----

## Using AI to Design a Table

This is one of the most practical Vibe Coding scenarios. You don’t need to know SQL syntax — just tell AI what you need:

> “I need a table for storing blog posts. It should have a title, content, author, publish status, and creation timestamp. Use Cloudflare D1’s SQLite syntax.”

AI generates something like:

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

You don’t need to fully understand every line, but knowing the basic concepts helps you evaluate AI’s output.

-----

## Key Concepts Explained

**PRIMARY KEY**: The unique identifier for each record. Like a national ID number — no two records share the same value.

**NOT NULL**: This field cannot be empty. A post title can’t be blank, so we add NOT NULL.

**DEFAULT**: The value used when nothing is provided. `status` defaults to `'draft'`, `created_at` defaults to the current time.

**FOREIGN KEY**: Links to another table. `author_id` references the `id` in the `users` table — it records “who wrote this post.”

-----

## Querying D1 in Cloudflare Workers

After creating your tables, this is how you work with data in Workers:

```typescript
// Insert a new product
await env.DB.prepare(`
  INSERT INTO products (name, price, stock)
  VALUES (?, ?, ?)
`).bind(name, price, stock).run()

// Read all in-stock products
const products = await env.DB.prepare(`
  SELECT * FROM products
  WHERE stock > 0
  ORDER BY created_at DESC
  LIMIT 10
`).all()
```

You don’t need to write these yourself — tell AI “query all products with stock greater than zero, ordered by creation date descending” and it generates the correct code.

-----

## Common AI Prompts for Database Operations

Things you can tell AI directly:

**Read:**

> “Fetch the 10 most recent published posts from the posts table, including the author’s name”

**Insert:**

> “Add a new order record to the orders table with fields user_id, amount, and status”

**Update:**

> “Set the status of post id 5 to published”

**Delete:**

> “Delete all draft posts for user_id 123”

Always use parameterized queries in the generated code (`?` placeholders) to prevent SQL Injection — covered in the security tutorial.

-----

## SQL Cheatsheet

You don’t need to memorize SQL syntax — paste anything you don’t understand to AI. But these patterns come up constantly:

### Read (SELECT)

```sql
-- Get all products
SELECT * FROM products

-- Get specific columns only
SELECT id, name, price FROM products

-- Filter with a condition
SELECT * FROM products WHERE stock > 0

-- Sort results
SELECT * FROM products ORDER BY created_at DESC

-- Limit results
SELECT * FROM products LIMIT 10

-- Pagination
SELECT * FROM products LIMIT 10 OFFSET 20

-- Multiple conditions
SELECT * FROM products WHERE stock > 0 AND price < 500
```

### Insert (INSERT)

```sql
INSERT INTO products (name, price, stock)
VALUES ('Lavender Soap', 280, 50)
```

### Update (UPDATE)

```sql
-- Update a specific record (always use WHERE — without it you update every row)
UPDATE products SET stock = 45 WHERE id = 1

-- Update multiple fields at once
UPDATE orders SET status = 'shipped', updated_at = CURRENT_TIMESTAMP WHERE id = 5
```

### Delete (DELETE)

```sql
-- Delete a specific record (always use WHERE)
DELETE FROM products WHERE id = 1
```

### Join Two Tables (JOIN)

```sql
-- Get orders with the customer's email address
SELECT orders.id, orders.amount, users.email
FROM orders
JOIN users ON orders.user_id = users.id
WHERE orders.status = 'paid'
```

**Always use parameterized queries in Cloudflare Workers** (prevents SQL Injection):

```typescript
// Use ? placeholders — never concatenate strings directly
const product = await env.DB.prepare(
  'SELECT * FROM products WHERE id = ?'
).bind(productId).first()

const products = await env.DB.prepare(
  'SELECT * FROM products WHERE stock > ? ORDER BY created_at DESC LIMIT ?'
).bind(0, 10).all()
```

-----

## D1’s Limits

**Free tier:**

- Storage: 5GB
- Daily reads: 100,000
- Daily writes: 50,000

More than enough for most apps starting out.

**When D1 isn’t the right fit:**

- Geographically distributed writes at high volume (D1 has a single primary write node)
- Very high concurrent write throughput (thousands of writes per second)
- Complex full-text search (SQLite’s full-text search is limited)

For 90% of indie apps, D1 is more than sufficient.

-----

## Summary

D1 is your app’s long-term memory — where you store users, posts, orders, and any other structured data.

The practical Vibe Coder workflow:

1. Tell AI in plain language what tables you need
1. AI generates the SQL — review it
1. Run it to create the tables
1. Use `env.DB.prepare()` for queries, with AI writing the query code

-----

👉 [Next: R2 vs S3 — Why I Don’t Use AWS for Storage](./10-r2-vs-s3-en.md)

Questions? Find me on X at [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price increases to $199 on June 1, 2026.