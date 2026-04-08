# ケーススタディ：vibefast.appでEコマースサイトを作る——SoapCo

[English](../en/25-ecommerce-case-study-en.md) · [繁中](../zh/25-ecommerce-case-study-zh.md) · [Español](../es/25-ecommerce-case-study-es.md) · [日本語](../jp/25-ecommerce-case-study-jp.md) · [Português (BR)](../pt-br/25-ecommerce-case-study-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約10分

-----

## SaaSとEコマースの違い

前の記事（InvoiceTrack）ではSaaSプロダクトの構築を見た。この記事ではEコマースサイト「SoapCo」を例に、SaaSとは異なる開発のポイントを紹介する。

主な違いを先に整理しておこう：

| | SaaS（InvoiceTrack） | Eコマース（SoapCo） |
|---|---|---|
| 課金モデル | 月額サブスクリプション | 1回限りの商品購入 |
| データ | ユーザーごとに独立 | 共有商品カタログ + 個別注文 |
| コア体験 | ダッシュボード・業務ツール | 商品ブラウジング・購入フロー |
| Stripe | サブスクリプション | 一度限りの決済 |
| 在庫管理 | 不要 | 必要 |

-----

## プロダクト概要：SoapCo

**コンセプト：** 天然・手作りソープの小規模Eコマースショップ

**ターゲット：** 自然派・健康志向の消費者

**MVPの核心機能：**
- 商品一覧と商品詳細ページ
- ショッピングカート（複数商品・数量指定）
- Stripe Checkoutで一度限りの決済
- 注文完了後の確認メール
- 在庫管理（商品ページに残り在庫数を表示）

**明確に除外：**（MVPでは作らない）
- アカウント登録・ログイン（ゲスト購入のみ）
- 商品レビュー機能
- 割引コード
- 複数の配送オプション

-----

## ステップ1：AIとDBスキーマを設計する

```
先にコードを書かないでください。

天然ソープのEコマースサイト「SoapCo」を作ります。
ゲスト購入のみ（ユーザーログイン不要）。

必要なテーブルを設計してください：
- 商品（カテゴリ付き、在庫管理）
- ショッピングカート（一時保存）
- 注文と注文明細

各テーブルのカラム、型、制約を含めてください。
まだSQLは生成しなくていいです、設計だけ提案してください。
```

AIが提案するスキーマ：

```sql
-- カテゴリテーブル
CREATE TABLE categories (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  name TEXT NOT NULL,
  slug TEXT NOT NULL UNIQUE
);

-- 商品テーブル
CREATE TABLE products (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  category_id INTEGER,
  name TEXT NOT NULL,
  slug TEXT NOT NULL UNIQUE,
  description TEXT,
  price INTEGER NOT NULL,        -- 円（整数）
  stock INTEGER NOT NULL DEFAULT 0,
  image_url TEXT,
  status TEXT NOT NULL DEFAULT 'active',  -- active / inactive
  created_at TEXT DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (category_id) REFERENCES categories(id)
);

-- 注文テーブル
CREATE TABLE orders (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  order_number TEXT NOT NULL UNIQUE,
  customer_email TEXT NOT NULL,
  customer_name TEXT NOT NULL,
  shipping_address TEXT NOT NULL,
  subtotal INTEGER NOT NULL,
  shipping_fee INTEGER NOT NULL DEFAULT 0,
  total INTEGER NOT NULL,
  status TEXT NOT NULL DEFAULT 'pending',  -- pending / paid / shipped / cancelled
  stripe_session_id TEXT,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP,
  paid_at TEXT
);

-- 注文明細テーブル
CREATE TABLE order_items (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  order_id INTEGER NOT NULL,
  product_id INTEGER NOT NULL,
  product_name TEXT NOT NULL,  -- 注文時点の名前を保存
  quantity INTEGER NOT NULL,
  unit_price INTEGER NOT NULL, -- 注文時点の価格を保存
  subtotal INTEGER NOT NULL,
  FOREIGN KEY (order_id) REFERENCES orders(id),
  FOREIGN KEY (product_id) REFERENCES products(id)
);
```

設計のポイント：`order_items`に`product_name`と`unit_price`を保存するのは重要だ。後で商品の名前や価格が変わっても、過去の注文記録が正確に保持される。

-----

## ステップ2：ショッピングカートの設計

EコマースのUI設計で最も重要な判断の一つが「カートをどこに保存するか」だ。

**選択肢とトレードオフ：**

**Cookieベース（vibefast.appの採用方式）**

```
ユーザーのブラウザのCookieにカートデータを保存する
```

メリット：
- サーバーにカートのDBテーブルが不要
- ゲスト購入のみの場合に特に適している
- ブラウザを閉じても残る（Cookie有効期限まで）

デメリット：
- Cookieのサイズ制限（通常4KB）があるため商品数が多いと問題になる
- サーバーサイドでカートを操作できない

**D1（データベース）ベース**

メリット：
- サーバー側でカートを操作できる
- ユーザーがログインすれば複数デバイス間で同期できる

デメリット：
- カートの有効期限管理が必要（古いカートを定期的に削除するなど）
- ゲスト購入の場合、セッションIDの管理が必要

SoapCoはゲスト購入のみで商品数が少ないため、**Cookieベース**を選択する。

AIに実装を依頼：

```
Remix + Cloudflare WorkersでCookieベースのショッピングカートを実装してください。

機能：
- カートに商品を追加（商品ID、数量）
- カートから商品を削除
- 商品数量を変更
- カートの合計金額を計算
- カートの内容をCookieに保存・取得

カートのデータ構造：
{ productId: number, quantity: number }[]

カートの内容は JSON.stringify してCookieに保存、
取得時は JSON.parse して使う。
```

-----

## ステップ3：商品一覧と商品詳細ページ

```
商品一覧ページ（/shop）を実装してください。

- D1からstatus = 'active'の商品を取得
- カテゴリでフィルタリングできる（クエリパラメータ ?category=soap）
- 商品カードグリッド（PC: 3列、モバイル: 1列）
- 各カードに：商品画像、商品名、価格（¥ 記号付き）、在庫切れバッジ（stock = 0の場合）
- 「カートに追加」ボタン（在庫切れ時はdisabled）
```

```
商品詳細ページ（/shop/:slug）を実装してください。

- slugで商品を取得
- 大きな商品画像
- 商品名、価格、説明文
- 数量選択（1〜在庫数）
- 「カートに追加」ボタン
- 在庫数を表示（「残り○個」）
- 存在しない商品は404を返す
```

-----

## ステップ4：Stripe Checkoutで複数商品の決済

SaaSと違い、EコマースのStripe Checkoutは複数の商品を含む。

```
カートの内容を受け取ってStripe Checkout Sessionを作成するAPIを実装してください。

入力：
- cartItems: [{ productId: number, quantity: number }]
- customerEmail: string（任意）
- shippingFee: number（固定 ¥700）

処理：
1. D1から各商品の名前・価格・在庫を確認する
2. 在庫不足の場合はエラーを返す
3. Stripe Checkout Sessionを作成する（line_itemsに各商品を含める）
4. session_idとともにcheckout URLを返す

成功URL：/success?session_id={CHECKOUT_SESSION_ID}
キャンセルURL：/cart
```

AIが生成するコードのサンプル：

```typescript
export async function handleCheckout(request: Request, env: Env) {
  const { cartItems, customerEmail } = await request.json()

  // 在庫確認と商品情報取得
  const lineItems = []
  for (const item of cartItems) {
    const product = await env.DB.prepare(
      'SELECT * FROM products WHERE id = ? AND status = ?'
    ).bind(item.productId, 'active').first()

    if (!product || product.stock < item.quantity) {
      return Response.json(
        { error: `${product?.name || '商品'} の在庫が不足しています` },
        { status: 400 }
      )
    }

    lineItems.push({
      price_data: {
        currency: 'jpy',
        product_data: { name: product.name },
        unit_amount: product.price,
      },
      quantity: item.quantity,
    })
  }

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)
  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: lineItems,
    mode: 'payment',
    customer_email: customerEmail || undefined,
    success_url: `${env.APP_URL}/success?session_id={CHECKOUT_SESSION_ID}`,
    cancel_url: `${env.APP_URL}/cart`,
    metadata: { cartItems: JSON.stringify(cartItems) },
  })

  return Response.json({ url: session.url })
}
```

-----

## ステップ5：Webhookで注文処理

Stripe Webhookで決済完了を受け取り、注文を確定させる：

```
Stripe checkout.session.completed Webhookを処理するハンドラを実装してください。

処理内容：
1. metadataからcartItemsを取得
2. ordersテーブルに注文レコードを作成
3. order_itemsテーブルに各商品の明細を保存
4. 各商品の在庫数を減らす（stock -= quantity）
5. Resendで注文確認メールを顧客に送信

冪等性の処理：stripe_session_idが同じ注文が既に存在する場合はスキップする。
```

-----

## ステップ6：管理画面での注文管理・在庫管理

vibefast.appの管理画面（`/admin`）に追加する：

```
管理画面に以下のページを追加してください：

/admin/orders：注文一覧
- 注文番号、顧客名、注文日、金額、ステータス
- ステータスを「発送済み」に更新するボタン
- 発送済みに変更したら顧客に発送通知メールを送る

/admin/products：商品管理
- 商品一覧（名前、価格、在庫数、ステータス）
- 在庫数の一括更新機能
- 商品の有効化・無効化

/admin/products/new：商品追加
/admin/products/:id/edit：商品編集（R2への画像アップロード含む）
```

-----

## ステップ7：ビジュアルとブランディング

Eコマースはビジュアルが命だ。機能完成後にデザインを仕上げる：

```
SoapCoのブランドデザインを整えてください。

ブランドイメージ：ナチュラル、手作り感、温かみ、信頼
カラーパレット：stone-50（背景）+ green-800（アクセント）+ amber-100（暖色）
フォント：システムフォントスタック

優先的に整えるページ：
1. トップページ（ヒーローセクション + 特集商品）
2. 商品一覧ページ（カードデザイン）
3. カートページ（シンプルで使いやすく）

信頼性を示す要素を含めてください：
- 「天然素材」「手作り」「無添加」などのバッジ
- お客様の声（テキストのみ）
- セキュア決済バッジ（Stripe使用を明示）
```

Eコマースで信頼性の表示が重要な理由：ユーザーはクレジットカード情報を入力する前に、このサイトが信頼できるかを判断する。ブランドの一貫性、安全な決済の明示、お客様の声——これらが購入の決め手になる。

-----

## SaaSとEコマース、共通のベース

SoapCoとInvoiceTrack、異なるプロダクトだが、共通して使ったvibefast.appの機能：

**両方で使ったもの：**
- Cloudflare Workers（バックエンドAPI実行）
- D1データベース
- Stripe決済（モードが異なるだけ）
- Resendメール送信
- 管理画面の基本レイアウト

**SaaSで使ったもの（Eコマースでは不要）：**
- ユーザー認証、JWT
- Stripeサブスクリプション

**Eコマースで使ったもの（SaaSでは不要）：**
- R2画像ストレージ
- Cookieベースのカート管理
- 在庫管理ロジック

vibefast.appはこれらの共通基盤を提供していて、プロダクト固有の部分だけを自分で作ればいい。

-----

## Eコマースで特に注意すること

**在庫の同時更新問題**  
複数のユーザーが同時に同じ商品を最後の1個に向かって購入しようとした場合——これは「レースコンディション」と呼ばれる問題だ。D1のトランザクションを使って適切に処理する：

```typescript
// 在庫確認と予約をトランザクションで処理
await env.DB.batch([
  env.DB.prepare(
    'UPDATE products SET stock = stock - ? WHERE id = ? AND stock >= ?'
  ).bind(quantity, productId, quantity)
])
```

**Webhookの冪等性**  
Stripe Webhookは稀に同じイベントを2回送ることがある。注文処理で`stripe_session_id`の重複チェックを必ず実装する。

**ゲスト購入の後処理**  
ゲスト購入の場合、注文確認メールが顧客との唯一の連絡手段だ。メールの内容を充実させる——注文番号、商品一覧、配送予定、問い合わせ方法。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
