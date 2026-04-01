# Security Basics for Vibe Coders: Confirm These Before Going Live

[English](../en/13-security-basics-for-vibe-coders-en.md) · [繁中](../zh/13-security-basics-for-vibe-coders-zh.md) · [Español](../es/13-security-basics-for-vibe-coders-es.md) · [日本語](../jp/13-security-basics-for-vibe-coders-jp.md) · [Português (BR)](../pt-br/13-security-basics-for-vibe-coders-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~10 minutes

-----

## You Don't Need to Be a Security Expert, But You Need to Know These

Many Vibe Coders only start thinking about security after their app goes live. This is normal—when you're using AI to quickly build features, security easily gets pushed to the "deal with later" list.

But a few things aren't "deal with later," they're "must confirm before going live."

This article isn't about turning you into a security engineer, it's about clarifying the most important checkpoints in language you can understand.

-----

## 1. Secret Keys Must NEVER Appear in Code

This is the most common and most serious mistake.

```typescript
// ❌ Wrong: secret key hardcoded in code
const token = jwt.sign(payload, "my-super-secret-key-123")

// ✅ Correct: read from environment variables
const token = jwt.sign(payload, env.JWT_SECRET)
```

Why is this so serious? Because your code usually gets pushed to GitHub. As long as the repo is public, or accidentally set to public one day, anyone can see your secret key, then forge any user's identity and bypass all authentication.

**How VibeFast handles it:** `npm run setup` automatically generates JWT secret and writes it to Cloudflare Workers environment variables. You'll never see the secret key value in code.

**What you need to confirm:**

- `.env` file is added to `.gitignore`
- All secrets (Stripe key, Resend key, JWT secret) are read from `env`, not hardcoded
- Before pushing code to GitHub, search the repo for strings like `sk_live_`, `re_`, `secret`

-----

## 2. Never Trust User Input Directly

Places where your app receives user input—forms, URL parameters, API requests—each is a potential attack vector.

### SQL Injection

If you directly concatenate user input into SQL queries, attackers can inject malicious SQL statements:

```typescript
// ❌ Dangerous: direct string concatenation
const query = `SELECT * FROM users WHERE email = '${userInput}'`
// Attacker inputs: ' OR '1'='1
// Result: SELECT * FROM users WHERE email = '' OR '1'='1'
// This returns all user data

// ✅ Safe: use parameterized queries
const result = await db.prepare(
  "SELECT * FROM users WHERE email = ?"
).bind(userInput).first()
```

D1 supports parameterized queries, all VibeFast database operations use this method. If you write SQL yourself, always use `?` placeholders, never string concatenation.

### XSS (Cross-Site Scripting)

If you directly render user input as HTML, attackers can inject malicious scripts:

```typescript
// ❌ Dangerous
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ Safe: let React/Remix auto-escape
<div>{userInput}</div>
```

Remix auto-escapes by default, as long as you don't use `dangerouslySetInnerHTML`, it's basically safe.

-----

## 3. API Endpoints Must Verify Identity and Permissions

Every API endpoint requiring login must verify the JWT token. This sounds obvious, but many people forget during rapid development.

```typescript
// ❌ Forgot verification
export async function action({ request }: ActionFunctionArgs) {
  // Process directly, didn't confirm if user is logged in
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}

// ✅ Verify identity first
export async function action({ request, context }: ActionFunctionArgs) {
  const user = await requireAuth(request, context.env)
  // Confirm logged in before continuing
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}
```

Going further: **not just verify identity, also verify permissions**. User A shouldn't be able to delete User B's posts, even if both are logged in.

```typescript
// ✅ Verify identity + permissions
const post = await db.prepare(
  "SELECT * FROM posts WHERE id = ?"
).bind(postId).first()

if (post.authorId !== user.userId) {
  throw new Response("Forbidden", { status: 403 })
}
```

-----

## 4. Sensitive Operations Need Rate Limiting

If your login API has no rate limiting, attackers can use programs to automatically try millions of password combinations (brute force).

Cloudflare has a natural advantage here—you can use Cloudflare's Rate Limiting feature to block abnormal request frequencies before traffic enters Workers, no need to implement in code yourself.

Endpoints that should at least be limited:

- `/login`, `/signup`—prevent brute force
- `/api/send-email`—prevent being used to send spam
- Any operation triggering costs (Stripe payments, R2 uploads)

-----

## 5. HTTPS Is Standard, Not Optional

Your app must use HTTPS, not HTTP.

Good news: **apps deployed on Cloudflare automatically enable HTTPS**, no extra configuration needed. Cloudflare handles SSL certificate application and renewal, you don't need to worry at all.

But one thing to confirm: if you have a custom domain, confirm in Cloudflare Dashboard that SSL/TLS setting is Full or Full (Strict), don't use Flexible (in Flexible mode, the Cloudflare to your origin segment is still HTTP, has security risks).

-----

## 6. Error Messages Shouldn't Leak Internal Information

During development you might be used to displaying detailed error messages for debugging convenience:

```typescript
// ❌ Production shouldn't do this
catch (error) {
  return json({ error: error.message, stack: error.stack })
}
```

`error.stack` exposes your code structure, file paths, even function names, providing useful information to attackers.

```typescript
// ✅ Production environment
catch (error) {
  console.error(error) // Only log full error in logs
  return json({ error: "Something went wrong" }, { status: 500 })
}
```

-----

## 7. Regularly Update Dependencies

Your app depends on many third-party packages, these packages occasionally discover security vulnerabilities and release fixes. If you keep using old versions, you'll be exposed to known vulnerabilities.

```bash
# Check which packages have security issues
npm audit

# Auto-fix minor issues
npm audit fix
```

Don't need to do this daily, but at least run `npm audit` once a month, prioritize high/critical severity vulnerabilities.

-----

## Pre-Launch Security Checklist

```
□ All secret keys in environment variables, not hardcoded
□ .env added to .gitignore
□ All SQL queries use parameterized approach
□ All login-required APIs verify JWT
□ Sensitive API endpoints have rate limiting (or use Cloudflare Rate Limiting)
□ Production error messages don't expose stack traces
□ Ran npm audit, no high/critical vulnerabilities
□ Cloudflare SSL/TLS set to Full or Full (Strict)
```

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price increases to $199 on June 1, 2026.
