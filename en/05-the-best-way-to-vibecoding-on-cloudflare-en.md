# The Best Way to Vibe Code on Cloudflare

[English](../en/05-the-best-way-to-vibecoding-on-cloudflare-en.md) · [繁中](../zh/05-the-best-way-to-vibecoding-on-cloudflare-zh.md) · [Español](../es/05-the-best-way-to-vibecoding-on-cloudflare-es.md) · [日本語](../jp/05-the-best-way-to-vibecoding-on-cloudflare-jp.md) · [Português (BR)](../pt-br/05-the-best-way-to-vibecoding-on-cloudflare-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Date:** March 7, 2026  
**Reading time:** ~7 minutes

-----

As a 50-year-old solopreneur, I've been vibe coding for over a year.

Last month, I went from idea to live production app in 40 minutes. Not a demo—a real app running on 300+ nodes globally, with authentication, database, API, everything.

That experience confirmed something: choosing the right platform matters more than coding speed.

If you're vibe coding, this article explains why the **Cloudflare stack**—Workers, D1, R2, Remix—is currently the smoothest choice. It lets you think less about deployment and more about your product.

-----

## Why Not Next.js? Why Not Vercel?

Anyone who's used traditional cloud solutions knows the feeling: your idea is clear, but you get stuck on environment setup, frontend-backend CORS errors, database connections—and the afternoon is gone.

I walked that path too. After switching to the Cloudflare fullstack, I realized deployment could be this quiet—no server management, no CORS configuration, no worrying about traffic costs exploding. Write code, one command, live globally.

-----

## Remix: The Frontend Framework ChatGPT and Shopify Use

When it comes to frontend frameworks, most people think of Next.js first. But in the past two years, the smartest engineering teams have quietly switched away from it.

**ChatGPT migrated their entire frontend from Next.js to Remix in 2024.** The reason was straightforward: Remix's routing architecture is simpler, data loading is more efficient, development is faster. For a product supporting hundreds of millions of users with daily iterations, this decision speaks volumes.

**Shopify too.** After migrating their massive admin system with 1,017 routes to Remix, page load speeds improved by 30%. For a platform used by millions of merchants daily, 30% is significant.

Shopify acquired Remix in 2022 precisely because of its commitment to web standards and focus on performance.

Why is Remix particularly good for vibe coding? Because its design logic is very close to "plain language"—you want to read data, write a loader; you want to submit a form, write an action. No need to first understand Next.js's pile of App Router, Pages Router, Server Components concepts. AI tools understand Remix more intuitively and generate code with fewer errors.

-----

## Cloudflare Workers: Your App Runs in 300 Places Simultaneously

The traditional server concept: your app runs on a machine in some city, and user requests must travel that distance.

Cloudflare Workers is completely different. After deploying your code, it's automatically replicated to 300+ Cloudflare nodes globally. User requests get routed to the nearest node.

This means users in Taipei and New York both get responses in milliseconds, without you doing any extra configuration.

Workers also don't have the "cold start" problem of traditional servers—that situation where the first request takes several seconds to respond. Workers are always ready, with nearly imperceptible response times.

Another design I love is **Service Binding**: your frontend and backend can communicate directly within Cloudflare's internal network, not over the public internet. This means zero CORS configuration, and API keys aren't exposed externally. That 40-minute login flow completion was mainly saved here—previously, I'd spend most of the time on frontend-backend communication setup.

-----

## D1: Database at the Edge

Databases have always been a sticking point in vibe coding. The traditional approach is finding an external database service, then dealing with connection strings, latency issues, cost calculations...

Cloudflare D1 simplifies this. D1 is a SQLite database, but it runs on Cloudflare's edge network, right next to Workers. Data read/write latency is very low, usually just milliseconds, because the database and your code run in the same place.

For vibe coding, D1's other advantage: you can directly tell AI "help me design a schema to store user data," and the SQL AI generates works immediately, no extra ORM setup or conversion layers needed.

**Free tier**: 5GB storage, 100K reads + 50K writes daily—completely sufficient for most apps' early stages.

-----

## R2: Store Media Files, No Egress Fees

If your app needs to upload images, store videos, handle user avatars, the traditional approach is AWS S3. Great functionality, but egress fees are a trap—the more traffic, the scarier the bill.

Cloudflare R2 is S3-compatible storage, meaning you operate it like S3, but **egress traffic is completely free**.

When I vibed a blog system, all images went in R2, Remix pages pulled directly from the edge—fast enough to feel like local development, but actually globally deployed.

**Free tier**: 10GB storage, 1 million operations monthly.

-----

## Put It All Together, Start for Free

The most surprising thing about this stack: **all the free tiers combined are enough to run a real app**, no need to pay server costs from day one.

|Service      |Free Tier                   |
|------------|----------------------------|
|Workers     |100K requests daily         |
|D1 Database |5GB + 100K reads, 50K writes daily|
|R2 Storage  |10GB + 1M operations monthly|
|Remix Frontend|Runs directly on free Workers|

I built [vibefast.app](https://vibefast.app) with this combination—including user registration/login, dashboard, data analytics, order system. The entire site runs on Cloudflare without paying a cent in server costs.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
