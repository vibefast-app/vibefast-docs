# What is a Domain and DNS? What to Do After Buying a Domain

[English](../en/12-domain-and-dns-setup-guide-en.md) · [繁中](../zh/12-domain-and-dns-setup-guide-zh.md) · [Español](../es/12-domain-and-dns-setup-guide-es.md) · [日本語](../jp/12-domain-and-dns-setup-guide-jp.md) · [Português (BR)](../pt-br/12-domain-and-dns-setup-guide-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~7 minutes

-----

## You Bought a Domain. Now What?

A lot of people buy a domain and then don’t know what to do next.

The domain is ready, the app is deployed, but the two aren’t connected — visiting the domain shows a blank page or “site not found.”

This tutorial explains the relationship between domains and DNS, and walks through exactly how to point your domain to Cloudflare Workers.

-----

## What is a Domain?

A **domain** is your app’s address — something like `vibefast.app`.

Your app actually runs at an IP address, like `104.21.45.123`. But nobody wants to memorize numbers, so domains exist — a memorable name that maps to a numeric address.

Domains are purchased from domain registrars (Namecheap, GoDaddy, and Cloudflare Registrar all work). `.app`, `.com`, `.io` are different top-level domains (TLDs) with slightly different pricing and use cases.

-----

## What is DNS?

**DNS (Domain Name System)** is the system that translates domain names into IP addresses.

Think of it as a phone book:

```
vibefast.app → 104.21.45.123
google.com → 142.250.80.46
```

When someone types `vibefast.app` into a browser, here’s what happens:

```
1. Browser asks DNS: what's the IP address for vibefast.app?
2. DNS looks it up and replies: 104.21.45.123
3. Browser connects to that IP and loads the site
```

The whole process happens in milliseconds — you never notice it.

-----

## Types of DNS Records

When configuring DNS, you’ll see several record types:

|Record Type|Purpose                          |Example                                        |
|-----------|---------------------------------|-----------------------------------------------|
|`A`        |Points a domain to an IP address |`vibefast.app → 104.21.45.123`                 |
|`CNAME`    |Points a domain to another domain|`www.vibefast.app → vibefast.app`              |
|`MX`       |Mail server configuration        |Routes email to `@vibefast.app`                |
|`TXT`      |Verification purposes            |Proves domain ownership to Google, Stripe, etc.|

-----

## Connecting Your Domain to Cloudflare Workers

The setup has two parts: **first, move your domain’s DNS management to Cloudflare; second, bind your custom domain inside Workers.**

### Step 1: Move DNS Management to Cloudflare (Recommended)

If you bought your domain through Cloudflare Registrar, DNS is already managed by Cloudflare — skip to Step 2.

If your domain is registered elsewhere (Namecheap, GoDaddy, etc.):

1. Log into [Cloudflare Dashboard](https://dash.cloudflare.com) and click “Add a domain”
1. Enter your domain name and select the free plan
1. Cloudflare automatically scans and imports your existing DNS records — verify nothing is missing
1. Cloudflare gives you two Nameserver addresses, for example:
   
   ```
   aria.ns.cloudflare.com
   bob.ns.cloudflare.com
   ```
1. Go to your domain registrar and replace the current Nameservers with these two
1. Wait for propagation — usually a few minutes to a few hours, up to 48 hours maximum

Once it takes effect, all your DNS settings live inside Cloudflare alongside your Workers, D1, and R2.

### Step 2: Set a Custom Domain in Workers

After DNS is managed by Cloudflare, go into your Worker’s settings to bind the domain:

1. Log into [Cloudflare Dashboard](https://dash.cloudflare.com)
1. Click “Workers & Pages” in the left sidebar
1. Click your **frontend Worker** (in vibefast.app, this is the web worker, not the API worker)
1. Go to “Settings” → “Domains & Routes”
1. Click “Add” → “Custom Domain”
1. Select your domain from the dropdown — it will list domains already registered in your Cloudflare account
1. Click “Add Custom Domain”

Note: your domain must already be in your Cloudflare account (i.e. Step 1 is complete) before it appears in the dropdown.

Cloudflare automatically:

- Adds the corresponding DNS record
- Requests and configures an SSL certificate
- Routes traffic for that domain to your Worker

A few minutes later, open a browser, type your domain, and your app loads.

-----

## How to Verify the Setup

```bash
# Check DNS records for your domain
nslookup vibefast.app

# Or use dig
dig vibefast.app

# Confirm HTTPS certificate
curl -I https://vibefast.app
```

If you see the correct IP or CNAME in the output, DNS has propagated successfully.

-----

## Common Questions

**Q: How long does DNS propagation take?**

In theory up to 48 hours, but in practice usually a few minutes to a few hours. If it’s been more than 2 hours with no change, double-check that all records are filled in correctly.

**Q: Do I need to configure `www.vibefast.app` and `vibefast.app` separately?**

Yes. You need to configure each separately:

- `vibefast.app` (the apex/root domain)
- `www.vibefast.app` (the www subdomain)

Typically you’d point both to Workers, or point `www` to the apex domain with a CNAME, and set up a redirect so users always land on one canonical version.

**Q: Do I need to get an SSL certificate myself?**

No. Once your domain points to Cloudflare, SSL certificates are automatically provisioned and renewed. Your app automatically supports HTTPS — nothing to configure.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on August 1, 2026.
