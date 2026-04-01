# Don’t Panic: Using AI to Read Error Messages and Fix Problems

[English](../en/18-debug-and-errors-en.md) · [繁中](../zh/18-debug-and-errors-zh.md) · [Español](../es/18-debug-and-errors-es.md) · [日本語](../jp/18-debug-and-errors-jp.md) · [Português (BR)](../pt-br/18-debug-and-errors-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## When You See a Red Error Message — Don’t Close It

The typical beginner reaction to an error is: panic, close the terminal, try again, same result.

But error messages aren’t your enemy. **They’re your most important clue.** They tell you exactly what went wrong and why. Learn to work with them and you’ll solve problems ten times faster.

More importantly: **you don’t need to understand every error yourself. Just paste it to AI.**

-----

## Three Types of Errors

### Syntax Errors

The code is written incorrectly — a missing bracket, an unclosed quote, a misspelled keyword. These are the easiest to fix because Cursor usually highlights the problem line with a red underline immediately.

```
SyntaxError: Unexpected token '}'
```

Just tell AI: “There’s a syntax error in this code. Find it and fix it.”

### Runtime Errors

The syntax is fine, but the code breaks when it runs. Common examples:

```
TypeError: Cannot read properties of undefined (reading 'name')
```

This usually means you expected a variable to have a value, but it’s actually `undefined` — for example, a database query returned nothing and you tried to access a property on the result.

```
ReferenceError: env is not defined
```

You used a variable that hasn’t been declared or passed into the current scope.

### Logic Errors

The hardest kind to find. The code runs without throwing an error, but the result is wrong — product price calculated incorrectly, stock not properly deducted, user not redirected to the right page after login.

These require tracing the execution of your program to find where the logic went wrong.

-----

## First Checkpoint: Validate AI’s Code with npm run build

After AI writes a piece of code, **don’t rush to run `npm run dev` or deploy. Run `npm run build` first.**

```bash
npm run build
```

`npm run build` does two things:

1. **TypeScript type checking** — catches type errors, undefined variables, incorrect function calls
1. **Compiles the code** — confirms the entire project can be bundled successfully

This is usually faster than starting the dev server, and it catches a lot of problems before you even run the app.

**Only move on when build succeeds:**

```bash
✓ Built in 2.3s   ← this is what you want to see
```

If the build fails, the terminal tells you exactly which file and line has the problem:

```
src/routes/checkout.ts:23:18 - error TS2532: Object is possibly 'undefined'.
```

This is much faster than waiting to discover errors at runtime. Paste the error to AI, let it fix the code, run build again to confirm it passes, then continue.

**Make this a habit: AI writes code → `npm run build` → passes → `npm run dev` to test the feature.**

-----

## The Standard Process When You Hit an Error

### Step 1: Copy the Complete Error Message

Don’t just take a screenshot or note “there was an error.” Copy the full error text, including:

- The error type (TypeError, SyntaxError, etc.)
- The error description
- The file and line number where it occurred (the stack trace)

```
TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)
    at Object.<anonymous> (api/src/index.ts:45:5)
```

This tells you: on line 23 of `checkout.ts`, something is `undefined` and you tried to read its `price` property.

### Step 2: Paste It to AI With Context

```
I'm building a checkout feature in Cloudflare Workers and I hit this error:

TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)

Here's my code:
[paste the relevant code]

What I'm trying to do: after the user clicks "Buy", the backend queries a product from D1,
then creates a Stripe Checkout Session.

Explain what this error means, what's likely causing it, and how to fix it.
```

### Step 3: Understand the Explanation Before Asking for a Fix

After AI explains the issue, make sure you understand what went wrong before asking it to fix the code. Don’t just say “fix it” — ask “why did this happen” first. That way, when you see something similar in the future, you’ll know where to look.

-----

## console.log: The Simplest Debugging Tool

When you’re not sure what value a variable has, the most direct approach is to print it out with `console.log`:

```typescript
export async function handleCheckout(request: Request, env: Env) {
  const { productId } = await request.json()
  
  const product = await env.DB.prepare(
    'SELECT * FROM products WHERE id = ?'
  ).bind(productId).first()
  
  // Add this line to see what product actually contains
  console.log('product:', product)
  
  // If product is null or undefined, the next line will throw an error
  const price = product.price
}
```

### Viewing console.log in Cloudflare Workers

Cloudflare Workers logs don’t appear in the browser’s developer tools. You need to view them through Cloudflare:

**Method 1: Real-time (best during development)**

```bash
npx wrangler tail
```

After running this, all logs from your Worker stream directly to your terminal in real time.

**Method 2: Dashboard**
Cloudflare Dashboard → Workers & Pages → your Worker → Logs

-----

## Advanced AI Debugging Techniques

### Ask AI to Add Debug Logs

```
This function isn't producing the right result. Add console.log statements
at the key steps so I can trace what each value is:

[paste your code]
```

### Ask AI to Explain a Stack Trace

A stack trace shows the chain of function calls that led to the error. It’s hard to read at first — just paste it to AI:

```
Explain what this stack trace is telling me. Where did the error originate?

[paste the full stack trace]
```

### Ask AI to Explain Code You Don’t Understand

When you encounter code that doesn’t make sense, don’t skip it:

```
Explain what this code does in plain language.
I particularly don't understand these lines:

[paste the code]
```

-----

## The Most Common Cloudflare Workers Errors

### `env is not defined` or `env.DB is not defined`

You used `env` somewhere, but the function doesn’t receive `env` as a parameter.

```typescript
// ❌ This function has no access to env
async function getProduct(productId: string) {
  const product = await env.DB.prepare(...) // where does env come from?
}

// ✅ Pass env in explicitly
async function getProduct(productId: string, env: Env) {
  const product = await env.DB.prepare(...)
}
```

### `Cannot read properties of null`

A database query found no matching record and returned `null`, but you accessed a property on it directly.

```typescript
const product = await env.DB.prepare('SELECT * FROM products WHERE id = ?')
  .bind(productId).first()

// ❌ If product is null, this throws an error
const price = product.price

// ✅ Check first
if (!product) {
  return Response.json({ error: 'Product not found' }, { status: 404 })
}
const price = product.price
```

### Deployed Successfully But Feature Isn’t Working

First confirm you’re looking at the latest version — Cloudflare has caching, and sometimes changes take a few seconds to propagate after deployment.

Use curl to verify your API is returning what you expect:

```bash
curl -s https://your-app.com/api/products | jq
```

If that looks fine, run `wrangler tail` and look for error messages in the logs.

-----

## The Most Direct Approach: Take a Screenshot and Show AI

Sometimes describing the problem in text is tedious — “something on the page is in the wrong position,” “the button doesn’t respond when clicked,” “the error appears in a strange place.”

**Just take a screenshot and paste it directly into Cursor’s AI chat.**

Screenshots can show:

- The error screen as it appears in the browser
- Terminal error output (more complete than copied text — colors and formatting are preserved)
- UI results that don’t match what you expected
- Error logs in Cloudflare Dashboard

Tell AI:

```
Here's my problem — screenshot below:
[paste screenshot]

What I expected: [describe]
What I'm actually seeing: [describe]
Help me figure out what's wrong.
```

A screenshot is more accurate than a text description. AI can see exactly what you’re seeing without relying on your translation.

Especially useful for:

- **Layout issues**: a screenshot beats describing “something moved to the wrong side”
- **Terminal errors**: screenshot preserves color highlighting and the full stack trace better than plain text
- **Cloudflare logs**: screenshot the entire log panel so AI can see timestamps and the complete error in context

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price increases to $199 on June 1, 2026.
