# Changelog

[中文](../zh/changelog-zh.md) | [Back to index](./index.md)

All notable version updates are recorded here. Format follows [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

-----

## [v1.0.0] — March 2026

### Initial Release 🎉

VibeFast is now available for purchase. This is the first public version — and the version that [vibefast.app](https://vibefast.app) itself runs on.

**Core Architecture**

- Remix frontend running on Cloudflare Workers
- Cloudflare Workers API (TypeScript)
- Cloudflare D1 database with auto schema on first deploy
- Cloudflare R2 object storage
- Service Binding — zero CORS internal communication between frontend and backend
- Bearer JWT authentication with auto-generated secret
- Turborepo monorepo — frontend, API, and shared packages in one repo

**Built-in Modules**

- User auth — registration, login, logout, protected routes
- Admin dashboard — manage users, content, and orders
- Blog CMS — create, edit, and publish posts with categories
- Media library — upload and manage files via R2
- Stripe one-time payments — digital product checkout with pre-configured webhook
- Resend transactional email — purchase confirmation and admin notification
- Built-in pageview analytics — no third-party service required
- One-command setup script — `npm run setup` handles all initialization and deployment

**Buyer Documentation**

- Quickstart guide
- Rebrand guide
- Required secrets reference
- Stripe & Resend setup guide
- Module removal guide
- Deployment guide
- API reference

-----

*Changelog is actively maintained. Every version update will be recorded here.*

*Purchase VibeFast to receive all future updates: [vibefast.app](https://vibefast.app)*