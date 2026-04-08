# Case Study: Building a SaaS Product with vibefast.app

[English](../en/24-saas-case-study-en.md) · [繁中](../zh/24-saas-case-study-zh.md) · [Español](../es/24-saas-case-study-es.md) · [日本語](../jp/24-saas-case-study-jp.md) · [Português (BR)](../pt-br/24-saas-case-study-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~10 minutes

-----

## The Starting Point: You Bought vibefast.app and Have a SaaS Idea

This tutorial isn’t about vibefast.app’s features — the quickstart docs cover those.

This is about showing a real workflow: **what does it actually look like to go from a SaaS idea to a running product using vibefast.app?**

The example: a SaaS that helps solopreneurs track client invoices — **InvoiceTrack**. Simple feature set: create invoices, track payment status, send automatic reminders before due dates.

-----

## Step 1: Clone and Get It Running First

After purchasing vibefast.app, you’ll receive a collaborator invitation to the private repo. Accept it, then clone:

```bash
git clone https://github.com/vibefast-app/vibefast.git invoicetrack
cd invoicetrack
npm install
```

Follow the quickstart docs to run setup. Within a few minutes you have a fully working app — login, payment flow, admin panel — all running with vibefast.app’s default content.

**Building on top of this is ten times faster than starting from scratch.**

-----

## Step 2: Write a Requirements Document

Before touching any code, have AI help you organize your requirements (the work planning tutorial covers this process in detail):

```
I want to build an invoice management SaaS for solopreneurs using vibefast.app.
The product is called InvoiceTrack.

Core features (MVP):
1. Users can create client records (company name, contact, email)
2. Users can create invoices for clients (line items, amounts, due dates)
3. Track invoice status (draft, sent, paid, overdue)
4. Automatically send reminder emails 3 days before due date
5. Dashboard showing total outstanding balance

Technical foundation: vibefast.app (Auth, Stripe, and Resend already integrated)
Not in this version: PDF export, multi-currency, sub-accounts
```

-----

## Step 3: Design the Database Structure

With the requirements clear, have AI design the tables:

```
Don't write any code yet.

Based on these requirements, design the D1 database schema for:
- clients
- invoices
- invoice_items

What columns does each table need? What are the relationships between them?
```

AI gives you a schema. Go through it line by line — ask about anything unclear, push back on anything you don’t like, and once you’re satisfied, ask AI to generate the SQL to create the tables.

-----

## Step 4: Build One Feature at a Time

With the data structure confirmed, work through it in layers:

**Layer 1 — Foundation**

```
□ Create clients, invoices, and invoice_items tables
□ Confirm vibefast.app's auth protects all new routes
```

**Layer 2 — Core Features**

```
□ Client management (create, edit, list)
□ Invoice creation (select client, add line items, set due date)
□ Invoice status updates
□ Dashboard showing total outstanding balance
□ Due date reminder emails (via Cron Trigger)
```

**Layer 3 — Polish**

```
□ Invoice page design improvements
□ Email template refinements
□ Search and filter functionality
```

Every feature follows the same loop: discuss first → confirm in Plan mode → implement → test locally → commit.

-----

## Step 5: Rebrand

vibefast.app’s default UI is your starting point, not your final product. Swap in InvoiceTrack’s brand:

Tell AI:

```
Replace vibefast.app's default branding with InvoiceTrack.
This includes:
- Site title and meta title
- Logo text (text for now, image later)
- Change primary color from orange to blue (blue-600)
- Hero text on the homepage
```

Rebranding typically takes under an hour.

-----

## Step 6: Configure Stripe Billing

InvoiceTrack’s pricing: $9/month, up to 50 invoices.

vibefast.app already has Stripe integrated. You just need to:

1. Create a $9/month subscription product in Stripe Dashboard
1. Update the Stripe Price ID in your environment variables
1. Tell AI: “Modify the subscription logic so free users can only create 3 invoices, then prompt them to upgrade”

The payment flow itself doesn’t need to be rewritten — vibefast.app’s Stripe integration works out of the box.

-----

## From Idea to First Paying User: The Timeline

How long did this actually take — from clone to a live product with a paying user?

```
Day 1:   Clone, setup, explore the project structure
Days 2–3: Database design, Layer 1 foundation
Days 4–7: Layer 2 core features
Days 8–9: Rebranding, Stripe configuration
Day 10:  Testing, bug fixes, deployment
Day 11:  Launch, start promoting
```

Eleven days from zero to live. Not because of any special skill — because vibefast.app had auth, payments, and deployment already handled. All the time went into InvoiceTrack’s actual product features.

-----

## Key Lessons

**Don’t try to build everything at once.** PDF export, multi-currency support — those are great features, but none of them belong in v1. Ship the smallest version that solves the core problem. Iterate based on real user feedback.

**Auth and payments are the hardest parts.** Without vibefast.app, getting those two right could take two to three weeks. With the template, they’re skipped entirely — all focus goes to the product.

**Commit every day.** Make at least one commit before ending your work session, even if you only finished a small piece. It gives you a safety net to roll back to, and keeps your progress visible.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
