# 5 Pitfalls I've Hit in Vibe Coding (and How to Avoid Them)

[English](../en/16-vibe-coding-common-mistakes-en.md) · [繁中](../zh/16-vibe-coding-common-mistakes-zh.md) · [Español](../es/16-vibe-coding-common-mistakes-es.md) · [日本語](../jp/16-vibe-coding-common-mistakes-jp.md) · [Português (BR)](../pt-br/16-vibe-coding-common-mistakes-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## Over a Year of Vibe Coding, Hit Plenty of Pitfalls

I started seriously using AI tools to build products in late 2024. VibeFast is the result of over a year's accumulation, but in this process, I hit many unnecessary pitfalls.

This article is what I wish someone had told me from the start.

-----

## Pitfall 1: Throwing Requirements at AI Without Thinking Them Through

**Symptom:** AI generates code, you modify it, modify again, after five or six modifications it's still wrong, ultimately spending more time than writing it yourself.

**Root cause:** Not that AI isn't good, it's that your requirements themselves aren't clear.

AI is very good at turning vague requirements into "looks like it works" but isn't what you want. You say "make me a login feature," AI makes it, but what you wanted was "login with email, no password needed, send one-time link to inbox"—these are completely different.

**How to avoid:** Before asking AI, clarify requirements in one or two sentences:

> "What I want to do, in which file, what's the input, what's the output, what are the constraints."

Spending 5 minutes clarifying requirements saves 30 minutes of back-and-forth modifications.

-----

## Pitfall 2: Changing Too Many Things at Once

**Symptom:** You tell AI "add login feature, user avatar upload, email notifications," AI modifies many files at once, then the app breaks, you don't know where the problem is.

**Root cause:** Changing multiple features simultaneously, any one having issues is hard to pinpoint.

**How to avoid:** Do one thing at a time. After confirming no issues, commit once, then continue to the next feature.

```bash
# Commit after completing each feature
git add .
git commit -m "add low stock warning to product detail page"
```

This way when problems occur, you know the issue must be in the last commit, scope dramatically narrowed.

-----

## Pitfall 3: Deploying Directly Without Local Testing

**Symptom:** After `npm run deploy`, open production environment, it's broken.

**Root cause:** AI-generated code isn't perfect every time, sometimes has syntax errors, sometimes logic has subtle issues.

**How to avoid:** After each modification, first run `npm run dev` to test locally, confirm functionality works and no console errors, then `npm run deploy`.

Particularly test these points:

- Walk through normal flow once
- Edge cases (blank input, maximum values, special characters)
- Logged in/logged out states

-----

## Pitfall 4: Letting AI Make Security-Related Decisions

**Symptom:** You ask AI "how to store user passwords," AI gives a solution, you use it directly, result is plaintext password storage or insecure MD5 hash.

**Root cause:** AI sometimes gives "works but insecure" solutions, because it prioritizes solving your functional requirements, doesn't necessarily proactively consider security.

**How to avoid:** Proactively ask AI about security-related decisions: "Is this approach secure? What are potential risks?"

Password storage: must use bcrypt or argon2, don't use MD5 or SHA1.
JWT secret: must use sufficiently long random string, don't use simple strings.
SQL queries: must use parameterized queries, don't use string concatenation.

These are explained more completely in the security article.

-----

## Pitfall 5: Not Reading AI-Generated Code, Pasting Directly

**Symptom:** App runs, but weeks later you discover a feature behaves strangely, look at the code, completely don't understand what that section is doing, also don't know how to modify it.

**Root cause:** You treat AI as a black box, code goes in, functionality comes out, but you completely don't understand what happens in between.

**How to avoid:** Don't need to understand every line, but clarify key logic.

Ask directly in Cursor:

> "Explain what this code section is doing, in plain language"

AI will explain, you record the explanation in comments:

```typescript
// Display low stock alert when stock <= 3
// isOutOfStock: stock is 0, button disabled
// isLowStock: stock between 1-3, display "Only X left"
const isOutOfStock = product.stock === 0
const isLowStock = product.stock > 0 && product.stock <= 3
```

This way months later when you come back, you can quickly recall what this section does.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
