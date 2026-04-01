# How I Use Cursor to Take a Feature from Idea to Live

[English](../en/15-cursor-workflow-from-idea-to-deploy-en.md) · [繁中](../zh/15-cursor-workflow-from-idea-to-deploy-zh.md) · [Español](../es/15-cursor-workflow-from-idea-to-deploy-es.md) · [日本語](../jp/15-cursor-workflow-from-idea-to-deploy-jp.md) · [Português (BR)](../pt-br/15-cursor-workflow-from-idea-to-deploy-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~10 minutes

-----

## A Real Feature Development Process

I'll use a concrete example to demonstrate: **adding "low stock alert" feature to a handmade soap e-commerce site**—on product detail pages when stock is 3 or below, display "Only X left" in red text.

From idea to live, the whole process took about 20 minutes.

-----

## Step 1: Clearly State Requirements (5 minutes)

The biggest problem many people have using AI tools: requirements aren't stated clearly enough, AI's results keep veering off, after going back and forth several times it's more tiring than writing yourself.

Before starting, think through three things:

**What I want:** On product detail page, when stock <= 3, display "Only X left" above add-to-cart button in small red text.

**Where to change:** Product detail page route file (`/shop/:slug`)

**Logic:** Judge from API-returned `stock` field, only display when <= 3, when 0 display "Sold out" and disable button.

-----

## Step 2: Tell Cursor (3 minutes)

Open Cursor, press `Cmd+L` to open chat, enter:

> "On product detail page (`/shop/:slug`), when product's `stock` field <= 3, display 'Only X left' in small red text above add-to-cart button. When stock is 0, display 'Sold out' and set button to disabled. Use Tailwind CSS."

Cursor sees the entire project structure, knows where this file is, generated code works directly.

-----

## Step 3: Review AI-Generated Code (2 minutes)

Cursor generated something like this:

```typescript
// Stock status logic
const isOutOfStock = product.stock === 0
const isLowStock = product.stock > 0 && product.stock <= 3
```

```tsx
{/* Stock alert */}
{isLowStock && (
  <p className="text-sm text-red-500 mb-2">
    Only {product.stock} left
  </p>
)}
{isOutOfStock && (
  <p className="text-sm text-red-500 mb-2">Sold out</p>
)}

{/* Add to cart button */}
<button
  disabled={isOutOfStock}
  className={`w-full py-3 rounded-lg font-semibold ${
    isOutOfStock
      ? 'bg-zinc-300 text-zinc-500 cursor-not-allowed'
      : 'bg-orange-500 text-white hover:bg-orange-400'
  }`}
>
  {isOutOfStock ? 'Sold out' : 'Add to Cart'}
</button>
```

Looks right, logic clear, no obvious issues.

-----

## Step 4: Test Locally (5 minutes)

```bash
npm run dev
```

Open product detail page, confirm:

- Stock > 3: normal button display, no alert
- Stock <= 3: red "Only X left" appears
- Stock = 0: displays "Sold out", button grayed out and unclickable

Sometimes AI-generated styles differ slightly from expectations. I directly tell Cursor: "Sold out button gray is too light, change to zinc-400," it modifies directly.

-----

## Step 5: Deploy (2 minutes)

```bash
npm run deploy
```

Wait for deployment to complete, open production URL to confirm feature is live.

Entire process: **idea → live, 20 minutes.**

-----

## Habits That Make AI Tools More Effective

From my year-plus of vibe coding experience, a few habits that improve AI output quality:

**State "in which file"**
Don't just say "add a stock alert for me," say "add in product detail page route, file is `shop.$slug.tsx`." Cursor can see the entire project, but giving it a clear starting point makes output more precise.

**Explain "why do it this way"**
"Chinese calculates at 300 characters per minute" is better than "calculate reading time." Background information lets AI make more appropriate choices.

**Change one thing at a time**
Don't say all at once "add reading time, add share button, change title style." Split into three requests, confirm each is okay before continuing. Changing multiple things simultaneously, when problems occur it's hard to pinpoint where.

**Ask about code you don't understand**
"What is this `Math.ceil` doing?" Ask directly in Cursor, it will explain. You don't need to fully understand every line, but should have basic understanding of key logic, otherwise when problems occur later you won't know where to look.

**Test locally before deploying**
Run `npm run dev` locally once, confirm no issues before `npm run deploy`. Discovering problems after deploying to production, rollback is more troublesome.

-----

## What Features Fit This Workflow?

**Very suitable:**

- Adding and modifying UI components
- New API endpoints
- Database queries
- Form validation logic
- Style adjustments

**Need more caution:**

- Logic involving payments (Stripe webhooks, amount calculations)
- Authentication-related modifications
- Database changes affecting existing data structure

For the latter, AI-generated code can be a starting point, but review more carefully, manually test every edge case when necessary.

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price increases to $199 on June 1, 2026.
