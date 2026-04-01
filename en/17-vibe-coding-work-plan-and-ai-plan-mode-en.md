# How to Create a Vibe Coding Work Plan: From Requirements Analysis to AI Plan Mode

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~10 minutes

-----

## How Most People's Vibe Coding Starts

Have an idea, open Cursor, say "make me an XX app."

AI starts generating, you start modifying, modifying and discover the direction is wrong, tear it down and restart. Generate again, modify again, wrong again.

Three days later, you have a bunch of half-finished features, none complete, also don't know what to do next.

**The problem isn't AI, it's that you have no plan.**

-----

## Why Do Vibe Coders Particularly Need Plans?

Traditional engineers have years of training, know how to break down problems, estimate workload, prioritize.

Vibe Coders' advantage is speed—but speed is also a trap. AI makes you "do anything quickly," so you easily run very fast in the wrong direction.

The plan's purpose isn't to slow you down, it's to ensure your speed is used in the right place.

-----

## Step 1: Write a Requirements Document

Before opening Cursor, first spend 30 minutes writing a requirements document.

The requirements document is the first document of the entire Vibe Coding project—it exists before you write any line of code. This document is the consensus foundation between you and AI, every time you start a new task afterwards, you can paste it to AI, it knows the entire project's background, no need to re-explain every time.

### Don't Know How to Write? Let AI Draft It

If this is your first time making a product, you might not know how to turn vague ideas in your head into a complete document. This is completely normal—just let AI help you draft.

Say you bought the VibeFast template, want to make an e-commerce site selling handmade soap. You have a vague idea in your head, but don't know how to organize it into a document.

Open Claude or ChatGPT, directly state your idea:

```
I want to use the VibeFast template (Remix + Cloudflare fullstack)
to make an e-commerce site selling handmade soap.
I make soap myself, want to sell online, customers can order, pay, receive shipping notifications.
Inventory isn't large, each time about 20–30 items.

Please help me write a requirements document, including:
1. Who are the target users, what are their pain points
2. MVP core features (maximum five)
3. Tech stack (based on VibeFast's existing architecture)
4. What this version won't do
5. Development task list, divided into foundation/core/polish layers

Don't write code yet, just write the document.
```

AI might generate a draft like this:

```
# Handmade Soap E-commerce Requirements Document

## Target Users
- Who they are: Consumers who like natural handmade soap, 25–45 years old, care about ingredients and quality
- Their pain points: Can't find quality-assured, ingredient-transparent handmade soap purchasing channels
- What they want: Simple browsing, secure ordering, know their order status

## MVP Core Features
1. Product list and product detail pages
2. Shopping cart and checkout (Stripe payment)
3. Order management (customers can check order status)
4. Auto-send notification email after shipping
5. Admin backend to manage products and orders

## Tech Stack (Based on VibeFast)
- Frontend/Backend: VibeFast existing architecture (Remix + Cloudflare Workers)
- Database: D1 (products, orders, users)
- Image storage: R2 (product images)
- Payment: Stripe (VibeFast already integrated)
- Email: Resend (VibeFast already integrated)

## This Version Won't Do
- Member points system
- Product review feature
- Multiple languages
- Mobile app

## Task List

### Layer 1 — Foundation
- [ ] Create products table
- [ ] Create orders and order_items tables
- [ ] Set up R2 for product images

### Layer 2 — Core Features
- [ ] Product list and detail pages
- [ ] Shopping cart logic
- [ ] Stripe checkout flow
- [ ] Order confirmation email
- [ ] Admin product and order management

### Layer 3 — Polish
- [ ] Product image lazy loading
- [ ] Order status page optimization
- [ ] SEO meta tags
```

After getting the draft, **read line by line, don't rush to accept**:

- Don't understand something, ask: "What is the order_items table? Why separate from orders?"
- Don't like something, say: "I don't need member system, customers checkout without registration, change to guest checkout"
- Missing something, add: "I want to add inventory management feature, each product should have quantity limit"
- Uncertain about something, discuss: "Should shipping notifications use email or SMS? What are the pros and cons of each?"

Go back and forth several rounds, until you look at this document and feel "yes, this is what I want to build"—only then is it complete.

