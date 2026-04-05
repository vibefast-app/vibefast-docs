# Case Study: Building an E-Commerce Site with VibeFast

[English](../en/25-ecommerce-case-study-en.md) · [繁中](../zh/25-ecommerce-case-study-zh.md) · [Español](../es/25-ecommerce-case-study-es.md) · [日本語](../jp/25-ecommerce-case-study-jp.md) · [Português (BR)](../pt-br/25-ecommerce-case-study-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~10 minutes

-----

## The Starting Point: You Bought VibeFast and Want to Build a Store

This tutorial uses a concrete scenario to show what it actually looks like: **going from an e-commerce idea to a running product with VibeFast.**

The example: a handmade soap e-commerce site — **SoapCo**. Making soap and selling it directly. No complex inventory system needed. A small catalog — around 20–30 SKUs at any given time.

-----

## How This Differs from the SaaS Case Study

The previous tutorial (23) was a SaaS — users pay a monthly fee to use your tool.

E-commerce is different: **users buy physical products, each transaction is one-time, and you need to manage products, orders, and fulfillment.**

This changes the data structure and business logic. But VibeFast’s foundation — auth, Stripe, Resend — works the same way in both cases.

-----

## Step 1: Clone and Get It Running

Same as the SaaS case — clone the repo, run setup following the quickstart docs, and confirm the full app works locally.

You don’t need to change anything at this step. You’re just confirming the starting point is solid.

-----

## Step 2: Write a Requirements Document

```
I want to build a handmade soap e-commerce site using VibeFast.
The store is called SoapCo. I make the soap and sell it myself.
Small catalog — around 20–30 SKUs.

Core features (MVP):
1. Product listing and detail pages (images, ingredient descriptions, stock status)
2. Shopping cart (add multiple products, adjust quantities)
3. Checkout and payment (Stripe, one-time payment)
4. Automatic order confirmation email
5. Admin panel for managing products and orders

Technical foundation: VibeFast (Auth, Stripe, and Resend already integrated)
Not in this version: loyalty points, product reviews, multi-language, discount codes
```

-----

## Step 3: Design the Database Structure

E-commerce schema is slightly more complex than SaaS. Have AI design it:

```
Don't write any code yet.

Based on these e-commerce requirements, design the D1 database schema for:
- products
- orders
- order_items

What columns does each table need? What are the relationships between them?
Product images will be stored in R2 — the table only needs to store the image URL.
```

AI gives you a draft. Go through it:

- Does `products` have `name`, `slug`, `description`, `price`, `stock`, `image_url`? Does that look right?
- What does `orders` need to record? Should it store a shipping address?
- What order statuses make sense? `pending`, `paid`, `shipped`, `delivered`?

Once confirmed, ask AI to generate the SQL and run it.

-----

## Step 4: Product Images

E-commerce needs product images — the biggest difference from a SaaS build.

The good news: **VibeFast includes a full media library** — image upload, management, and display are all built in. You don’t need to design this flow yourself.

When creating or editing a product in the admin panel, just use VibeFast’s media library to select or upload the image. Storing it to R2, generating a URL, displaying it on the frontend — all handled.

All you need to do is add an `image_url` column to the `products` table and wire it to the existing media library component:

```
Add an image_url column to the products table.
In the product create and edit pages in the admin panel,
connect VibeFast's existing media library component to let the user pick an image.
Save the selected image URL into this column.
```

That’s it. No need to build image upload from scratch.

-----

## Step 5: Cart Design Decision

The shopping cart is the core of any e-commerce site. Two approaches:

**Option A: Store in the browser (localStorage/Cookie)**

- Users can add to cart without logging in
- Simple — no database needed
- Downside: cart is lost if they switch devices

**Option B: Store in the database**

- Requires login to add to cart
- Syncs across devices
- More complex to implement

For a small store like SoapCo, Option A is sufficient. Tell AI:

```
Store the cart in a cookie. No login required.
Format: a JSON array — [{productId, quantity}, ...]
Login or shipping details are only required at checkout.

Don't write any code yet — are there any potential issues with this design?
```

Discuss first, confirm the direction, then implement.

-----

## Step 6: Stripe Checkout

VibeFast already has Stripe integrated. E-commerce checkout is slightly different from a SaaS subscription — this is a one-time payment and the cart may have multiple products.

Tell AI:

```
Implement the checkout flow:

1. User clicks "Checkout" — cart contents are sent to the backend
2. Backend validates that each product has sufficient stock
3. Create a Stripe Checkout Session with all cart items
4. On successful payment:
   - Create records in orders and order_items
   - Deduct stock for each product
   - Send order confirmation email via Resend
5. Handle payment result via webhook

List the plan and which files will be affected before writing any code.
```

-----

## Step 7: Admin Panel

E-commerce needs a simple admin area to manage products and orders.

VibeFast already has the admin panel structure in place. What you need to add:

- Product management (create, edit, publish/unpublish, set stock levels)
- Order list (view all orders and their status)
- Update order status (shipped, delivered)

These are all standard CRUD operations. Ask AI to implement them one by one.

-----

## Step 8: Branding and Design

Replace VibeFast’s default UI with SoapCo’s visual identity.

Visuals matter a lot in e-commerce — especially product photos and overall quality of presentation. It’s worth spending extra time here:

```
Restyle the site to match a natural handmade soap brand:
- Primary colors: warm off-white (stone-50) paired with deep green (green-800)
- Typography feel: elegant, natural
- Product cards: image-forward, minimal text
- Overall tone: handcrafted, natural, quality — avoid over-designed
```

-----

## From Idea to Launch: The Timeline

```
Day 1:      Clone, setup, explore the project structure
Day 2:      Database schema design, image URL field
Days 3–5:   Product pages (listing + detail)
Days 6–7:   Shopping cart logic
Days 8–9:   Stripe checkout + webhook + order email
Day 10:     Admin panel (products + orders)
Days 11–12: Branding, visual polish, detail adjustments
Day 13:     Test every flow end-to-end, fix bugs
Day 14:     Deploy, prepare to launch
```

Two weeks from zero to live. If you had to build auth and Stripe from scratch, those two alone would take over a week. VibeFast skips all of that — the full two weeks goes into the store itself.

-----

## Details That Are Easy to Miss

**Deduct stock after payment is confirmed — not when the item is added to the cart.**
Adding to cart is just intent. If the payment fails or the user abandons checkout, the stock should never have been deducted. Only deduct inside the Stripe webhook’s `checkout.session.completed` handler.

**The order confirmation email needs enough information.**
After paying, users need to see: order number, itemized list, total amount, estimated delivery window, and contact information. This email is the primary trust signal after a purchase.

**Test the full purchase flow — not just the UI.**
Use Stripe’s test card to run through the complete flow: add to cart → checkout → payment → confirm email arrives → verify order appears in admin. Every step needs to check out.

-----

## Key Lessons

**E-commerce is built on trust, not features.**
Clear product photos, detailed descriptions, a smooth payment flow, prompt confirmation emails — these matter far more than whether you have a discount code feature.

**Start with a small catalog and validate the flow.**
On day one, put up only 3–5 products. Confirm the full buying experience works without issues. Expand the catalog once you have confidence.

**Customer service matters more than features.**
After your first customers buy, reach out and ask how the experience was. Real feedback from real customers is worth more than any analytics data.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
