# Why VibeFast Runs Entirely on Cloudflare

[中文](../zh/why-cloudflare-fullstack-zh.md) | [Back to index](./index.md)

**Updated:** March 2026  
**Reading time:** about 6 minutes

-----

## Here’s What Most Templates Look Like

Frontend on Vercel. Database on Supabase or PlanetScale. File storage on AWS S3. Email via SendGrid.

Four vendors. Four billing dashboards. Four sets of free tier limits. Four places things can break.

Each service has its own failure modes. One morning you open your inbox to find Supabase saying you’ve exceeded the free tier connection limit, or Vercel telling you you’ve gone over your bandwidth allowance. Your app is down. And you’re not even sure which layer is the problem.

VibeFast makes a different choice: **run everything on Cloudflare.**

-----

## What “Cloudflare Full-Stack” Actually Means

Every layer of VibeFast runs on Cloudflare:

|Layer       |Service           |Purpose                                  |
|------------|------------------|-----------------------------------------|
|Frontend    |Remix on Workers  |Page rendering, routing, UI              |
|Backend     |Cloudflare Workers|API logic, authentication, business rules|
|Database    |Cloudflare D1     |Users, posts, orders                     |
|File storage|Cloudflare R2     |Images, media, uploads                   |

One vendor. One bill. One dashboard.

-----

## Why This Matters for You

### Speed: Your App Runs Where Your Users Are

Traditional servers live in a fixed location — say, a data center in Virginia. Every time a user in Tokyo clicks a button, the request travels to Virginia and back. That round trip can take 200–300ms.

Cloudflare has **300+ edge locations** worldwide. When you deploy your code, it automatically distributes across every node. Users in Tokyo connect to Tokyo. Users in Frankfurt connect to Frankfurt. Everyone gets the fastest possible response.

And Cloudflare Workers has no cold start problem. Traditional serverless functions can take several seconds to wake up on the first request. Workers are always warm. Response time is nearly imperceptible.

-----

### Zero CORS: Frontend and Backend Talk Internally

This is Cloudflare’s unique architectural advantage, called **Service Binding**.

In a typical setup, the frontend calls the backend over the public internet. That means configuring CORS, managing exposed API keys, and adding a network round trip to every frontend-to-backend call.

VibeFast’s architecture:

```
User → Cloudflare edge node
              ↓ (internal — never touches the public internet)
       Frontend Worker ←→ Backend Worker
                                ↓
                            D1 Database
```

The result:

- **Zero CORS configuration** — the problem is eliminated at the architectural level
- **No exposed API endpoints** — the backend doesn’t need a public URL
- **Faster responses** — one fewer network round trip on every request

For vibe coders, CORS errors are one of the most common sources of wasted debugging time. Service Binding makes the problem structurally impossible.

-----

### Zero Egress Fees: The Real Advantage of R2

If your app handles image uploads, media files, or user avatars, the default choice is AWS S3.

S3 itself isn’t expensive. But **egress fees** are a trap. Every time a user downloads a file, AWS charges you for the outbound bandwidth. The more successful your app becomes, the bigger the bill. Many indie developers discover this problem only after their app starts growing.

Cloudflare R2 works differently: **egress is free**. Users can download files as many times as they want. You pay nothing for outbound traffic.

-----

### Free Tier That’s Actually Enough to Launch

Cloudflare’s free plan is genuinely generous for indie developers:

|Service    |Free Tier                                    |
|-----------|---------------------------------------------|
|Workers    |100K requests per day                        |
|D1 Database|5GB storage + 100K reads / 50K writes per day|
|R2 Storage |10GB + 1M operations per month               |

For most apps at launch, these limits are more than enough. You can test in production from day one, without paying for servers, and upgrade when you have the revenue to justify it.

vibefast.app runs on this exact stack — user registration, dashboard, order system — entirely within the free tier in its early stage.

-----

### One Vendor, Far Less Uncertainty

Multiple vendors introduce a subtler problem beyond complex billing: **the integration points between services are where things break.**

Timeout between Vercel and Supabase? Misconfigured permissions between Supabase and AWS S3? Every integration point is a potential failure point — and when something breaks, you’re hunting through two different vendors’ documentation and status pages to find the cause.

With Cloudflare full-stack, all services run in the same network, officially supported to work together, with documentation in one place. When something goes wrong, there’s one place to look.

-----

## Direct Comparison: Next.js + Vercel vs VibeFast

|                   |Next.js + Vercel         |VibeFast (Cloudflare)  |
|-------------------|-------------------------|-----------------------|
|Cold start         |Occasionally noticeable  |Sub-millisecond, always|
|CORS config        |Required                 |Zero — Service Binding |
|Database           |Separate service required|D1, edge-native        |
|File storage       |Separate service required|R2, zero egress fees   |
|Global edge nodes  |Limited                  |300+ PoPs              |
|Vendor count       |3–4 services             |1 (Cloudflare)         |
|Deploy command     |Multi-step               |`npm run setup`        |
|Free tier to launch|Constrained              |Genuinely sufficient   |

-----

## Edge-Compatible vs. Edge-Native

You may have seen other templates that claim to support “edge deployment.” There’s an important distinction: **edge-compatible** and **edge-native** are not the same thing.

Edge-compatible means your code *can* run at the edge, but the architecture wasn’t designed for it. Next.js App Router has significant limitations in edge runtime — many Node.js APIs don’t work there, and you constantly need to check documentation to see what’s supported.

VibeFast’s entire stack — Workers, D1, R2, Service Binding — was designed from the ground up for Cloudflare’s environment. No compatibility shims. No “this feature isn’t supported in edge runtime” surprises.

-----

## The Bottom Line

Choosing Cloudflare full-stack isn’t just a technical decision. It’s a decision to **reduce complexity**.

One vendor instead of four. Service Binding eliminates CORS. R2 eliminates egress fees. 300+ nodes means every user gets a fast experience. All of this together means you spend more time building your product and less time managing infrastructure.

-----

Want to understand VibeFast’s other core architectural decision?

👉 [Why a Monorepo?](./why-monorepo.md)

Ready to get started?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price goes up to $199 on June 1, 2026.