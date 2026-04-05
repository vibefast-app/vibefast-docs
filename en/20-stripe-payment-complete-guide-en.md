# Stripe Payments: From Sandbox Testing to Going Live

[English](../en/20-stripe-payment-complete-guide-en.md) · [繁中](../zh/20-stripe-payment-complete-guide-zh.md) · [Español](../es/20-stripe-payment-complete-guide-es.md) · [日本語](../jp/20-stripe-payment-complete-guide-jp.md) · [Português (BR)](../pt-br/20-stripe-payment-complete-guide-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~10 minutes

-----

## Payments Are Your App’s Most Important Feature

When users pay, it means your product is genuinely valuable.

Stripe is the most widely used online payment service today — clear documentation, well-designed API, and support for virtually all major credit cards and payment methods. This guide walks you through the full flow, from creating an account to your first real transaction.

-----

## Step 1: Create a Stripe Account

Go to [stripe.com](https://stripe.com) and sign up.

After entering your basic information, Stripe will require **identity verification (KYC)**, including:

- Personal or business legal information
- A bank account (for receiving payouts)
- Identity documents

This process usually takes 1–3 business days. Until verification is complete, you can only use test mode — no real money can be processed.

**Note for users outside supported countries:** Stripe is not available in all countries. If Stripe isn’t available where you are, you have two options:

**Option A: Use a local payment processor**
Look for payment providers that support your region. The downside is typically limited support for international cards, which makes it harder to sell to customers abroad.

**Option B: Register a US company and apply for Stripe**
Incorporating a US LLC (through services like Stripe Atlas or Firstbase) lets you apply for Stripe. One-time cost is roughly $500, with annual state fees around $300. This is a common path for indie hackers who want to accept global payments and comes with a US bank account as well.

If you’re only serving local customers, Option A is simpler. If you’re building for a global market, Option B is worth considering.

-----

## Step 2: Understand Test Mode and Live Mode

Stripe has two completely separate environments:

**Test Mode**

- API keys start with `sk_test_`
- All transactions are simulated — no real money moves
- Use Stripe’s test card numbers to run through flows

**Live Mode**

- API keys start with `sk_live_`
- Real transactions, real money

**Always develop and test in test mode first. Only switch to live mode after everything is confirmed working.**

Stripe test card numbers:

```
Card number: 4242 4242 4242 4242
Expiry: any future date (e.g. 12/30)
CVV: any three digits (e.g. 123)
```

This card always succeeds in test mode. To test a declined payment:

```
Card number: 4000 0000 0000 0002
→ This card is always declined
```

-----

## Step 3: Get Your API Keys

In Stripe Dashboard → Developers → API keys, you’ll find two keys:

- **Publishable key** (`pk_test_...`): safe to use on the frontend to initialize Stripe.js
- **Secret key** (`sk_test_...`): backend only — never expose this in frontend code or GitHub

Add both to your environment variables:

```
STRIPE_PUBLISHABLE_KEY=pk_test_xxxxx
STRIPE_SECRET_KEY=sk_test_xxxxx
```

-----

## Step 4: Understand How Stripe Checkout Works

Stripe offers two approaches for accepting payments:

### Stripe Checkout (Recommended for Vibe Coders)

Stripe provides a pre-built payment page. You create a Checkout Session, redirect the user to Stripe’s hosted page, and Stripe redirects them back to your app when the payment is complete.

```
User clicks "Buy"
  ↓
Your backend creates a Checkout Session
  ↓
User is redirected to Stripe's payment page
  ↓
User enters card details (on Stripe's page — you never see them)
  ↓
Payment succeeds → Stripe redirects to your success URL
Payment fails → Stripe redirects to your cancel URL
```

The advantage: you don’t build a payment form, and Stripe handles all the security.

### Stripe Elements (Advanced)

Embeds Stripe’s payment form directly inside your own page — fully customizable appearance, but more setup required. Checkout is sufficient for most indie apps starting out.

-----

## Step 5: Implement the Checkout Session

Tell AI:

```
Create a POST /api/checkout endpoint in Cloudflare Workers.

Requirements:
- Accept productId and userId
- Create a Stripe Checkout Session
- Read product info from the D1 products table
- On success, redirect to /success?session_id={CHECKOUT_SESSION_ID}
- On cancel, redirect to /shop

Environment variable: env.STRIPE_SECRET_KEY
```

AI will generate something like this:

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
        unit_amount: product.price, // in cents — $10.00 = 1000
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

## Step 6: Set Up Webhooks (The Most Important Step)

A lot of people skip this and then wonder why their app doesn’t respond after a successful payment — it’s because the backend never found out the payment happened.

**Webhooks** are notifications Stripe sends to your server when events occur (payment completed, refund issued, subscription updated).

### Add a Webhook Endpoint in Stripe Dashboard

Stripe Dashboard → Developers → Webhooks → Add endpoint

Enter your webhook URL: `https://your-app.com/api/webhook/stripe`

Select the events to listen for — at minimum:

- `checkout.session.completed` (payment completed)
- `payment_intent.payment_failed` (payment declined)

### Implement the Webhook Handler

```typescript
export async function handleStripeWebhook(request: Request, env: Env) {
  const signature = request.headers.get('stripe-signature')
  const body = await request.text()

  const stripe = new Stripe(env.STRIPE_SECRET_KEY)

  let event
  try {
    // Verify this request actually came from Stripe, not a spoofed request
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

    // Record the order in the database
    await env.DB.prepare(`
      INSERT INTO orders (user_id, product_id, amount, status, stripe_session_id)
      VALUES (?, ?, ?, 'paid', ?)
    `).bind(userId, productId, session.amount_total, session.id).run()

    // Send confirmation email via Resend
    // ...
  }

  return new Response('OK', { status: 200 })
}
```

After setting up the webhook in Stripe Dashboard, you’ll receive a **Webhook Secret**. Add it to your environment variables:

```
STRIPE_WEBHOOK_SECRET=whsec_xxxxx
```

-----

## Step 7: Test the Full Flow in Sandbox Mode

Stripe’s test mode (sandbox) lets you test the complete payment flow without moving any real money.

**Recommended approach: deploy to Cloudflare production and test with sandbox keys.**

The reason is straightforward: Stripe Webhooks need a publicly accessible HTTPS URL. Cloudflare Workers gives you one the moment you deploy — far simpler than setting up a local tunnel.

### Testing Flow

1. **Confirm your environment variables use test keys**
   
   In Cloudflare Dashboard → Workers → your API Worker → Settings → Variables, verify:
   
   ```
   STRIPE_SECRET_KEY = sk_test_xxxxx
   STRIPE_WEBHOOK_SECRET = whsec_xxxxx (test mode webhook secret)
   ```
1. **Add a test webhook in Stripe Dashboard**
   
   Stripe Dashboard → Developers → Webhooks → Add endpoint
   
   Enter your production URL: `https://your-app.workers.dev/api/webhook/stripe`
   
   Copy the Webhook Secret Stripe gives you and update it in Cloudflare.
1. **Run through the full payment flow with a test card**
   
   Open your production URL, click “Buy”, and on Stripe’s payment page enter:
   
   ```
   Card number: 4242 4242 4242 4242
   Expiry: any future date (e.g. 12/30)
   CVV: any three digits (e.g. 123)
   ```
1. **Verify every step completed correctly**
- Stripe Dashboard → Payments: the test transaction appears
- Stripe Dashboard → Webhooks: webhook delivered successfully (200 response)
- Your app’s database: order record was created
- User received a confirmation email

Only move to live mode after every step checks out.

### Test the Failure Path Too

```
Card number: 4000 0000 0000 0002
→ Always declined — verify your app handles payment failures correctly
```

-----

## Step 8: Switch to Live Mode

Once sandbox testing passes, switch to live mode:

1. Toggle to **Live Mode** in the top-right corner of Stripe Dashboard
1. Get your live API key (`sk_live_...`)
1. Add a new webhook endpoint under Live Mode (same URL, but registered under Live Mode)
1. Get the live Webhook Secret (`whsec_...`)
1. Update your Cloudflare Workers environment variables:
   
   ```bash
   npx wrangler secret put STRIPE_SECRET_KEY
   # Enter sk_live_xxxxx
   
   npx wrangler secret put STRIPE_WEBHOOK_SECRET
   # Enter the live whsec_xxxxx
   ```

After switching, make one real payment with a small amount (e.g. $1) to confirm everything works end-to-end.

-----

## Step 9: Using VibeFast — Skip All of This

If you’re using VibeFast, the good news is: **the entire Stripe flow is already built.**

VibeFast includes:

- Checkout Session creation logic
- Webhook handler (with signature verification and idempotency handling)
- Automatic order record creation on successful payment
- Payment confirmation email via Resend

You don’t need to implement any of the code above. You just need to do one thing:

**Set your environment variables in Cloudflare Dashboard**

Go to Cloudflare Dashboard → your API Worker → Settings → Variables:

```
STRIPE_SECRET_KEY = sk_test_xxxxx (test) or sk_live_xxxxx (live)
STRIPE_WEBHOOK_SECRET = whsec_xxxxx
STRIPE_CURRENCY = usd (or hkd, eur, etc.)
STRIPE_PRICE_CENTS = 9900 (in cents — 9900 = $99.00)
```

No need to create a product in Stripe Dashboard first. VibeFast generates the price dynamically when creating the Checkout Session.

Set the variables, deploy, and payments are live.

Run through the sandbox flow (Step 7) to confirm everything works, then swap in your live keys.

-----

## Common Mistakes

**Webhook signature verification fails**
The most common cause is reading the body as parsed JSON before passing it to the webhook handler. The handler must read the **raw body text** — if you call `request.json()` first, the signature check will fail.

**Getting the amount unit wrong**
Stripe uses cents, not dollars. $10.00 should be passed as `1000`, not `10`.

**Not handling webhook retries**
If Stripe doesn’t receive a 200 response, it retries for up to 3 days. Make your webhook handler idempotent — processing the same event twice should have no side effects. Use `stripe_session_id` as a unique key in your database to prevent duplicate orders.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
