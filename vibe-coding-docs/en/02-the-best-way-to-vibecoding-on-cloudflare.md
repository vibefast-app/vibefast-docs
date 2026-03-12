# The Best Way to Vibe Coding on Cloudflare

[中文](../zh/02-the-best-way-to-vibecoding-on-cloudflare-zh.md) | [Back to index](./index.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Date:** March 7, 2026  
**Reading Time:** ~7 minutes

-----

I’m a 50-year-old solopreneur who’s been vibe coding for over a year.

Last month, I went from idea to a live app in 40 minutes. Not a demo — a real production app running on 300+ global nodes, with user authentication, a database, and a working API.

That experience confirmed something I’d been suspecting for a while: **choosing the right platform matters more than how fast you can write code.**

If you’re into vibe coding, this article is about why the **Cloudflare stack** is the smoothest ride right now — Workers, D1, R2, and Remix, all working together so you can think less about deployment and more about your product.

-----

## Why Not Next.js? Why Not Vercel?

Anyone who’s shipped with traditional cloud setups knows the feeling: the idea is clear, but you spend the whole afternoon fighting CORS errors, database connection strings, and environment variable hell. The actual product doesn’t move.

I’ve been there. When I switched to a fully Cloudflare-native stack, I realized deployment could be quiet — no servers to manage, no CORS to configure, no surprise bandwidth bills. Write the code, run one command, live globally.

-----

## Remix: The Framework ChatGPT and Shopify Both Chose

When most developers think about React frameworks, Next.js comes to mind first. But over the past two years, some of the smartest engineering teams in the world have quietly moved away from it.

**ChatGPT migrated its entire frontend from Next.js to Remix in 2024.** The reasons: simpler routing architecture, more efficient data loading, and faster iteration speed. When a product serving hundreds of millions of users makes that call, it says a lot.

**Shopify did the same.** After migrating their admin system — which has over 1,017 routes — to Remix, page load speeds improved by 30%. For a platform used by millions of merchants every day, that’s not a small number. Shopify was so convinced they acquired the Remix team in 2022.

Why does Remix work so well for vibe coding? Because it’s designed around how humans actually think about web apps. You want to load data — write a loader. You want to handle a form — write an action. There’s no mental overhead from Next.js concepts like App Router vs Pages Router, Server Components, or Client Components. AI tools also generate more accurate Remix code, with fewer hallucinations and fewer errors to fix.

-----

## Cloudflare Workers: Your App Runs in 300+ Places at Once

Traditional servers work like this: your app lives in one city, and every user request has to travel there and back.

Cloudflare Workers is completely different. When you deploy, your code is automatically distributed across 300+ Cloudflare edge locations around the world. Every user gets routed to the nearest node — someone in Tokyo and someone in New York both get a response in milliseconds.

No configuration needed. It just works.

Workers also eliminates the “cold start” problem — that frustrating delay where the first request to a serverless function takes several seconds to wake up. Workers run on V8 isolates, which are always warm and ready. The response time is nearly imperceptible.

My favorite part of the architecture is **Service Binding**: your frontend and backend Workers communicate internally within Cloudflare’s network, never over the public internet. That means zero CORS configuration, no exposed API keys, and no network latency between your frontend and backend. This is where most of those 40 minutes were saved — I used to spend half my time just wiring up the front and back to talk to each other.

-----

## Cloudflare D1: A Database That Lives at the Edge

Databases have always been a friction point in vibe coding. The traditional approach means finding an external database service, dealing with connection strings, configuring network rules, and watching latency numbers that don’t make sense.

Cloudflare D1 simplifies all of this. D1 is a SQLite database that runs on Cloudflare’s edge network — right next to your Workers. Read and write latency is typically under 10ms because the database and your code are in the same place.

For vibe coding, D1 has another big advantage: you can tell an AI to design your database schema, and the SQL it generates works directly. No ORM configuration, no type mapping layers, no translation steps. The AI writes SQL, you run it, it works.

**Free tier:** 5GB storage, 100K reads + 50K writes per day. More than enough to launch and validate your first app.

-----

## Cloudflare R2: Store Media Without the Egress Fees

If your app needs image uploads, video storage, or user avatars, the traditional path leads to AWS S3. Powerful, but the egress fees are a trap — the more successful your app, the bigger the surprise on your next bill.

Cloudflare R2 is S3-compatible storage with **zero egress fees**. You use it exactly like S3, but Cloudflare doesn’t charge you for data going out.

When I built a blog system with VibeFast, I stored all images in R2 and served them through Remix directly from the edge. It felt like local development — instant loads, no configuration — but it was running globally in production.

**Free tier:** 10GB storage, 1 million operations per month.

-----

## The Full Stack, Completely Free to Start

The thing that surprised me most about this stack: **the free tiers across all four services are generous enough to run a real app** without paying for servers on day one.

|Service       |Free Tier                            |
|--------------|-------------------------------------|
|Workers       |100K requests per day                |
|D1 Database   |5GB + 100K reads / 50K writes per day|
|R2 Storage    |10GB + 1M operations per month       |
|Remix Frontend|Runs on free Workers                 |

I built [vibefast.app](https://vibefast.app) entirely on this stack — user registration and login, a live analytics dashboard, an order system — all running on Cloudflare, with zero server costs.

-----

## Want to See It in Action?

Sign up for a free account at [vibefast.app](https://vibefast.app). Once you’re in, you’ll see:

- The site’s **real traffic data from the past 7 days**
- **Your registration number** — which user you are out of everyone who’s signed up

The dashboard you’re looking at is built with the VibeFast template. The auth flow you just went through, the UI, the data — that’s exactly what you get when you buy it.

If you want the complete template — auth, blog, Stripe payments, admin dashboard, D1 migrations, one-command deploy — it’s **$99, lifetime access**, with a private GitHub repo and all future updates included.

-----

The whole point of vibe coding is to think less and build more. Cloudflare lets you stop thinking about infrastructure so you can start thinking about your product.

Questions? Find me on X at [@dankopeng](https://x.com/dankopeng) or drop a comment below.

Next up: **Vibe your first auth system with this stack — from zero to a working login page in under an hour.**

Happy VibeCoding! 🚀