# What Are Environment Variables? Why You Can't Put API Keys in Code

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

## Summary

The core concept of environment variables is simple: **secrets don't belong in code, they belong in the environment.**

Three principles to remember:

1. Add `.env` to `.gitignore`
2. Set production secrets on the platform, not in code
3. If accidentally leaked, immediately revoke and regenerate, no exceptions

-----

👉 [Next: What is an API? Plain English Explanation of Frontend-Backend Communication](./03-what-is-api-frontend-backend-en.md)

Got questions? Find me on X [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, rising to $199 on June 1, 2026.
