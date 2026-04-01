# Why I Started Building Seriously at 50

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## I Hadn’t Written Code in 20 Years

I studied computer science in university. After graduating, I worked as a software engineer for a few years.

Then I started my own business.

Once I went down that path — running a company, managing people, dealing with customers — the technical side of my life quietly faded away. Not overnight. Just gradually, over two decades, until the things I once knew were so far behind the current state of the industry that they barely counted.

During those years, I had a lot of ideas. “It would be great if there was an app that did X.” “Why hasn’t anyone solved this problem?” The ideas kept coming. But I had no way to turn them into real products. I used to know how to code, just not the modern kind. Hiring engineers was expensive, and the communication overhead was even more costly. Teaching myself? Every time I started, something interrupted me — or I’d get halfway through learning something and discover I needed ten other things first.

So the ideas sat in my head for years. Then quietly disappeared.

-----

## Then Something Changed in Late 2024

I started using AI tools seriously — not just to ask questions, but to actually write code.

The first time I had AI generate a working web app for me, I sat in front of my screen and felt something strange. How did this get so easy?

It’s not that there’s no learning curve. You still need to understand basic concepts. You still need to judge whether what the AI produces is correct. You still need to work through errors. But for me, there was an extra advantage: the fundamentals from my CS background were still there. The logic, the mental models — just buried under twenty years of rust. AI helped me close that gap.

The wall that said “you’re too far behind to build products” suddenly got a lot shorter.

-----

## Why Cloudflare?

I tried different stacks.

Next.js and Vercel are the most documented combination, and I used them for a while. But every time something broke, I was chasing the problem across Vercel, Supabase, and AWS — documentation scattered across three platforms, no clear place to start.

Then I looked seriously at Cloudflare’s ecosystem and found it solved most of what I hated:

- One account, one dashboard, one bill
- Workers means no servers to manage — deploy is just deploy
- D1 sits right next to Workers — no cross-network database calls
- R2 for file storage with zero egress fees

Most importantly: one command to take an app from zero to a globally deployed production environment.

For a solopreneur, every service you don’t have to manage is one less thing pulling your attention away from the product.

-----

## How VibeFast Came to Be

After building a few projects on Cloudflare, I noticed I kept doing the same things at the start of every new project: set up auth, set up the database, set up Stripe, set up email, wire the frontend and backend together.

Days of work. And every time, I’d step on at least one of the same traps I’d already stepped on before.

I started consolidating all of that setup into a reusable starting point — something I could clone for each new project, swap in the branding, and ship.

Then I thought: if this starting point is useful for me, it’s probably useful for other Vibe Coders too.

That became VibeFast.

-----

## What’s Different About Starting at 50?

Honestly — some things are harder.

I learn new things more slowly than I did in my twenties. When I look at complex TypeScript, my brain needs more time. Twenty years away from the field isn’t something you fully recover from in a few months, even with a computer science foundation.

But some things are easier.

I know exactly what problem I want to solve, because I have enough years of real business experience to recognize what actually hurts. I don’t need to go looking for “a good idea to build.” I build the things I wish existed.

And I no longer need to prove anything to anyone. One advantage of being fifty is that other people’s opinions carry less weight. That makes it easier to make decisions you actually believe in, rather than decisions you think will be approved of.

-----

## For Anyone Who Thinks They Started Too Late

If you’re at some point in your life, reading about people who started coding at fourteen and feeling like you missed the window —

I don’t think there’s such a thing as too late to start.

AI tools have lowered the barrier significantly. You don’t need to learn a programming language from scratch or understand every underlying principle. What you need is: a real problem you want to solve, the willingness to learn enough to evaluate what AI produces, and the patience to keep going when things break.

Everything in the vibefast-docs repo is me taking what I’ve learned and writing it in language that anyone can follow. The course is free because I want this door to be open to more people.

If you want a starting point that’s already configured — so you can focus on building your product instead of wiring up infrastructure — that’s what VibeFast is for.

-----

Whatever you choose, the important thing is to start.

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app)** — Early bird $99, price increases to $199 on June 1, 2026.