# R2 vs S3: Why I Don't Use AWS to Store Images

[English](../en/08-cloudflare-r2-vs-aws-s3-en.md) · [繁中](../zh/08-cloudflare-r2-vs-aws-s3-zh.md) · [Español](../es/08-cloudflare-r2-vs-aws-s3-es.md) · [日本語](../jp/08-cloudflare-r2-vs-aws-s3-jp.md) · [Português (BR)](../pt-br/08-cloudflare-r2-vs-aws-s3-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~6 minutes

-----

## Does Your App Need to Store Images?

User avatars, product screenshots, blog cover images—any app with media upload needs a place to store these files.

Databases aren't suitable for storing images (databases are for structured text data), you need a dedicated "object storage" service.

The most common choice is **AWS S3**. But if your app runs on Cloudflare, there's a better option: **Cloudflare R2**.

-----

## S3's Hidden Cost: Egress Fees

AWS S3 pricing has a trap many people fall into: **egress fees**.

- Store images in S3: storage fee (~$0.023/GB/month)
- Users download images: **egress fee (~$0.09/GB)**

Doesn't sound like much? Let's calculate:

Say your app has 1,000 users, each downloads an average of 10 images daily, 500KB each:

```
Daily egress = 1,000 × 10 × 0.5MB = 5,000MB = 5GB
Daily egress cost = 5GB × $0.09 = $0.45
Monthly egress cost = $0.45 × 30 = $13.5
```

The more successful your app, the higher the bill. Some companies only discover S3 egress fees dominate costs after their app goes viral.

-----

## R2's Pricing Logic

Cloudflare R2's pricing strategy is completely different:

|Cost Item|AWS S3     |Cloudflare R2      |
|---------|-----------|-------------------|
|Storage  |$0.023/GB/month|$0.015/GB/month|
|Egress   |$0.09/GB   |**$0 — Completely free**|
|Free tier|Limited    |10GB storage + 1M operations/month|

Zero egress fees make R2's advantage very clear for high-traffic apps. The more your images are downloaded, the bigger the cost difference versus S3.

-----

## Technical Differences Between R2 and S3

|            |AWS S3   |Cloudflare R2         |
|------------|---------|----------------------|
|API compatibility|Native S3 API|S3 API compatible|
|Global distribution|Manual multi-region setup|Automatic global distribution|
|Workers integration|Cross-network calls|Direct Binding, no public internet|
|CORS setup|Manual configuration|Handle through Workers, can be zero CORS|

**S3 API compatibility** is important—most packages supporting S3 (like `@aws-sdk/client-s3`) can directly operate R2, just swap the endpoint URL. This makes migration costs very low.

-----

## Using R2 in Cloudflare Workers

Basic upload and read pattern:

```typescript
// Upload image to R2
await env.R2.put(`images/${filename}`, fileBuffer, {
  httpMetadata: { contentType: file.type }
})

// Return R2 file through Workers
const object = await env.R2.get(`images/${filename}`)
if (!object) return new Response("Not found", { status: 404 })

return new Response(object.body, {
  headers: { "Content-Type": object.httpMetadata?.contentType || "application/octet-stream" }
})
```

Proxying R2 access through Workers (rather than exposing public URLs directly) lets you add any logic in Workers: verify user permissions, track download counts, add cache control, etc. With VibeFast, R2 binding configuration is already handled, just use `env.R2` directly in Workers.

-----

## When to Consider S3?

R2 is better for Cloudflare ecosystem apps in almost every aspect, but a few situations might still need S3:

- You need S3's advanced features (like Intelligent Tiering, Glacier cold storage)
- Your app isn't on Cloudflare, R2's advantages aren't as obvious
- You need deep integration with existing S3-dependent services

For most indie apps running on Cloudflare, R2 is the more natural choice.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
