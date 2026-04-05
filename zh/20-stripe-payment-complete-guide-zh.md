# Stripe 收款完整指南：從測試到正式上線

[English](../en/20-stripe-payment-complete-guide-en.md) · [繁中](../zh/20-stripe-payment-complete-guide-zh.md) · [Español](../es/20-stripe-payment-complete-guide-es.md) · [日本語](../jp/20-stripe-payment-complete-guide-jp.md) · [Português (BR)](../pt-br/20-stripe-payment-complete-guide-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 10 分鐘

-----

## 收款是你 app 最重要的功能

用戶願意付錢，才代表你的產品真的有價值。

Stripe 是目前最主流的線上收款服務，文件清楚、API 設計好、支援幾乎所有主要信用卡和付款方式。這篇帶你從申請帳號到第一筆真實交易，一步步走完整個流程。

-----

## 第一步：申請 Stripe 帳號

前往 [stripe.com](https://stripe.com) 申請帳號。

填入基本資料之後，Stripe 會要求你完成**身份驗證（KYC）**，包括：

- 個人或公司的法律資訊
- 銀行帳戶（用來收款）
- 身份證明文件

這個過程通常需要 1-3 個工作天。在驗證完成之前，你只能在測試模式下收款，不能處理真實的金錢。

**台灣用戶注意：** Stripe 目前不支援台灣直接申請帳號。有兩條路：

**路線 A：用台灣本地金流**
綠界 ECPay、歐付寶 O’Pay 都支援台灣信用卡和超商付款，適合主要用戶在台灣的產品。缺點是不支援國際信用卡，向外國用戶收款會有困難。

**路線 B：註冊美國公司申請 Stripe**
在美國成立 LLC（可透過 Stripe Atlas 或 Firstbase），就能申請 Stripe。費用大約 $500 一次性，之後每年州稅約 $300。適合打算向全球用戶收款的產品，同時擁有美國銀行帳戶和 Stripe 帳號，是很多台灣 indie hacker 的選擇。

只服務台灣用戶選路線 A，做全球市場選路線 B。

-----

## 第二步：理解測試模式和正式模式

Stripe 有兩個完全分開的環境：

**測試模式（Test Mode）**

- API key 以 `sk_test_` 開頭
- 所有交易都是假的，不會有真實金錢流動
- 用 Stripe 提供的測試信用卡號碼來測試

**正式模式（Live Mode）**

- API key 以 `sk_live_` 開頭
- 真實交易，真實金錢

**永遠先在測試模式下開發和測試，確認沒問題再切換到正式模式。**

Stripe 的測試信用卡號碼：

```
卡號：4242 4242 4242 4242
到期日：任何未來日期（例如 12/30）
CVV：任何三位數字（例如 123）
```

這張卡在測試模式下永遠成功。要測試付款失敗的情況：

```
卡號：4000 0000 0000 0002
→ 這張卡永遠被拒絕
```

-----

## 第三步：取得 API Key

在 Stripe Dashboard → Developers → API keys，你會看到兩組 key：

- **Publishable key**（`pk_test_...`）：可以放在前端，用來初始化 Stripe.js
- **Secret key**（`sk_test_...`）：只能放在後端，絕對不能出現在前端或 GitHub

把這兩個 key 加進你的環境變數：

```
STRIPE_PUBLISHABLE_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx
```

-----

## 第四步：理解 Stripe 的收款流程

Stripe 的標準收款流程分兩種：

### Stripe Checkout（推薦給 Vibe Coder）

Stripe 提供一個預建好的付款頁面，你只需要建立一個 Checkout Session，把用戶導向 Stripe 的頁面，付款完成後 Stripe 把用戶導回你的 app。

```
用戶點「購買」
  ↓
你的後端建立 Checkout Session
  ↓
把用戶導向 Stripe 的付款頁面
  ↓
用戶輸入信用卡資料（在 Stripe 的頁面，你看不到）
  ↓
付款成功 → Stripe 導回你的 success URL
付款失敗 → Stripe 導回你的 cancel URL
```

優點：你不需要自己做付款表單，安全性由 Stripe 負責。

### Stripe Elements（進階）

把 Stripe 的付款表單嵌在你自己的頁面裡，外觀完全可以客製化，但需要更多設定。大多數 indie app 一開始用 Checkout 就夠了。

-----

## 第五步：實作 Checkout Session

告訴 AI：

```
幫我在 Cloudflare Workers 建立一個 POST /api/checkout 端點。

功能：
- 接收 productId 和 userId
- 用 Stripe 建立一個 Checkout Session
- 商品資訊從 D1 的 products 資料表讀取
- 成功付款後導向 /success?session_id={CHECKOUT_SESSION_ID}
- 取消付款後導向 /shop

環境變數：env.STRIPE_SECRET_KEY
```

AI 會生成類似這樣的 code：

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
        currency: 'usd',
        product_data: { name: product.name },
        unit_amount: product.price, // 單位是分，$10 = 1000
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

## 第六步：設定 Webhook（最重要的一步）

很多人忽略這一步，然後發現付款成功了但 app 沒有反應——因為你的後端不知道付款完成了。

**Webhook** 是 Stripe 在事件發生時（付款成功、退款、訂閱更新）主動打到你的伺服器的通知。

### 在 Stripe Dashboard 設定 Webhook

Stripe Dashboard → Developers → Webhooks → Add endpoint

填入你的 Webhook URL：`https://your-app.com/api/webhook/stripe`

選擇要監聽的事件，一般至少要選：

- `checkout.session.completed`（Checkout 付款完成）
- `payment_intent.payment_failed`（付款失敗）

### 實作 Webhook Handler

```typescript
export async function handleStripeWebhook(request: Request, env: Env) {
  const signature = request.headers.get('stripe-signature')
  const body = await request.text()

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  let event
  try {
    // 驗證這個請求真的來自 Stripe，不是偽造的
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

    // 在資料庫記錄訂單
    await env.DB.prepare(`
      INSERT INTO orders (user_id, product_id, amount, status, stripe_session_id)
      VALUES (?, ?, ?, 'paid', ?)
    `).bind(userId, productId, session.amount_total, session.id).run()

    // 發送確認 email（用 Resend）
    // ...
  }

  return new Response('OK', { status: 200 })
}
```

**Webhook Secret** 在 Stripe Dashboard 設定 Webhook 之後會給你，把它加進環境變數：

```
STRIPE_WEBHOOK_SECRET=whsec_xxxxx
```

-----

## 第七步：在沙箱模式下測試完整流程

Stripe 的測試模式（沙箱）讓你在不動用真實金錢的情況下，測試完整的收款流程。

**推薦做法：直接部署到 Cloudflare 正式環境，用沙箱模式測試。**

原因很簡單：Stripe Webhook 需要公開的 HTTPS URL 才能接收通知。Cloudflare Workers 部署完就有公開 URL，比在本地架設 tunnel 省事得多。

### 測試流程

1. **確認環境變數用的是測試 key**
   
   在 Cloudflare Dashboard → Workers → 你的 API Worker → Settings → Variables，確認：
   
   ```
   STRIPE_SECRET_KEY = sk_test_xxxxx
   STRIPE_WEBHOOK_SECRET = whsec_xxxxx（測試模式的 webhook secret）
   ```
1. **在 Stripe Dashboard 設定測試 Webhook**
   
   Stripe Dashboard → Developers → Webhooks → Add endpoint
   
   填入你的正式環境 URL：`https://your-app.workers.dev/api/webhook/stripe`
   
   Stripe 給你一個 Webhook Secret，更新到 Cloudflare 環境變數。
1. **用測試信用卡走一遍完整流程**
   
   打開你的正式環境 URL，點「購買」，進入 Stripe 的付款頁面，輸入測試卡號：
   
   ```
   卡號：4242 4242 4242 4242
   到期日：任何未來日期（例如 12/30）
   CVV：任何三位數字（例如 123）
   ```
1. **確認每個環節都正常**
- Stripe Dashboard → Payments：看到這筆測試交易
- Stripe Dashboard → Webhooks：看到 webhook 成功送達（200 回應）
- 你的 app 資料庫：訂單記錄已建立
- 用戶收到確認 email

所有環節確認沒問題，才切換到正式模式。

### 測試失敗情境

除了成功付款，也要測試失敗情境：

```
卡號：4000 0000 0000 0002
→ 付款永遠被拒絕，確認 app 有正確處理失敗的情況
```

-----

## 第八步：切換到正式模式

沙箱測試沒問題之後，切換到正式模式：

1. 在 Stripe Dashboard 右上角切換到 **Live Mode**
1. 取得正式環境的 API key（`sk_live_...`）
1. 在 Webhooks 新增正式環境的 endpoint（同樣的 URL，但這次在 Live Mode 下設定）
1. 取得正式環境的 Webhook Secret（`whsec_...`）
1. 更新 Cloudflare Workers 的環境變數：
   
   ```bash
   npx wrangler secret put STRIPE_SECRET_KEY
   # 輸入 sk_live_xxxxx
   
   npx wrangler secret put STRIPE_WEBHOOK_SECRET
   # 輸入正式環境的 whsec_xxxxx
   ```

切換完成後，用真實信用卡付一筆小金額（例如 $1）做最終確認。

-----

## 第九步：用 VibeFast 跳過所有這些設定

如果你用的是 VibeFast，好消息是：**Stripe 的完整流程已經做好了。**

VibeFast 內建：

- Checkout Session 的建立邏輯
- Webhook handler（含簽名驗證、冪等性處理）
- 付款成功後自動建立訂單記錄
- 付款確認 email（透過 Resend）

你不需要自己實作任何上面的 code。你只需要做一件事：

**在 Cloudflare Dashboard 設定環境變數**

在 Cloudflare Dashboard → 你的 API Worker → Settings → Variables：

```
STRIPE_SECRET_KEY = sk_test_xxxxx（測試）或 sk_live_xxxxx（正式）
STRIPE_WEBHOOK_SECRET = whsec_xxxxx
STRIPE_CURRENCY = usd（或其他貨幣，例如 hkd、twd）
STRIPE_PRICE_CENTS = 9900（以分為單位，9900 = $99.00）
```

不需要在 Stripe Dashboard 預先建立商品，VibeFast 在建立 Checkout Session 時會直接用這些值動態生成價格。

設定完，部署，收款功能就上線了。

測試用沙箱模式走一遍（第七步），確認沒問題再切換到正式 key。

-----

## 常見錯誤

**Webhook 驗證失敗**
最常見的原因是 body 被解析過了。Webhook handler 必須讀取**原始的 raw body**，不能先 `request.json()`，否則簽名驗證會失敗。

**金額單位搞錯**
Stripe 的金額單位是「分」（cents），不是元。$10.00 要傳 `1000`，不是 `10`。

**沒有處理 Webhook 重試**
Stripe 如果沒有收到 200 回應，會重試最多 3 天。你的 Webhook handler 要確保冪等性（同一個事件處理兩次不會出問題），可以在資料庫用 `stripe_session_id` 做唯一性檢查。

-----

## 總結

Stripe 收款的完整流程：

1. 申請帳號，完成身份驗證
1. 用沙箱模式（測試 key）開發和測試
1. 用 Checkout Session，讓 Stripe 處理付款頁面
1. 設定 Webhook，讓後端知道付款完成了
1. 部署到 Cloudflare，用測試信用卡走完整流程
1. 確認每個環節正常，切換到正式 key

用 VibeFast 的話：設定三個環境變數，收款功能直接上線。

最容易被忽略的是第 4 步——沒有 Webhook，你的 app 不知道誰付了錢。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
