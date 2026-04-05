# What Are Environment Variables? Why You Can't Put API Keys in Code

[English](../en/09-environment-variables-and-secrets-en.md) · [繁中](../zh/09-environment-variables-and-secrets-zh.md) · [Español](../es/09-environment-variables-and-secrets-es.md) · [日本語](../jp/09-environment-variables-and-secrets-jp.md) · [Português (BR)](../pt-br/09-environment-variables-and-secrets-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~6 minutes

-----

## Start with a Real Disaster

In 2022, a developer pushed code containing an AWS API key to GitHub.

Within four minutes, automated programs scanned and found the key.

Eight hours later, he received a $50,000 AWS bill.

This isn't an isolated case. Thousands of API keys are accidentally exposed on GitHub daily, scanned and abused by automated tools.

-----

## What Are Environment Variables?

Your app needs some "secret information" to function:

- Stripe API key (for payments)
- Resend API key (for sending emails)
- JWT secret (for signing login tokens)
- Database connection strings

These can't be written in code, because code gets pushed to GitHub.

**Environment variables** store this secret information outside of code—in the operating system or deployment platform's "environment." Your code reads these values at runtime, but the values themselves don't exist in code.

-----

## Local Development: .env Files

During local development, you typically create a `.env` file:

```
STRIPE_SECRET_KEY=sk_live_xxxxx
RESEND_API_KEY=re_xxxxx
JWT_SECRET=my-super-secret-key
```

Then read in code like this:

```typescript
// Node.js / Remix
const stripeKey = process.env.STRIPE_SECRET_KEY

// Cloudflare Workers
const stripeKey = env.STRIPE_SECRET_KEY
```

**Critical: `.env` files must NEVER be pushed to GitHub.**

Confirm your `.gitignore` includes this line:

```
.env
.env.local
.env.production
```

-----

## Production Environment: Platform Secrets Management

Local `.env` only works on your own computer. When deploying to production, you need to set environment variables on the platform.

**Cloudflare Workers approach:**

```bash
# Set secret with Wrangler CLI
npx wrangler secret put JWT_SECRET
# After entering the value, Cloudflare encrypts and stores it, won't appear in any logs
```

Or manually set in Cloudflare Dashboard under Workers → Settings → Variables.

-----

## Common Mistakes

### Mistake 1: Pushed .env to GitHub

```bash
# Remedy: remove from git history
git rm --cached .env
git commit -m "remove .env from tracking"
```

But if already pushed, assume the key is leaked, immediately go to the corresponding platform to revoke and regenerate a new key.

### Mistake 2: Hardcoded key in code

```typescript
// ❌ This exposes your key
const stripe = new Stripe("sk_live_xxxxx")

// ✅ Correct approach
const stripe = new Stripe(env.STRIPE_SECRET_KEY)
```

### Mistake 3: console.log .env values

```typescript
// ❌ Logs might be recorded
console.log("Stripe key:", env.STRIPE_SECRET_KEY)

// ✅ Only confirm existence, don't print value
console.log("Stripe key exists:", !!env.STRIPE_SECRET_KEY)
```

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
