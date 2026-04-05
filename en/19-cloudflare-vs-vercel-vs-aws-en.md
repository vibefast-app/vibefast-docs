# Why I Chose Cloudflare Over Vercel and AWS

[English](../en/19-cloudflare-vs-vercel-vs-aws-en.md) · [繁中](../zh/19-cloudflare-vs-vercel-vs-aws-zh.md) · [Español](../es/19-cloudflare-vs-vercel-vs-aws-es.md) · [日本語](../jp/19-cloudflare-vs-vercel-vs-aws-jp.md) · [Português (BR)](../pt-br/19-cloudflare-vs-vercel-vs-aws-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## This Is Not a Neutral Comparison

There are plenty of “Vercel vs AWS vs Cloudflare: The Complete Comparison” articles out there. Every one of them ends with “it depends on your use case.”

This isn’t that kind of article.

I’m a solopreneur spending my own money and time building products. This is my real decision: **I chose Cloudflare, and I think most Vibe Coders should too.**

If you want a balanced, neutral breakdown, this probably isn’t for you. If you want to know what someone who’s actually using these platforms thinks — keep reading.

-----

## Vercel’s Problem Isn’t the Technology — It’s the Business Model

Vercel’s developer experience is genuinely good. I won’t argue otherwise. Automatic deployments on `git push`, auto-generated preview URLs — these are thoughtful details done well.

But Vercel’s business model is built on one assumption: **the more successful your app, the more you pay.**

Egress bandwidth costs money. Serverless function execution time costs money. Once you exceed the free tier, your bill scales with your traffic.

That’s not inherently wrong — Vercel is a company, it needs revenue. But for indie hackers, it means your cost curve is directly tied to your user growth. The period when your app is just starting to take off is often the same period when you don’t yet have enough revenue to cover it. That’s exactly when the bill starts exploding.

The bigger problem is: **Vercel has no database and no file storage.**

You need to find Supabase for your database, AWS S3 for image storage, Resend for email. Every service you add means another account, another bill, another thing that can break. You think you’re using one platform — you’re actually managing a distributed network of services.

-----

## AWS’s Problem Isn’t the Cost — It’s the Complexity

AWS costs aren’t actually as crazy as some people claim. Compared to what Vercel charges at scale, AWS is often cheaper in practice.

But AWS has a different problem: **it’s designed for engineering teams, not solopreneurs.**

VPCs, Security Groups, IAM Roles, Load Balancers, Auto Scaling Groups — just getting a basic web app running requires understanding a dozen concepts and configuring dozens of options. Any misconfiguration can mean security vulnerabilities, service outages, or inexplicable charges.

AWS’s design philosophy is maximum flexibility. You can control every detail. But most indie apps don’t need that flexibility — what they need is “configure it once and never think about it again.”

AI can generate Terraform configs and CloudFormation templates for you. But you still need to understand what those things are doing, otherwise when something breaks you have no idea where to start.

-----

## What Problem Does Cloudflare Actually Solve?

I didn’t choose Cloudflare because it’s cheapest or because it’s fastest. I chose it because it solves the problem I care about most: **letting me put all my energy into the product, not the infrastructure.**

**One account. One dashboard. One bill.**

Workers runs your code, D1 stores your data, R2 stores your files — all inside Cloudflare. No switching between four or five platforms, no managing four or five sets of API keys. When something breaks, there’s only one place to look.

**Zero egress fees.**

If a million users download images from R2, the egress cost is zero. The more successful your app becomes, the more obvious this advantage gets. Vercel and AWS egress fees add up fast at high traffic.

**Global deployment from day one.**

Cloudflare has 300+ edge nodes. Your code automatically runs closest to each user. A user in Hong Kong connects to a Hong Kong node. No CDN configuration, no multi-region setup — it just works.

**Generous free tier.**

Workers gets 100,000 requests per day free. D1 gets 5 million reads per month free. R2 gets 10GB storage free. For a new app, you might never hit the limits. Scale up when you have the users and revenue to justify it.

-----

## Cloudflare’s Limitations — You Should Know These Too

I’m not writing an advertisement, so here’s what Cloudflare isn’t good for:

**Workers has CPU time limits.** Free plan: 10ms CPU time per request. Paid plan: up to 30 seconds. Video transcoding, heavy data processing, AI model inference — these don’t belong in Workers.

**D1 is SQLite, not PostgreSQL.** If you need complex database features — sophisticated joins, stored procedures, full-text search — D1 might not cut it. Most indie apps never need these, but if your business logic is unusually complex, think it through.

**The ecosystem is relatively new.** D1 is still maturing. Core functionality is stable and production-ready, but if your app has very specific requirements, verify they’re supported before committing.

-----

## One Question to Help You Decide

You don’t need a decision tree. One question is enough:

**Are you building alone, or with a team?**

Building solo: choose Cloudflare. Your time is your most valuable resource. Any choice that reduces the time you spend managing infrastructure is the right choice.

Building with a team: it depends on your stack. If the team is already deep in Next.js, Vercel is fine. If you have complex backend requirements, Cloudflare or AWS both work — it comes down to the team’s technical background.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
