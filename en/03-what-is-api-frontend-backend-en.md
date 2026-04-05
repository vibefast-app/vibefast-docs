# What is an API? Plain English Explanation of Frontend-Backend Communication

[English](../en/03-what-is-api-frontend-backend-en.md) · [繁中](../zh/03-what-is-api-frontend-backend-zh.md) · [Español](../es/03-what-is-api-frontend-backend-es.md) · [日本語](../jp/03-what-is-api-frontend-backend-jp.md) · [Português (BR)](../pt-br/03-what-is-api-frontend-backend-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~7 minutes

-----

## An Analogy You Use Every Day

You go to a restaurant.

You don't walk into the kitchen and cook yourself. You tell the waiter what you want. The waiter takes your request to the kitchen, the kitchen prepares it, and the waiter brings the food back to you.

**The waiter is the API.**

An API is the communication bridge between the frontend (you) and the backend (kitchen). The frontend doesn't need to know how the backend works, just how to talk to the API.

-----

## What Exactly Is an API?

**API (Application Programming Interface)** is a set of rules defining how two systems communicate.

In the context of web apps, it usually means:

- **Frontend** (page in the browser) sends a request
- **Backend API** (server) receives the request, processes it, returns results

A typical API request looks like this:

```
Frontend asks: "Give me user data for userId = 123"
→ GET /api/users/123

Backend responds: "Here's their data"
→ { "id": 123, "name": "Danko", "email": "danko@example.com" }
```

-----

## HTTP Methods: What Are You Telling the API?

API requests have four common actions, corresponding to different HTTP methods:

|Method          |Purpose  |Analogy        |
|----------------|---------|---------------|
|`GET`           |Read data|"Show me the menu"|
|`POST`          |Create data|"I want to order this dish"|
|`PUT` / `PATCH` |Update data|"Change my order to another dish"|
|`DELETE`        |Delete data|"Cancel this dish"|

-----

## API Responses: What Is the Backend Saying?

Backend responses usually contain two parts:

**HTTP Status Code**—a number telling you the result:

|Code  |Meaning                  |
|------|-------------------------|
|`200` |Success                  |
|`201` |Created successfully     |
|`400` |Your request has issues (Bad Request)|
|`401` |You're not logged in (Unauthorized)|
|`403` |You don't have permission (Forbidden)|
|`404` |Not found                |
|`500` |Server error             |

**Response Content**—usually in JSON format:

```json
{
  "success": true,
  "data": {
    "id": 123,
    "name": "Danko"
  }
}
```

-----

## VibeFast's API Architecture

VibeFast uses **Service Binding** to let frontend and backend communicate directly within Cloudflare's internal network, not over the public internet.

```
Browser
  ↓ HTTP request
Remix Frontend Worker
  ↓ Service Binding (internal, zero CORS)
Workers API
  ↓
D1 Database
```

This means:

- Backend API has no public URL, external access impossible
- No CORS configuration needed between frontend and backend
- Each API call saves one network round trip, faster speed

-----

## Third-Party APIs: Your App Calls Others' APIs Too

Your app doesn't just have its own API, it also calls others' APIs:

- **Stripe API**: "Create a payment for me"
- **Resend API**: "Send an email to this user"
- **Cloudflare API**: `npm run deploy` behind the scenes calls Cloudflare's API

These third-party APIs require API keys to use—this is why the previous article said API keys must be kept safe in environment variables.

-----

## Designing API Endpoints with AI

This is a practical Vibe Coding scenario. You can directly tell AI:

> "Create an API endpoint in Cloudflare Workers, GET /api/posts, return all published articles, sorted by creation time descending, 10 per page."

AI will generate code like this:

```typescript
// apps/api/src/routes/posts.ts
app.get("/api/posts", async (c) => {
  const page = Number(c.req.query("page") || 1)
  const limit = 10
  const offset = (page - 1) * limit

  const posts = await c.env.DB.prepare(`
    SELECT id, title, slug, created_at 
    FROM posts 
    WHERE status = 'published'
    ORDER BY created_at DESC
    LIMIT ? OFFSET ?
  `).bind(limit, offset).all()

  return c.json({ success: true, data: posts.results })
})
```

You don't need to write this yourself, but understanding what it does helps you better judge if AI's output is correct.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
