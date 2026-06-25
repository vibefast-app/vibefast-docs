# How to Test Your App: Let AI Generate curl Commands for Every Feature

[English](../en/21-ai-testing-guide-en.md) · [繁中](../zh/21-ai-testing-guide-zh.md) · [Español](../es/21-ai-testing-guide-es.md) · [日本語](../jp/21-ai-testing-guide-jp.md) · [Português (BR)](../pt-br/21-ai-testing-guide-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~7 minutes

-----

## After Deploying, How Do You Know It Actually Works?

A lot of people deploy, open a browser, click around, and assume everything is fine because nothing visibly broke.

But the browser only tests what you can see. Is your API returning the right data? Does the frontend actually return a 200? Does a failed login return the correct error code? None of that is visible from clicking around.

My habit: **every time I finish a feature, I test it with curl immediately.** Whether it’s local development or a production deployment, curl works in both environments and the results are immediate.

-----

## What is curl?

curl is a command-line tool for sending HTTP requests. It comes pre-installed on Mac and Linux, and is available on Windows too.

No npm packages to install. No code to write. One line in the terminal tests any URL — frontend pages and backend APIs alike.

**And you don’t need to memorize curl syntax. Just ask AI to generate the commands.**

-----

## Step 1: Ask AI to Generate Your curl Commands

After finishing any feature, tell AI directly:

```
I just deployed a handmade soap e-commerce site to https://soapco.com.

Generate curl commands to test these features:
1. Homepage returns successfully
2. Product listing API (GET /api/products)
3. Single product detail (GET /api/products/lavender-soap)
4. Login API (POST /api/auth/login) with email: test@example.com, password: test123
5. Using the token from login, test the protected order list API (GET /api/orders)
6. Hit the order list without a token — confirm it's blocked (should return 401)
```

AI generates a complete set of curl commands. Copy and paste them into your terminal and run them.

**This is the core mindset for Vibe Coder testing: you describe what to test, AI writes how to test it.**

-----

## curl Works on Frontend Pages Too

A lot of people assume curl is only for backend APIs. It works on frontend pages just as well:

```
Generate curl commands to test these frontend pages:
1. Homepage https://soapco.com/ returns 200
2. Product listing page /shop returns successfully
3. A non-existent page /xyz returns 404 (not 200)
4. The /admin dashboard, when accessed without logging in, redirects to login (should return 302 or 401)
```

AI generates something like:

```bash
# Test homepage
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/
# Expected: 200

# Test non-existent page
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/xyz
# Expected: 404

# Test admin page is blocked without login
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/admin
# Expected: 302 or 401, never 200
```

`-o /dev/null` discards the HTML body so it doesn’t clutter your terminal. `-w "%{http_code}"` prints only the status code — for frontend testing, the status code is usually all you need.

-----

## When You Need to See the Full Response

Sometimes you need to see the full API response body. Tell AI:

```
Generate a curl command to hit GET https://soapco.com/api/products
and display the JSON response formatted for easy reading.
```

AI generates:

```bash
curl -s https://soapco.com/api/products | jq
```

`jq` is a JSON formatting tool. Install it on Mac with `brew install jq`, or just ask AI “how do I install jq on my system.”

-----

## When You Need to See the Status Code

Not sure whether an API is returning 200, 401, or 500? Tell AI:

```
Generate a curl command to hit this endpoint and display
both the HTTP status code and the response body:
POST https://soapco.com/api/auth/login
body: {"email": "test@example.com", "password": "wrongpassword"}
```

AI generates:

```bash
curl -i -X POST https://soapco.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "wrongpassword"}'
```

`-i` includes the HTTP status code and response headers in the output.

-----

## Testing Locally Works the Same Way

No need to wait for a deployment — test locally as soon as it’s running:

```
Generate curl commands to test the product API locally.
The local server runs at http://localhost:8787
```

AI swaps in the localhost URL. Everything else is identical.

**Test locally → confirm it works → deploy → test again with the production URL.**

Both steps need to pass before you can call it done.

-----

## A Good Habit: Ask AI to Bundle Your Tests Into a Script

Once you have multiple features to test, ask AI to consolidate them:

```
Bundle these tests into a test.sh script.
Each test should print "PASS" or "FAIL",
and the script should show a final count of passed and failed tests.

Tests:
1. Homepage returns 200
2. Product listing API returns 200
3. Non-existent product returns 404
4. Order API without token returns 401

Base URL: https://soapco.com
```

Then run it after every deployment:

```bash
bash test.sh
```

A few seconds to confirm all your main features are still working — no need to run commands one by one.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on August 1, 2026.
