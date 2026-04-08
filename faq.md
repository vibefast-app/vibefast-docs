# FAQ

[English](./faq.md) · [繁中](./faq-zh.md) · [日本語](./faq-jp.md) · [Español](./faq-es.md) · [Português (BR)](./faq-pt-br.md)

-----

## About the Product

### Is vibefast.app open source?

No. vibefast.app template is a commercial product. This public repository serves as an introduction and learning resource entry point only.

### What’s the difference between the public repo and the private repo?

The public repo contains product introduction and Vibe Coding tutorials. The private repo contains the complete source code and all buyer documentation — delivered after purchase.

### Can I use vibefast.app template for my own product?

Yes — that’s the primary use case. Most buyers either rebrand it directly as their own product, or use it as the foundation for a custom SaaS or web app.

### Do I have to keep all the default modules?

No. vibefast.app template ships as a complete reference product, but you can keep, adjust, or remove modules based on what you need. If you don’t need the blog, for example, you can remove it without affecting anything else.

### Will all technical details be published here publicly?

No. This repo covers public-facing introductions and tutorials only. Full architecture documentation, deployment guides, and the API reference are all inside the private repo.

-----

## About Purchasing

### How do I buy it? How do I get the code after paying?

Go to [vibefast.app](https://vibefast.app) and complete the Stripe checkout. After payment, the system automatically sends a GitHub invitation to your email. Accept the invitation to access the private repo. The entire process is automated and typically completes within a few minutes.

If you haven't received the invitation after 10 minutes, check your spam folder or contact [hello@dankoai.com](mailto:hello@dankoai.com).

### Is the early bird price limited by time or quantity?

Time. The $99 price is available until June 1, 2026, then goes up to $199. Buying now versus buying after June 1 is a $100 difference.

### What does the one-time payment include?

$99 includes: access to the private repo, the complete source code, all buyer documentation, and all future template updates. No monthly fee. No hidden costs.

### What can I see in the live demo before buying?

You can sign up for free and enter the live backend. Inside, you can explore Analytics, Blog, and Media directly. Business and User areas are shown with sample data in limited-access mode, while protected actions remain restricted.

### What’s the refund policy?

Because this is a digital product with immediate source code access upon purchase, refunds are not offered as a general policy. If you run into technical problems after purchasing, contact [hello@dankoai.com](mailto:hello@dankoai.com) first — we’ll do our best to help resolve the issue.

-----

## About the Technology

### How much technical background do I need to use vibefast.app template?

Enough to read and follow the tutorials in this repo. vibefast.app template is designed to be vibe coder-friendly — paired with AI tools like Cursor or Claude, people without deep engineering backgrounds can get it running. If you can run `npm install` and follow what AI-generated code is doing, you have enough.

### Do I need a paid Cloudflare account?

No. Cloudflare’s free tier is more than enough to launch a real app — Workers at 100K requests/day, D1 at 100K reads/day, R2 at 1M operations/month. You can upgrade when your app actually has the traffic to justify it.

### Will vibefast.app template support other stacks in the future?

vibefast.app template is focused on the Cloudflare full-stack (Remix + Workers + D1 + R2) — that’s the core positioning. Supporting other stacks or deployment platforms is not on the current roadmap.

### Can I use vibefast.app template across multiple projects?

Yes. The current license supports multi-project use. You can use it freely across as many personal or client projects as you want — including agency work.

### How is vibefast.app template different from other boilerplates like ShipFast or Supastarter?

vibefast.app template is built natively on Cloudflare's full stack (Remix + Workers + D1 + R2). The key differences: true one-command deploy (`npm run setup`), zero cold starts on the edge, no separate database hosting, and Service Binding between frontend and API with zero CORS overhead. It's not a generic template — it's an opinionated, production-tested architecture.

-----

## About Support

### Who do I contact if I have questions after purchasing?

You can reach us at:

- Email: [hello@dankoai.com](mailto:hello@dankoai.com)
- X: [@dankopeng](https://x.com/dankopeng)

### How often is vibefast.app template updated?

vibefast.app template follows a continuous improvement model. All updates are pushed to the private repo and included in your purchase at no extra cost.

### I want to understand Vibe Coding before buying. Where do I start?

Start here:

- [What Is Vibe Coding?](./en/01-what-is-vibecoding-en.md) — core concepts and key terms
- [Why Cloudflare Is the Best Choice for Vibe Coding](./en/05-the-best-way-to-vibecoding-on-cloudflare-en.md) — practical introduction

### I just bought it. What's the first thing I should do?

Follow the [Quickstart Guide](./quickstart.md) — you'll go from clone to a live app in under 10 minutes.

-----

General questions? Open an issue on [GitHub](https://github.com/vibefast-app/vibefast-docs/issues).  
Technical issues after purchase? Open an issue in the private repo or email [hello@dankoai.com](mailto:hello@dankoai.com).  
Or reach out directly at [@dankopeng](https://x.com/dankopeng).
