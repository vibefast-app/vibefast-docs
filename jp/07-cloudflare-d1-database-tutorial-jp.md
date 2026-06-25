# Cloudflare D1 データベース入門

[English](../en/07-cloudflare-d1-database-tutorial-en.md) · [繁中](../zh/07-cloudflare-d1-database-tutorial-zh.md) · [Español](../es/07-cloudflare-d1-database-tutorial-es.md) · [日本語](../jp/07-cloudflare-d1-database-tutorial-jp.md) · [Português (BR)](../pt-br/07-cloudflare-d1-database-tutorial-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約10分

-----

## データベースとは何か？

Webアプリを作るとき、データをどこかに保存しなければならない。ユーザーの情報、注文の記録、ブログの記事——これらを保存して取り出す仕組みが**データベース**だ。

データベースはExcelのスプレッドシートに似ている。**テーブル（表）**があり、**カラム（列）**で項目を定義し、**行**にデータが入る。

例えばユーザーテーブルはこんな形になる：

| id | email              | name  | created_at          |
|----|-------------------|-------|---------------------|
| 1  | alice@example.com | Alice | 2026-03-01 10:00:00 |
| 2  | bob@example.com   | Bob   | 2026-03-02 11:00:00 |

-----

## Cloudflare D1とは？

**D1** はCloudflareが提供するデータベースサービスだ。技術的には **SQLite** をベースにしており、Cloudflareのエッジネットワーク上で動作する。

D1の主な特徴：

- **エッジでの実行**：Workersと同じCloudflareインフラ上にあるため、データベースアクセスが非常に速い
- **SQLite互換**：標準的なSQL文が使え、SQLiteのエコシステムをそのまま活用できる
- **サーバーレス**：サーバー管理不要、スケーリングも自動

### D1の無料枠

- ストレージ：5GB
- 1日10万回の読み取りまで無料
- 1日5万回の書き込みまで無料

起動したてのアプリには十分すぎる枠だ。

-----

## AIでスキーマを設計する

データベースの設計（どんなテーブルが必要で、各テーブルにどんなカラムが必要か）は、Vibe Codingのなかで最も「AIと相談」すべき部分の一つだ。

こうAIに伝えよう：

```
先にコードを書かないでください。

手工芸品のEコマースサイトを作りたいです。
以下のテーブルが必要だと思います：
- products（商品）
- orders（注文）
- order_items（注文明細）
- users（ユーザー）

各テーブルにどんなカラムが必要か、テーブル間の関係は何か、
D1向けに設計してください。
```

AIが設計案を出したら、各カラムについて確認する：

- このカラムは本当に必要か？
- 型（TEXT、INTEGER、REAL）は適切か？
- NOT NULLにすべきか、NULLを許可すべきか？
- インデックスが必要なカラムはあるか？

納得できたら、AIにSQL文を生成させてD1で実行する。

-----

## 基本的なSQL操作

D1ではSQLを使ってデータを操作する。Vibe Coderが知っておくべき基本的な4つの操作を説明する。

### SELECT（データを読む）

```sql
-- 全商品を取得
SELECT * FROM products;

-- 条件付きで取得
SELECT * FROM products WHERE status = 'active';

-- 並べ替えと件数制限
SELECT * FROM products 
WHERE status = 'active'
ORDER BY created_at DESC
LIMIT 10;
```

### INSERT（データを追加する）

```sql
-- 新しい商品を追加
INSERT INTO products (name, price, stock, status)
VALUES ('ラベンダーソープ', 1200, 50, 'active');
```

### UPDATE（データを更新する）

```sql
-- 在庫を更新
UPDATE products 
SET stock = stock - 1
WHERE id = 123;

-- 複数カラムを更新
UPDATE orders 
SET status = 'shipped', shipped_at = CURRENT_TIMESTAMP
WHERE id = 456;
```

### DELETE（データを削除する）

```sql
-- 特定の商品を削除
DELETE FROM products WHERE id = 789;

-- 条件付きで削除
DELETE FROM orders WHERE status = 'cancelled' AND created_at < '2025-01-01';
```

-----

## パラメータ化クエリは必須

**絶対にやってはいけないこと**——ユーザーの入力を直接SQLに埋め込む：

```typescript
// ❌ 危険！SQLインジェクション攻撃の対象になる
const name = req.body.name
const query = `SELECT * FROM products WHERE name = '${name}'`
```

攻撃者が`name`に`'; DROP TABLE products; --`を入力すれば、テーブルが削除されてしまう。

**正しい方法**——パラメータ化クエリを使う：

```typescript
// ✅ 安全：パラメータを別途バインドする
const products = await env.DB.prepare(
  'SELECT * FROM products WHERE name = ?'
).bind(name).all()
```

`.bind()`を使うと、D1が自動的に入力値をエスケープしてSQLインジェクションを防ぐ。AIに依頼するときはこのパターンを使うよう明示しておこう。

-----

## JOINで複数テーブルを組み合わせる

実際のアプリでは、複数のテーブルのデータを組み合わせて取得することが多い。

例えば注文と商品名を同時に取得する：

```sql
SELECT 
  orders.id AS order_id,
  orders.total,
  orders.status,
  products.name AS product_name
FROM order_items
JOIN orders ON order_items.order_id = orders.id
JOIN products ON order_items.product_id = products.id
WHERE orders.user_id = ?
ORDER BY orders.created_at DESC
```

このようなJOINを含むクエリはAIに書いてもらうのが最も効率的だ。要件を自然言語で伝えるだけでいい：

```
注文の一覧を取得したいです。
各注文に含まれる商品名も一緒に取得してください。
ユーザーIDで絞り込み、新しい順に並べてください。
```

-----

## WorkersコードからD1を操作する

vibefast.appのバックエンドWorkersから実際にD1を使うパターン：

```typescript
// 全商品の取得
export async function getProducts(env: Env) {
  const result = await env.DB.prepare(
    'SELECT * FROM products WHERE status = ? ORDER BY created_at DESC'
  ).bind('active').all()
  
  return result.results
}

// 商品の作成
export async function createProduct(env: Env, data: ProductInput) {
  const result = await env.DB.prepare(`
    INSERT INTO products (name, description, price, stock, status)
    VALUES (?, ?, ?, ?, ?)
  `).bind(
    data.name,
    data.description,
    data.price,
    data.stock,
    'active'
  ).run()
  
  return result.meta.last_row_id
}
```

AIにこのパターンを見せておくと、D1を使うコードを正確に生成してくれる。

-----

## データベースマイグレーション

アプリが成長するにつれ、テーブルの構造を変える必要が出てくる（新しいカラムを追加するなど）。これを**マイグレーション**と呼ぶ。

vibefast.appではマイグレーションファイルを`migrations/`フォルダに管理する：

```sql
-- migrations/0001_add_description_to_products.sql
ALTER TABLE products ADD COLUMN description TEXT;
```

マイグレーションを実行：

```bash
npx wrangler d1 migrations apply DB
```

データベースの変更は必ずマイグレーションファイルとして管理する。直接テーブルを変更してしまうと、本番環境との差異が生まれて後で混乱する原因になる。

-----

## D1でよくあるエラー

**UNIQUE constraint failed**  
同じ値が一意制約のあるカラムに入ろうとしている。例えば同じメールアドレスで2回登録しようとした場合。先に存在確認をするか、`INSERT OR IGNORE`を使う。

**no such table**  
テーブルが存在しない。マイグレーションを実行し忘れているか、テーブル名のスペルミスの可能性がある。

**SQLITE_BUSY**  
同時に書き込みが多すぎる。D1は基本的に書き込みのロックがある。複数の並行書き込みが必要なユースケースは、D1の制限に注意する。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年8月1日より $199 に値上がり。
