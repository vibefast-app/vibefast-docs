# How to Discuss Projects with AI: Think First, Code Later

[English](../en/14-how-to-discuss-with-ai-before-coding-en.md) · [繁中](../zh/14-how-to-discuss-with-ai-before-coding-zh.md) · [Español](../es/14-how-to-discuss-with-ai-before-coding-es.md) · [日本語](../jp/14-how-to-discuss-with-ai-before-coding-jp.md) · [Português (BR)](../pt-br/14-how-to-discuss-with-ai-before-coding-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## Most People Use AI the Wrong Way

You encounter a problem, immediately open Cursor, say: "Fix this bug for me" or "Add this feature for me."

AI generates code, you paste it, run it, seems to work. Then you discover it broke something else, ask AI to fix it, breaks another thing...

Half an hour later, you're going in circles, code getting messier, you getting more frustrated.

**The problem isn't that AI isn't good enough, it's that you skipped the most important step: discuss first, implement later.**

-----

## Why Discuss First?

AI is very good at execution—you say do what, it does what, and does it fast.

But "quickly executing the wrong direction" wastes more time than "slowly thinking it through then doing it."

Benefits of discussing first:

- **Confirm what your real problem is**—you think it's problem A, after discussion discover it's actually problem B, solving B is ten times faster than solving A
- **Let AI suggest options you didn't think of**—AI has seen tons of similar scenarios, it knows what approaches exist and their tradeoffs
- **Avoid building then tearing down**—architectural decisions once made wrong are costly to change, discussion costs almost nothing

-----

## Core Principle: First Say "Don't Code Yet"

When you want to discuss a problem, **tell AI from the start you just want to discuss, don't want it to write code yet**:

```
I want to discuss a problem with you, don't write code yet,
wait until we reach consensus before implementing.
```

This sentence is important. AI's default behavior is "give solutions," it will tend to directly generate code. You explicitly say "don't code yet," it will truly enter discussion mode, giving you analysis, options, suggestions, instead of just throwing code at you.

-----

## Three Stages of Discussion

### Stage 1: Clearly State Your Problem

Describe the problem completely, don't just say symptoms, say background:

```
What I'm doing: [what's your project, where are you now]
What problem I'm facing: [specific description, not just "doesn't work"]
What I've tried: [what attempts you've made]
What I want to achieve: [what you hope the final result is]
```

**Example—incomplete problem description:**

```
My login is broken, fix it.
```

**Example—complete problem description:**

```
Don't code yet.

I'm making a subscription app built with VibeFast.
Problem: After users log in, refreshing the page logs them out.
Already tried: Confirmed JWT token generates correctly, cookie is also set.
Goal: Login state should persist after refresh.

What do you think might be the cause? What are possible solutions?
```

The second description lets AI give meaningful diagnosis, instead of guessing a direction and randomly changing things.

-----

### Stage 2: Let AI Suggest Options

Don't just ask "how to do it," ask "what are the approaches, what are their tradeoffs":

```
# Not good enough question
"How to implement user notification feature?"

# Better question
"I want to add user notification feature, don't code yet.
What are the implementation approaches? What's the complexity and tradeoffs of each?
My app currently uses VibeFast architecture (Remix + Cloudflare Workers + D1),
scale is still small, only me maintaining it."
```

AI might give you three options:

1. Simple version: directly store notification records in DB, frontend polling
2. Middle version: use Cloudflare Queues for async notifications
3. Complex version: WebSocket real-time push

With this list, you can make decisions based on your situation—you don't need the most complex, you need what fits your current stage.

-----

### Stage 3: Confirm Consensus Before Implementing

After discussion, before starting implementation, first confirm both sides have shared understanding of the same thing:

```
Okay, I've decided on approach 1 (DB store notifications + polling).
Before writing code, let's confirm our consensus:

1. Create new notifications table, fields are id, user_id, message, is_read, created_at
2. Add GET /api/notifications endpoint, returns current user's unread notifications
3. Frontend polls every 30 seconds, shows red dot when there are new notifications
4. After reading notifications, call PATCH /api/notifications/:id to mark as read

Is this understanding correct? Anything I haven't considered?
```

This step seems redundant, but is very valuable. You articulate the plan in your own words, AI can point out what you missed—like "you forgot to consider pagination when users have lots of notifications"—discovering before implementation is much easier than changing after.

After confirming no issues, then say:

```
Good, now start implementing. Begin with database migration.
```

-----

## A Complete Discussion Example

Below is an excerpt from a complete conversation in a real scenario, demonstrating what this process actually looks like.

-----

**You:**

```
Don't code yet.

I'm using VibeFast to make an app selling digital products.
Problem: I want buyers to be able to repeatedly download files they purchased,
but I don't want non-paying people to directly access download links.

Currently R2 files are public, anyone knowing the URL can download.

What methods can protect these download links?
```

**AI:**

```
Okay, let's discuss. This problem has a few common solutions:

Approach 1: Signed URL (pre-signed links)
When users request download, backend first verifies they have purchase record,
then generates a time-limited R2 Signed URL (e.g., 15 minutes valid),
returns this URL to frontend. Frontend directly uses this URL to download.
Pros: Simple, R2 natively supports. Cons: URL can be shared (valid for 15 minutes).

Approach 2: Backend proxy download
Download request first hits your Workers API, API verifies identity,
Workers directly reads file stream from R2, returns to user.
Pros: Complete control, can track download counts, limit download times.
Cons: Workers must transmit entire file, uses CPU time.

For your situation, approach 1 is usually sufficient. Do you want to limit download times?
```

**You:**

```
Don't need to limit times, but I want to track download counts.
Approach 1 + record downloads in DB, would that work?
```

**AI:**

```
Yes, this combination is very reasonable. The flow would be:

1. User clicks download → hits POST /api/downloads/:productId
2. API verifies user has purchased this product
3. Record in DB's download_logs table (user_id, product_id, timestamp)
4. Generate 15-minute valid R2 Signed URL
5. Return URL, frontend redirects to download

Need to create new download_logs table. Is this understanding correct?
```

**You:**

```
Yes, that works. Now start implementing, begin with the table.
```

-----

This conversation took about five minutes, but before you start coding you already know:

- What approaches exist
- Which you chose and why
- What the implementation scope is (one table + one API endpoint)

The subsequent implementation has clear goals, AI won't veer off course, you won't discover halfway through that the direction was wrong.

-----

## When to Discuss, When to Code Directly?

Not everything needs discussion first.

**Good for discussion first:**

- Architectural decisions (should this feature go in frontend or backend?)
- Problems with multiple approaches (what ways can implement X?)
- You're unsure where the problem root is (why did Y break?)
- Changes with larger impact scope

**Can code directly:**

- Simple clear UI adjustments (change this button color to orange)
- You already know clearly how to do it, just need AI to help write
- Add a new page, logic same as existing page

General principle: **when uncertain, discuss first; when certain, code directly.**

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
