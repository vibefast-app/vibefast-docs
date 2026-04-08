# What is JWT? Plain English Explanation of This Tech You Use Every Day

[English](../en/04-what-is-jwt-authentication-en.md) · [繁中](../zh/04-what-is-jwt-authentication-zh.md) · [Español](../es/04-what-is-jwt-authentication-es.md) · [日本語](../jp/04-what-is-jwt-authentication-jp.md) · [Português (BR)](../pt-br/04-what-is-jwt-authentication-pt-br.md)

**Author:** Danko Peng ([@dankopeng](https://x.com/dankopeng))  
**Updated:** March 2026  
**Reading time:** ~8 minutes

-----

## Start with a Real Scenario

You open an app, enter your username and password to log in.

Then you click to another page, and another page, and the system always knows who you are without asking you to log in again.

This seems natural, but there's a problem behind it:

**HTTP is a "stateless" protocol.**

Every request to the server is brand new—it doesn't automatically remember "who sent the last request." After you log in, when you click to the next page, how does the server know it's still you?

This is the problem JWT solves.

-----

## What Is JWT?

**JWT (JSON Web Token)** is a way for servers to "remember who you are."

Think of it as a **digital pass**:

1. You log in, server confirms your credentials are correct
2. Server gives you a pass (JWT token)
3. Every subsequent request, you bring this pass
4. Server sees the pass, knows who you are and what permissions you have

The whole process doesn't require the server to remember anything—all information is in the pass.

-----

## What Does JWT Look Like?

JWT is a very long string, like this:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMywiZW1haWwiOiJ1c2VyQGV4YW1wbGUuY29tIiwicm9sZSI6InVzZXIiLCJleHAiOjE3MDk0NTEyMDB9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

Looks complex, but it's actually three parts separated by `.`:

```
[Header].[Payload].[Signature]
```

### Header

Describes what algorithm this token uses for encryption:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload

Contains the actual data—who you are, what permissions you have, when this token expires:

```json
{
  "userId": 123,
  "email": "user@example.com",
  "role": "user",
  "exp": 1709451200
}
```

This part is **not encrypted**, just Base64 encoded. Anyone with the token can decode and see this content—so never put passwords or sensitive data here.

### Signature

This is the key. The server uses a **secret key** only it knows to sign the first two parts:

```
HMACSHA256(
  base64(header) + "." + base64(payload),
  secret_key
)
```

The signature's purpose: **prevent forgery**.

If someone gets your token and tries to change `role: "user"` to `role: "admin"` in the payload, the signature won't match. The server verifies and knows the token was tampered with, rejecting it immediately.

-----

## How Is JWT Different from Traditional Sessions?

Another common authentication method is **Session**:

|          |Session                |JWT               |
|----------|-----------------------|------------------|
|Server needs to store state|✅ Yes (store session data)|❌ No (info in token)|
|Suitable for distributed systems|❌ Complex (multiple servers need to share session)|✅ Naturally fits (every server can verify)|
|Token can be revoked|✅ Easy (just delete session)|⚠️ Complex (needs extra mechanism)|
|Edge computing compatible|❌ Difficult|✅ Perfect fit|

**JWT is particularly suitable for edge computing (Cloudflare Workers)** because Workers are stateless—each request might run on a different edge node, can't share session memory. JWT carries all information in the token, any node receiving a request can verify it independently without checking a database.

This is why vibefast.app chooses JWT over Session.

-----

## JWT Security Points

### Secret Key Must Be Strong and Confidential

JWT's security entirely depends on the secret key. If someone gets your secret key, they can forge anyone's token, impersonate any user, including admin.

Secret key must be read from environment variables, never hardcoded. Generate a random strong secret with this command:

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

Store the generated value in Cloudflare Workers environment variables, never let it appear in any code file.

### Token Must Have Expiration Time

Passes shouldn't be valid forever. JWT's `exp` field sets expiration time, server rejects expired tokens.

Common practice is access token validity of 15 minutes to 7 days, adjusted based on security needs. After expiration, users need to log in again.

### Token Must Be Stored Securely

Where you store the token matters:

|Storage Location    |Risk            |
|--------------------|----------------|
|localStorage        |⚠️ Vulnerable to XSS attacks|
|sessionStorage      |⚠️ Same as above|
|HttpOnly Cookie     |✅ Safer, JS can't read|
|Memory (React state)|✅ Safe but disappears on refresh|

vibefast.app uses HttpOnly Cookie to store tokens, currently the most common secure practice.

### Payload Shouldn't Contain Sensitive Data

JWT's payload is Base64 encoded, not encrypted. Anyone with the token can decode and see the contents in one second.

So payload only contains identity information (userId, role, email), never put passwords, credit card numbers, or any data that can't be public.

-----

## A Complete JWT Flow

Walk through the entire flow in plain language:

```
1. User enters username/password → sent to backend

2. Backend checks database, confirms password is correct

3. Backend uses secret key to generate JWT token:
   payload = { userId: 123, role: "user", exp: tomorrow }
   token = sign(payload, secret_key)

4. Put token in HttpOnly Cookie, return to browser

5. Every subsequent request, browser automatically includes Cookie

6. Backend receives request:
   - Extract token
   - Verify signature with secret key
   - Confirm not expired
   - Extract userId from payload, know who it is

7. Process request, return result
```

This entire flow is already implemented in vibefast.app, you don't need to write it yourself.

-----

## Common Questions

**Q: What if JWT token is stolen?**

This is JWT's most asked question. Once a token is issued, it's hard to revoke before expiration (unless you maintain a blacklist, but that loses JWT's stateless advantage).

Solution: shorten token validity (e.g., 1 hour), pair with refresh token mechanism for automatic renewal. vibefast.app currently uses 7-day validity, suitable for most web apps.

**Q: Must I use JWT?**

No. Session + Cookie is another mature solution, completely viable in traditional server environments. But in Cloudflare Workers edge computing environments, JWT is a more natural choice.

**Q: What's the relationship between JWT and OAuth?**

OAuth is an authorization framework (letting third-party apps access resources on your behalf, like "Sign in with Google"). JWT is a commonly used token format in OAuth flows, but JWT can be used independently without OAuth.

-----

## Summary

JWT is a digital pass that lets stateless HTTP systems identify users. It's particularly suitable for Cloudflare Workers edge computing environments because:

- No need for server to store session state
- Any edge node can verify independently
- Signature mechanism prevents forgery

Three security points for using JWT: keep secret key confidential, set expiration time, don't put sensitive data in payload.

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [Back to course index](../README.md)

Ready to skip the setup and start building?  
👉 **[vibefast.app](https://vibefast.app/pricing)** — Early bird $99, price increases to $199 on June 1, 2026.
