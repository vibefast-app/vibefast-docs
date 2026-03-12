# Why VibeFast Uses a Monorepo

[中文](../zh/why-monorepo-zh.md) | [Back to index](./index.md)

**Updated:** March 2026  
**Reading time:** about 5 minutes

-----

## A Problem You’ve Probably Experienced

You start building a new app.

Frontend lives in one repo. Backend lives in another. Each has its own `package.json`, its own deployment pipeline, its own environment variables.

Three months in, the frontend has added a few new API fields. The backend has changed a few interfaces. You notice the data formats have quietly drifted out of sync — but there’s no error message telling you this. You find out when a user reports that a page is broken.

This is the classic problem with managing frontend and backend separately: **both sides evolve independently, with no mechanism forcing them to stay in sync.**

A monorepo solves this.

-----

## What Is a Monorepo?

A **monorepo** (single repository) means keeping all related code for a project in one GitHub repository.

VibeFast’s structure:

```
vibefast/
├── apps/
│   ├── web/        ← Remix frontend
│   └── api/        ← Cloudflare Workers backend
├── packages/
│   ├── ui/         ← Shared UI components
│   └── config/     ← Shared config and type definitions
└── ...
```

Frontend, backend, shared components — all in one place.

-----

## What This Means for Vibe Coders

### One Command Starts Everything

The traditional multi-repo development workflow:

1. Open a terminal, navigate to the backend repo, run `npm run dev`
1. Open another terminal, navigate to the frontend repo, run `npm run dev`
1. Double-check that ports and configs are aligned

VibeFast’s development workflow:

```bash
npm run dev
```

One command. Frontend and backend start simultaneously, watch for changes simultaneously, hot-reload simultaneously.

Deployment works the same way:

```bash
npm run deploy
```

Frontend and backend go live together. No need to remember which to deploy first.

-----

### Type Errors Caught Immediately, Not After Launch

This is one of the most practical benefits of a monorepo.

VibeFast’s frontend and backend share a single set of TypeScript type definitions, stored in `packages/config`. Say your API returns a user object:

```typescript
// packages/config/src/types.ts
export type User = {
  id: number
  email: string
  createdAt: string
}
```

Both frontend and backend import the same `User` type. If you rename `createdAt` to `created_at` on the backend, every place in the frontend that uses that field **immediately shows a type error** — in your editor, the moment you save the file.

Not in code review. Not in testing. Not after a user reports something is broken.

For vibe coders this matters especially because AI-generated code frequently introduces subtle mismatches between frontend and backend. Shared types catch these the instant they happen.

-----

### AI Tools See the Entire Project at Once

This point is underappreciated, but it has a real impact.

When you use Cursor or Claude to help make a change, if frontend and backend live in separate repos, you have to explain: “here’s the frontend logic, and the backend in a different repo looks like this…” The AI can’t see both sides simultaneously — it relies on what you describe.

In a monorepo, the entire project is in one place. You can say “add an endpoint to the API, then call it from this page in the frontend,” and Cursor or Claude sees both sides at once. The generated code connects correctly without you manually copying between windows.

**Monorepo + AI tools is one of the combinations that made 40-minute deploys possible.**

-----

### Build Speed Doesn’t Slow Down as the Project Grows

VibeFast uses **Turborepo** to manage the monorepo’s build pipeline.

Turborepo has an intelligent caching mechanism: it remembers the output of each package’s last build. If you only change frontend code, the backend doesn’t recompile on the next build — Turborepo uses the cached result.

On a small project this difference is hard to notice. But as you add more modules and packages, the gap compounds. VibeFast is designed with the assumption that your project will grow, not just that it needs to run today.

-----

## Comparison: Multi-Repo vs Monorepo

|                          |Multiple Separate Repos              |VibeFast Monorepo                                  |
|--------------------------|-------------------------------------|---------------------------------------------------|
|Start dev environment     |Multiple terminals, multiple commands|`npm run dev`                                      |
|Deploy                    |Remember the order, run separately   |`npm run deploy`                                   |
|Frontend/backend type sync|Manual, drifts easily                |Automatic — change in one place, updates everywhere|
|AI tool assistance        |Context-switching between repos      |Entire project visible at once                     |
|Build speed               |Full rebuild every time              |Turborepo incremental caching                      |

-----

## The Bottom Line

A monorepo isn’t a trendy architectural choice. It solves real development friction: **the sync problems that come from managing frontend and backend separately, the cost of context-switching, and the limitation of AI tools that can’t see an entire project when it’s split across repos.**

VibeFast chose a monorepo because it’s the infrastructure decision that makes vibe coding genuinely smooth.

-----

Want to understand VibeFast’s other core architectural decision?

👉 [Why Cloudflare Full-Stack?](./why-cloudflare-fullstack.md)

Ready to get started?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price goes up to $199 on June 1, 2026.