**This document is yours, not AI's.** AI helps you draft, but every decision needs your understanding and approval. Don't include what you don't understand, change what you don't agree with.

Doesn't need to be as complete as a formal product document, but should cover these questions:

### 1. Who Is This Product For?

```
Target users: Consumers who like natural handmade soap, care about ingredients and quality
Their pain points: Can't find quality-assured, ingredient-transparent handmade soap purchasing channels
What they want: Simple browsing, secure ordering, clearly know order status
```

Clarify who users are, every subsequent feature decision judged by "is this useful to my target users."

### 2. What Are Core Features?

List features your app **must have**, maximum five:

```
Core Features (MVP):
1. Product list and detail pages
2. Shopping cart and checkout (Stripe payment)
3. Order management (customers can check order status)
4. Auto-send notification email after shipping
5. Admin backend to manage products and orders
```

This list is your MVP. Before launch, these five done is enough. Other features you think of, first put in "deal with later" list.

### 3. Tech Stack

VibeFast already decided most tech stack for you, you just need to confirm which services to use:

```
Frontend/Backend: VibeFast existing architecture (Remix + Cloudflare Workers)
Database: D1 (products, orders)
Image storage: R2 (product images)
Payment: Stripe (VibeFast already integrated)
Email: Resend (VibeFast already integrated)
```

Once tech stack is decided, don't switch midway. Switching tech costs a lot, and usually not because of the tech itself, but because you're not familiar yet.

### 4. What Not to Do

This column is as important as "what to do":

```
This version won't do:
- Member points system
- Product review feature
- Multiple languages
- Mobile app
```

Clearly stating what not to do prevents you from constantly adding features during development (scope creep), making MVP never finish.

-----

## Step 2: Break Requirements into Specific Tasks

After requirements document is written, break each feature into specific development tasks.

**Not specific enough tasks:**

```
- Make product pages
- Make checkout
```

**Specific enough tasks:**

```
- Create products table (name, description, price, stock, image_url)
- Create orders and order_items tables
- Implement GET /api/products endpoint (product list)
- Implement GET /api/products/:slug endpoint (product details)
- Implement POST /api/checkout endpoint (create Stripe session)
- Create product list page UI (/shop)
- Create product detail page UI (/shop/:slug)
```

The more specific tasks are, the easier to estimate "how long will this take," also easier to tell AI what to do.

-----

## Step 3: Prioritize

Divide tasks into three layers:

**Layer 1 — Foundation (do first)**  
Without these, other features can't run:

- Database schema
- Auth system
- Basic project structure

**Layer 2 — Core Features (do next)**  
Your MVP main features:

- Payment flow
- Main user pages
- Admin backend

**Layer 3 — Polish (do last)**  
Makes product better, but not essential:

- Animations and micro-interactions
- SEO optimization
- Error page design

Always start from Layer 1. Many people start with Layer 3 (adjusting button colors, adding animations), result is foundation not yet built.

-----

## Step 4: Use AI's Plan Mode

Now you have requirements document and task list, can start using AI.

But don't directly tell AI to start—first use **Plan mode**.

### What Is Plan Mode?

Cursor has a feature called **Plan** (select "Plan" in Chat mode). In Plan mode, you describe what you want to do, AI will:

1. Analyze your requirements
2. List what it plans to do, which files to modify
3. **Wait for your confirmation before executing**

The difference from directly telling AI to start is: you can see AI's "plan" before it does anything, confirm if the direction is right.

### How to Use Plan Mode

In Cursor's Chat panel, switch to **Plan** mode, enter:

```
I want to implement handmade soap e-commerce's product list and detail pages.

Background:
- This is a VibeFast-based e-commerce app (Remix + Cloudflare Workers + D1)
- D1 already has products table, fields: id, name, slug, description, price, stock, image_url
- Product images stored in R2

What to do:
1. GET /api/products — return all in-stock products (stock > 0)
2. GET /api/products/:slug — return single product details
3. Create /shop product list page, display product images, names, prices
4. Create /shop/:slug product detail page, display full info and add-to-cart button

Please first list your plan and which files you'll modify, don't start yet.
```

