# Stripe決済完全ガイド：テストから本番稼働まで

[English](../en/20-stripe-payment-complete-guide-en.md) · [繁中](../zh/20-stripe-payment-complete-guide-zh.md) · [Español](../es/20-stripe-payment-complete-guide-es.md) · [日本語](../jp/20-stripe-payment-complete-guide-jp.md) · [Português (BR)](../pt-br/20-stripe-payment-complete-guide-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約10分

-----

## 決済はアプリで最も重要な機能だ

ユーザーが支払いをしてくれることが、プロダクトに本当の価値があることを意味する。

Stripeは現在最も主流なオンライン決済サービスで、ドキュメントが明確でAPIの設計が優れており、ほぼすべての主要なクレジットカードと決済方法をサポートしている。この記事ではアカウント登録から最初の実際の取引まで、ステップバイステップで進める。

-----

## ステップ1：Stripeアカウントの登録

[stripe.com](https://stripe.com) にアクセスしてアカウントを登録する。

基本情報を入力した後、Stripeは**本人確認（KYC）**を求めてくる：

- 個人または法人の法的情報
- 銀行口座（入金用）
- 本人確認書類

この手続きは通常1〜3営業日かかる。確認が完了するまでは、テストモードでしか決済できない。

**日本在住の方へ：** Stripeは日本での登録をサポートしている。日本語インターフェースで、日本の銀行口座に直接入金できる。

-----

## ステップ2：テストモードと本番モードを理解する

Stripeには完全に分離された2つの環境がある：

**テストモード（Test Mode）**

- APIキーは `sk_test_` で始まる
- すべての取引は偽物で、実際の金銭は動かない
- Stripeが提供するテスト用クレジットカード番号でテストする

**本番モード（Live Mode）**

- APIキーは `sk_live_` で始まる
- 実際の取引で実際のお金が動く

**必ずテストモードで開発・テストを行い、問題がないことを確認してから本番モードに切り替える。**

Stripeのテスト用クレジットカード番号：

```
カード番号：4242 4242 4242 4242
有効期限：未来の任意の日付（例：12/30）
CVV：任意の3桁（例：123）
```

このカードはテストモードで常に成功する。決済失敗をテストする場合：

```
カード番号：4000 0000 0000 0002
→ このカードは常に拒否される
```

-----

## ステップ3：APIキーを取得する

Stripe Dashboard → Developers → API keys で、2種類のキーが表示される：

- **Publishable key**（`pk_test_...`）：フロントエンドに置ける、Stripe.jsの初期化に使う
- **Secret key**（`sk_test_...`）：バックエンドのみ、フロントエンドやGitHubに絶対に出てはいけない

この2つのキーを環境変数に追加する：

```
STRIPE_PUBLISHABLE_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx
```

-----

## ステップ4：Stripeの決済フローを理解する

Stripeの標準決済フローには2種類ある：

### Stripe Checkout（Vibe Coderに推奨）

Stripeが提供するあらかじめ作られた決済ページを使う。Checkout Sessionを作成してユーザーをStripeのページに誘導し、決済完了後にStripeがユーザーをアプリに誘導する。

```
ユーザーが「購入」をクリック
  ↓
バックエンドがCheckout Sessionを作成
  ↓
ユーザーをStripeの決済ページに誘導
  ↓
ユーザーがクレジットカード情報を入力（Stripeのページ、あなたは見えない）
  ↓
決済成功 → StripeがユーザーをSuccess URLに誘導
決済失敗 → StripeがユーザーをCancel URLに誘導
```

メリット：決済フォームを自分で作る必要がない、セキュリティはStripeが担当する。

### Stripe Elements（上級者向け）

Stripeの決済フォームを自分のページに埋め込む。外観を完全にカスタマイズできるが、より多くの設定が必要だ。ほとんどのインディーアプリは最初はCheckoutで十分だ。

-----

## ステップ5：Checkout Sessionの実装

AIに伝える：

```
Cloudflare Workersに POST /api/checkout エンドポイントを作成してください。

機能：
- productIdとuserIdを受け取る
- Stripeを使ってCheckout Sessionを作成する
- 商品情報はD1のproductsテーブルから読み込む
- 決済成功後は /success?session_id={CHECKOUT_SESSION_ID} に誘導
- 決済キャンセル後は /shop に誘導

環境変数：env.STRIPE_SECRET_KEY
```

AIが生成するコードのサンプル：

```typescript
import Stripe from 'stripe'

export async function handleCheckout(request: Request, env: Env) {
  const { productId, userId } = await request.json()

  const product = await env.DB.prepare(
    'SELECT * FROM products WHERE id = ?'
  ).bind(productId).first()

  if (!product) {
    return Response.json({ error: 'Product not found' }, { status: 404 })
  }

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  const session = await stripe.checkout.sessions.create({
    payment_method_types: ['card'],
    line_items: [{
      price_data: {
        currency: 'jpy',
        product_data: { name: product.name },
        unit_amount: product.price, // JPYは整数（¥1000 = 1000）
      },
      quantity: 1,
    }],
    mode: 'payment',
    success_url: `${env.APP_URL}/success?session_id={CHECKOUT_SESSION_ID}`,
    cancel_url: `${env.APP_URL}/shop`,
    metadata: { userId, productId },
  })

  return Response.json({ url: session.url })
}
```

-----

## ステップ6：Webhookの設定（最重要ステップ）

多くの人がこのステップを忘れ、決済が成功したのにアプリが反応しないという状況になる——バックエンドが決済完了を知らないからだ。

**Webhook** はStripeがイベント（決済成功、返金、サブスクリプション更新）が発生したときにあなたのサーバーに送る通知だ。

### Stripe DashboardでWebhookを設定する

Stripe Dashboard → Developers → Webhooks → Add endpoint

WebhookのURLを入力：`https://your-app.com/api/webhook/stripe`

監視するイベントを選択する。最低限：

- `checkout.session.completed`（Checkout決済完了）
- `payment_intent.payment_failed`（決済失敗）

### Webhook Handlerの実装

```typescript
export async function handleStripeWebhook(request: Request, env: Env) {
  const signature = request.headers.get('stripe-signature')
  const body = await request.text()

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  let event
  try {
    // このリクエストが本当にStripeから来たものかを検証する（偽造防止）
    event = stripe.webhooks.constructEvent(
      body,
      signature,
      env.STRIPE_WEBHOOK_SECRET
    )
  } catch (err) {
    return new Response('Webhook signature verification failed', { status: 400 })
  }

  if (event.type === 'checkout.session.completed') {
    const session = event.data.object
    const { userId, productId } = session.metadata

    // データベースに注文記録を作成
    await env.DB.prepare(`
      INSERT INTO orders (user_id, product_id, amount, status, stripe_session_id)
      VALUES (?, ?, ?, 'paid', ?)
    `).bind(userId, productId, session.amount_total, session.id).run()

    // 確認メールを送信（Resend経由）
    // ...
  }

  return new Response('OK', { status: 200 })
}
```

**Webhook Secret** はStripe DashboardでWebhookを設定した後に表示される。環境変数に追加する：

```
STRIPE_WEBHOOK_SECRET=whsec_xxxxx
```

-----

## ステップ7：サンドボックスモードで完全フローをテストする

Stripeのテストモード（サンドボックス）では、実際のお金を動かさずに完全な決済フローをテストできる。

**推奨：Cloudflareの本番環境にデプロイして、サンドボックスモードでテストする。**

理由はシンプルだ：Stripe WebhookにはパブリックなHTTPS URLが必要で、Cloudflare Workersにデプロイすると即座に公開URLが得られる。ローカルでトンネルを設定するより手間がかからない。

### テストフロー

1. **環境変数がテスト用キーになっているか確認する**
   
   Cloudflare Dashboard → Workers → 対象のAPI Worker → Settings → Variables：
   ```
   STRIPE_SECRET_KEY = sk_test_xxxxx
   STRIPE_WEBHOOK_SECRET = whsec_xxxxx（テストモードのWebhook secret）
   ```

2. **Stripe Dashboardでテスト用Webhookを設定する**
   
   Stripe Dashboard → Developers → Webhooks → Add endpoint
   
   本番環境のURLを入力：`https://your-app.workers.dev/api/webhook/stripe`
   
   StripeからWebhook Secretを取得してCloudflare環境変数に更新する。

3. **テストカードで完全なフローを通す**
   
   本番環境のURLを開いて「購入」をクリックし、Stripeの決済ページに進んでテストカード番号を入力：
   ```
   カード番号：4242 4242 4242 4242
   有効期限：任意の未来の日付（例：12/30）
   CVV：任意の3桁（例：123）
   ```

4. **すべての環節を確認する**
   - Stripe Dashboard → Payments：テスト取引が表示されているか
   - Stripe Dashboard → Webhooks：Webhookが正常に送信されたか（200レスポンス）
   - アプリのデータベース：注文レコードが作成されているか
   - ユーザーへの確認メールが届いているか

すべての環節が確認できたら、本番モードに切り替える。

### 失敗シナリオのテスト

成功だけでなく、失敗シナリオもテストする：

```
カード番号：4000 0000 0000 0002
→ 常に決済が拒否される。アプリが失敗ケースを正しく処理するか確認する
```

-----

## ステップ8：本番モードへの切り替え

サンドボックステストが完了したら、本番モードに切り替える：

1. Stripe Dashboardの右上で**Live Mode**に切り替える
2. 本番用APIキー（`sk_live_...`）を取得する
3. WebhooksにLive Mode用のエンドポイントを追加する（同じURL、今度はLive Modeで設定）
4. 本番用のWebhook Secretを取得する
5. Cloudflare WorkersのSecretを更新する：
   
   ```bash
   npx wrangler secret put STRIPE_SECRET_KEY
   # sk_live_xxxxx を入力
   
   npx wrangler secret put STRIPE_WEBHOOK_SECRET
   # 本番用の whsec_xxxxx を入力
   ```

切り替え後、実際のクレジットカードで小額（例：¥100）の取引を行って最終確認する。

-----

## ステップ9：vibefast.appを使えばこれらの設定をスキップできる

vibefast.appを使っている場合、**Stripeの完全なフローはすでに実装済みだ。**

vibefast.appに内蔵されているもの：

- Checkout Sessionの作成ロジック
- Webhook handler（署名検証、冪等性処理を含む）
- 決済成功後の注文レコード自動作成
- 決済確認メール（Resend経由）

上記のコードを自分で実装する必要はない。必要なのは1つだけ：

**Cloudflare Dashboardで環境変数を設定する**

Cloudflare Dashboard → 対象のAPI Worker → Settings → Variables：

```
STRIPE_SECRET_KEY = sk_test_xxxxx（テスト）または sk_live_xxxxx（本番）
STRIPE_WEBHOOK_SECRET = whsec_xxxxx
STRIPE_CURRENCY = jpy（または他の通貨、例：usd、hkd）
STRIPE_PRICE_CENTS = 9900（最小単位で指定。JPYは整数なので¥9900 = 9900）
```

Stripeダッシュボードで事前に商品を作成する必要はない。vibefast.appがCheckout Session作成時にこれらの値を動的に使用する。

設定してデプロイすれば、決済機能は稼働状態だ。

サンドボックスモードで一通りテストして（ステップ7）、問題がなければ本番キーに切り替える。

-----

## よくあるエラー

**Webhook署名検証失敗**  
最も一般的な原因はbodyが解析済みであることだ。Webhook handlerは**生のraw body**を読み込む必要があり、先に`request.json()`を呼んではいけない。そうしないと署名検証が失敗する。

**金額の単位を間違える**  
Stripeの金額の単位は「最小単位」だ。USDの場合は「セント」（$10.00 = 1000）。JPYは整数のため1円=1となる（¥1000 = 1000）。

**Webhookのリトライを処理していない**  
Stripeは200レスポンスを受け取れなかった場合、最大3日間リトライを続ける。Webhook handlerは冪等性（同じイベントを2回処理しても問題ない）を確保すること。データベースで`stripe_session_id`を使って重複チェックができる。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年8月1日より $199 に値上がり。
