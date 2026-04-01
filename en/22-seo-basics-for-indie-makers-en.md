# SEO Basics: Getting Your Product Found on Google

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## SEO Isn’t Mysterious — Most People Just Focus on the Wrong Things

A lot of people overcomplicate SEO. They spend hours researching algorithms, backlink strategies, and keyword density formulas.

For indie hackers, SEO really comes down to two things: **make your pages easy for Google to understand, and make real people want to click on them.**

Do those two things well and the traffic follows. Everything else is advanced optimization — you don’t need to think about it yet.

-----

## Thing One: Make Your Pages Easy for Google to Understand

### Title and Meta Description

The two most important SEO elements on any page:

**Title:** Appears in the browser tab and as the blue link in Google search results.

```html
<title>VibeFast — Cloudflare-Native Web App Template</title>
```

- Keep it between 50–60 characters
- Include your core keyword
- Every page should have a unique title — never reuse the same one

**Meta Description:** The grey text that appears below the title in search results.

```html
<meta name="description" content="Build full-stack apps fast with Remix + Cloudflare Workers. Auth, Stripe, D1, and R2 included. One command to deploy." />
```

- Keep it between 150–160 characters
- Clearly explain what the page is and what value it offers visitors
- Google doesn’t guarantee it will use this text, but it does most of the time

Setting these in Remix:

```typescript
// app/routes/_index.tsx
export function meta() {
  return [
    { title: "VibeFast — Cloudflare-Native Web App Template" },
    { name: "description", content: "Build full-stack apps fast with Remix + Cloudflare Workers." },
  ]
}
```

### Heading Structure (H1, H2, H3)

Every page should have exactly one `<h1>` that describes the page’s topic. Use `<h2>` and `<h3>` to organize the content hierarchy beneath it.

```html
<h1>Handmade Soap Shop</h1>
  <h2>Natural Ingredients</h2>
    <h3>Lavender Collection</h3>
    <h3>Rose Collection</h3>
  <h2>How to Order</h2>
```

Google uses heading structure to understand what a page is about.

### Image Alt Text

Every image needs an `alt` attribute that describes what it shows:

```html
<!-- ❌ No description -->
<img src="soap.jpg" />

<!-- ✅ Clear description -->
<img src="lavender-soap.jpg" alt="Handmade lavender soap with natural essential oils" />
```

Alt text helps Google understand what an image contains. It also lets screen readers describe images to visually impaired users.

-----

## Thing Two: Help Google Discover Your Pages

### Sitemap

A sitemap is an XML file that tells Google “here are all the pages on my site.”

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://yoursite.com/</loc>
    <lastmod>2026-03-01</lastmod>
  </url>
  <url>
    <loc>https://yoursite.com/shop</loc>
    <lastmod>2026-03-01</lastmod>
  </url>
</urlset>
```

Tell AI: “Create a dynamic sitemap in Remix that automatically includes all product pages.” It will generate a sitemap route that reads URLs from your database.

Then go to [Google Search Console](https://search.google.com/search-console) and submit your sitemap URL.

### robots.txt

`robots.txt` tells search engines which pages they can crawl and which to skip:

```
User-agent: *
Allow: /
Disallow: /admin
Disallow: /api

Sitemap: https://yoursite.com/sitemap.xml
```

Place this in `/public/robots.txt` and Remix will serve it automatically.

-----

## Thing Three: Content is the Foundation of Long-Term Traffic

Technical SEO is just the starting point. Over time, **valuable content is what keeps traffic coming.**

This doesn’t mean you need to publish blog posts every day. It means:

Your product pages should clearly answer “what is this, what problem does it solve, and who is it for.”

Your FAQ page should answer the questions your users actually search for, written in their language — not your internal terminology.

If you’re selling handmade soap, potential customers might search for “best natural handmade soap,” “benefits of lavender soap,” or “difference between handmade and commercial soap.” The answers to those questions are your SEO content.

### Using VibeFast’s Built-In Blog to Drive Traffic

The biggest friction with writing SEO content is usually “I’d have to build a blog system first” — database tables, admin editor, frontend display, per-post meta title and description… just getting that infrastructure set up takes significant time.

VibeFast has all of this built in. The included Blog feature covers:

- **Admin article management**: create, edit, publish, and unpublish posts without touching code
- **Markdown editor**: write directly in the admin panel with a live preview
- **Per-post SEO fields**: every article has its own meta title and meta description fields, so you control exactly what Google shows
- **Automatic sitemap**: new posts are automatically added to the sitemap when published, helping Google find them faster

The practical workflow:

1. Ask AI to find long-tail keywords your target users search for (covered in the next section)
1. Ask AI to draft the article content
1. Open VibeFast’s admin, paste into the Markdown editor, refine
1. Fill in the article’s meta title and description
1. Publish

From keyword to live article in under an hour. This is the most efficient content SEO workflow for Vibe Coders.

-----

## Using AI to Find Your Keywords

Tell AI directly:

```
I have an e-commerce site selling natural handmade soap.
My target customers care about ingredients and prefer natural products.

Find 10 long-tail keywords they're likely to search for,
and suggest which page on the site each keyword belongs on.
```

AI’s keyword suggestions:

```
"best natural handmade soap" → homepage or featured products page
"lavender essential oil soap benefits" → lavender collection product page
"difference between handmade and commercial soap" → FAQ or blog
"is handmade soap good for sensitive skin" → FAQ or blog
"how to store handmade soap" → care instructions page
```

Long-tail keywords (longer, more specific search phrases) have lower competition and higher conversion rates. For indie hackers, this is the most practical SEO strategy.

-----

## Technical SEO Checklist

Before going live, confirm:

```
□ Every page has a unique <title> (50–60 characters)
□ Every page has a meta description (150–160 characters)
□ Every page has exactly one <h1>
□ All images have alt text
□ sitemap.xml exists and has been submitted to Google Search Console
□ robots.txt is configured correctly
□ Site uses HTTPS (Cloudflare handles this automatically)
□ Pages load fast (Cloudflare edge nodes handle this automatically)
□ Site displays correctly on mobile
```

-----

## One Thing Not to Do: Don’t Buy Links or Use Black-Hat SEO

Google’s algorithm keeps getting smarter. Black-hat techniques — buying links, keyword stuffing, hidden text — might produce short-term results, but getting penalized means your rankings crater and recovery is slow and painful.

For indie hackers, the most sustainable SEO strategy is: **build something genuinely useful, write clear content, and get the technical basics right.**

Let Google do the rest.

-----

## Summary

SEO comes down to: **make your pages easy for Google to understand, and give real people a reason to click.**

Start with these:

1. Set a unique title and meta description on every page
1. Use a proper heading structure (h1, h2, h3)
1. Submit your sitemap to Google Search Console
1. Use AI to find long-tail keywords your users actually search for, then write content in their language

Once these are in place, the main thing left is patience — SEO typically takes 3–6 months to show meaningful results. But once you rank, it’s a reliable source of free traffic.

-----

Questions? Find me on X at [@dankopeng](https://x.com/dankopeng).  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price increases to $199 on June 1, 2026.