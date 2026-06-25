# D1 資料庫入門：用 AI 設計你的第一個資料表

[English](../en/07-cloudflare-d1-database-tutorial-en.md) · [繁中](../zh/07-cloudflare-d1-database-tutorial-zh.md) · [Español](../es/07-cloudflare-d1-database-tutorial-es.md) · [日本語](../jp/07-cloudflare-d1-database-tutorial-jp.md) · [Português (BR)](../pt-br/07-cloudflare-d1-database-tutorial-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 8 分鐘

-----

## 你的 app 需要記住東西

用戶註冊了，你要記住他們的帳號。他們發了一篇文章，你要存起來。他們買了東西，你要記錄訂單。

**資料庫就是你 app 的長期記憶。**

Cloudflare D1 是 Cloudflare 提供的資料庫服務——技術上是 SQLite，但跑在 Cloudflare 的邊緣網路上，和你的 Workers 在同一個地方，讀寫速度極快。

-----

## 資料表是什麼？

資料庫裡的資料用「表」（table）來組織，就像 Excel 試算表：

**users 表：**

|id|email            |name |created_at|
|--|-----------------|-----|----------|
|1 |danko@example.com|Danko|2026-03-01|
|2 |user2@example.com|Alice|2026-03-02|

每一欄是一個「欄位」（column），每一行是一筆「記錄」（row）。

-----

## 用 AI 設計資料表

這是 Vibe Coding 最實用的場景之一。你不需要自己想 SQL 語法，直接告訴 AI：

> 「我需要一個儲存部落格文章的資料表，要有標題、內容、作者、發布狀態、建立時間。用 Cloudflare D1 的 SQLite 語法。」

AI 會生成類似這樣的 SQL：

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

你不需要完全看懂每一行，但理解基本概念能讓你更好地判斷 AI 的輸出。

-----

## 基本概念解釋

**PRIMARY KEY**：每筆記錄的唯一識別碼。就像每個人的身分證號碼，不會重複。

**NOT NULL**：這個欄位不能是空的。標題不能是空的，所以加 NOT NULL。

**DEFAULT**：沒有填值時的預設值。`status` 預設是 `'draft'`（草稿），`created_at` 預設是當前時間。

**FOREIGN KEY**：關聯到另一個表。`author_id` 關聯到 `users` 表的 `id`，表示「這篇文章是誰寫的」。

-----

## 在 Cloudflare Workers 裡查詢 D1

建立資料表之後，在 Workers 裡這樣操作資料：

```typescript
// 新增一筆商品
await env.DB.prepare(`
  INSERT INTO products (name, price, stock)
  VALUES (?, ?, ?)
`).bind(name, price, stock).run()

// 讀取所有上架商品
const products = await env.DB.prepare(`
  SELECT * FROM products
  WHERE stock > 0
  ORDER BY created_at DESC
  LIMIT 10
`).all()
```

你不需要自己寫這些——告訴 AI「幫我在 D1 查詢所有庫存大於零的商品，按建立時間倒序」，AI 會生成正確的 code。

-----

## 用 AI 查詢資料的常見指令

幾個你可以直接告訴 AI 的需求：

**查詢：**

> 「從 posts 表讀取最新 10 筆已發布文章，包含作者名稱」

**新增：**

> 「在 orders 表新增一筆訂單記錄，欄位有 user_id、amount、status」

**更新：**

> 「把 post id = 5 的 status 改成 published」

**刪除：**

> 「刪除 user_id = 123 的所有草稿文章」

AI 生成的 SQL 記得用參數化查詢（`?` 佔位符），防止 SQL Injection——這在資安篇有說過。

-----

## SQL 速查表

不需要記住所有 SQL 語法，遇到不懂的貼給 AI 問。但這幾個模式你會一直用到：

### 查詢（SELECT）

```sql
-- 取得所有商品
SELECT * FROM products

-- 只取特定欄位
SELECT id, name, price FROM products

-- 加條件篩選
SELECT * FROM products WHERE stock > 0

-- 排序
SELECT * FROM products ORDER BY created_at DESC

-- 限制筆數
SELECT * FROM products LIMIT 10

-- 分頁
SELECT * FROM products LIMIT 10 OFFSET 20

-- 多個條件
SELECT * FROM products WHERE stock > 0 AND price < 500
```

### 新增（INSERT）

```sql
INSERT INTO products (name, price, stock)
VALUES ('薰衣草皂', 280, 50)
```

### 更新（UPDATE）

```sql
-- 更新特定記錄（記得加 WHERE，否則會更新全部）
UPDATE products SET stock = 45 WHERE id = 1

-- 更新多個欄位
UPDATE orders SET status = 'shipped', updated_at = CURRENT_TIMESTAMP WHERE id = 5
```

### 刪除（DELETE）

```sql
-- 刪除特定記錄（記得加 WHERE）
DELETE FROM products WHERE id = 1
```

### 關聯查詢（JOIN）

```sql
-- 取得訂單時同時取得用戶資料
SELECT orders.id, orders.amount, users.email
FROM orders
JOIN users ON orders.user_id = users.id
WHERE orders.status = 'paid'
```

**在 Cloudflare Workers 裡用參數化查詢**（防止 SQL Injection）：

```typescript
// 用 ? 佔位符，不要直接拼字串
const product = await env.DB.prepare(
  'SELECT * FROM products WHERE id = ?'
).bind(productId).first()

const products = await env.DB.prepare(
  'SELECT * FROM products WHERE stock > ? ORDER BY created_at DESC LIMIT ?'
).bind(0, 10).all()
```

-----

## D1 的限制

**免費額度：**

- 儲存：5GB
- 每天讀取：100,000 次
- 每天寫入：50,000 次

對大多數起步的 app 非常夠用。

**D1 不適合的場景：**

- 需要複雜的地理分布寫入（D1 寫入會同步到其他節點，但主寫入點是單一的）
- 需要即時的高並發寫入（每秒數千次寫入）
- 需要複雜的全文搜尋（SQLite 的全文搜尋功能有限）

對 90% 的 indie app 來說，D1 完全夠用。

-----

## 總結

D1 是你 app 的長期記憶，用來儲存用戶、文章、訂單等結構化資料。

對 Vibe Coder 來說，最實用的用法是：

1. 用自然語言告訴 AI 你需要什麼資料表
1. AI 生成 SQL，確認沒問題
1. 貼到 bootstrap SQL 或直接執行
1. 用 `env.DB.prepare()` 查詢，AI 幫你寫查詢語句

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 8 月 1 日漲至 $199。