AI will reply with a plan like this:

```
My plan:

1. Add apps/api/src/routes/products.ts
   - GET /products: query all products with stock > 0
   - GET /products/:slug: query single product

2. Add apps/web/app/routes/shop._index.tsx
   - loader calls products API
   - Display product grid, each card has image, name, price

3. Add apps/web/app/routes/shop.$slug.tsx
   - loader calls products/:slug API
   - Display product full info
   - Add-to-cart button (UI first, cart logic next step)

4. Add apps/web/app/components/ProductCard.tsx
   - Reusable product card component

Confirm this plan is okay?
```

After reviewing and confirming no issues, then say "okay, start implementing."

But "confirm no issues" isn't casually glancing and nodding—this step like requirements document, needs serious attention:

**Don't understand, ask clearly:**

```
You said to add ProductCard component,
what's the relationship between this component and shop._index.tsx?
Why separate, can't write directly in the page?
```

**Have concerns, voice them:**

```
Product images stored in R2, how does your plan get image URLs?
R2 images are private, need to proxy through Workers?
```

**Don't like, request modification:**

```
I don't want to make reusable ProductCard component, this version write directly in page first,
extract later when needed. Please adjust plan.
```

**Confirm impact scope:**

```
How many files will this plan add? Will it modify existing files?
Could it possibly affect existing features?
```

After clarifying all these questions and modifying to your satisfaction, then say "okay, start implementing."

A plan that hasn't started yet, modifying costs zero. A feature already half-implemented when discovering wrong direction, modifying costs potentially a lot.

### Plan Mode's Core Mindset: Plans Are for Review, Not Approval

Many people see AI list a plan, habitually say "good, let's start"—because the plan looks reasonable, and you're eager to see progress.

But plans are listed for you to **review**, not for you to **stamp approval**.

Several times I told AI to directly do it, it modified files I didn't expect, or used an architecture I didn't like, discovered after finishing. Plan mode lets you discover these issues before AI starts—but the premise is you actually carefully read, actually ask questions.

Treat reviewing plans as an independent work session, don't rush. These ten minutes are worth it.

-----

## A Complete Work Rhythm

Putting everything above together, your work rhythm is roughly like this:

**When starting new project (one-time)**

1. Write requirements document (30 minutes)
2. Break into specific tasks (30 minutes)
3. Prioritize Layer 1/2/3 (10 minutes)

**When starting work each day**

1. Look at task list, choose 1–3 tasks to do today
2. For uncertain parts, first discuss with "don't code yet" (refer to previous article)
3. After confirming direction, use Plan mode to have AI list plan
4. Confirm plan, start implementing
5. Test locally, if no issues then commit + push

**Weekly review**

1. Which tasks completed?
2. Which stuck? Why?
3. Any places needing priority adjustment?

-----

## Requirements Document Template

You can directly copy this template to start:

```markdown
# [Project Name] Requirements Document

## Target Users
- Who they are:
- Their pain points:
- What they want:

## Core Features (MVP, maximum five)
1. 
2. 
3. 
4. 
5. 

## Tech Stack
- Frontend:
- Backend:
- Database:
- Payment:
- Email:

## This Version Won't Do
- 
- 
- 

## Task List

### Layer 1 — Foundation
- [ ] 
- [ ] 

### Layer 2 — Core Features
- [ ] 
- [ ] 

### Layer 3 — Polish
- [ ] 
- [ ] 
```

Spending 30 minutes filling this template, you'll save lots of "discover wrong direction halfway through" time later.

-----

## Summary

Vibe Coding's speed advantage needs plans to ensure correct direction:

1. **Write requirements document**—who are users, what to do, what not to do
2. **Break into specific tasks**—more specific the better, convenient to tell AI what to do
3. **Prioritize Layer 1/2/3**—always build foundation first
4. **Use Plan mode**—have AI list plan first, you confirm then implement
5. **Choose 1–3 tasks daily**—small steps fast, every day has completed things

Plans aren't constraints, they ensure your speed is used in the right place.

-----

Got questions? Find me on X [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, rising to $199 on June 1, 2026.
