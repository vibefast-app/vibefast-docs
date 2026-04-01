# JWT 是什麼？用白話解釋這個你每天都在用的技術

[English](../en/04-what-is-jwt-authentication-en.md) · [繁中](../zh/04-what-is-jwt-authentication-zh.md) · [Español](../es/04-what-is-jwt-authentication-es.md) · [日本語](../jp/04-what-is-jwt-authentication-jp.md) · [Português (BR)](../pt-br/04-what-is-jwt-authentication-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 8 分鐘

-----

## 先從一個真實場景開始

你打開一個 app，輸入帳號密碼登入。

然後你點了一個頁面，又點了另一個頁面，系統每次都知道你是誰，不會要你重新登入。

這件事聽起來很自然，但背後藏著一個問題：

**HTTP 是「無記憶」的協議。**

每一次請求對伺服器來說都是全新的——它不會自動記得「上一個請求是誰發的」。你登入之後，下一次點頁面，伺服器怎麼知道還是你？

這就是 JWT 要解決的問題。

-----

## JWT 是什麼？

**JWT（JSON Web Token）** 是一種讓伺服器「記住你是誰」的方式。

你可以把它想成一張**數位通行證**：

1. 你登入，伺服器確認你的帳號密碼正確
1. 伺服器給你一張通行證（JWT token）
1. 之後你每次請求，都把這張通行證帶著
1. 伺服器看到通行證，就知道你是誰、有什麼權限

整個過程不需要伺服器記住任何東西——所有資訊都在通行證裡面。

-----

## JWT 長什麼樣子？

JWT 是一個很長的字串，長這樣：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOjEyMywiZW1haWwiOiJ1c2VyQGV4YW1wbGUuY29tIiwicm9sZSI6InVzZXIiLCJleHAiOjE3MDk0NTEyMDB9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

看起來很複雜，但它其實分成三個部分，用 `.` 隔開：

```
[Header].[Payload].[Signature]
```

### Header（標頭）

說明這個 token 用什麼演算法加密：

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

### Payload（內容）

放真正的資料——你是誰、有什麼權限、這個 token 什麼時候到期：

```json
{
  "userId": 123,
  "email": "user@example.com",
  "role": "user",
  "exp": 1709451200
}
```

這部分**沒有加密**，只是 Base64 編碼。任何人拿到 token 都可以解碼看到這些內容——所以不要把密碼或敏感資料放在這裡。

### Signature（簽名）

這才是關鍵。伺服器用一個只有它自己知道的 **secret key** 對前兩個部分簽名：

```
HMACSHA256(
  base64(header) + "." + base64(payload),
  secret_key
)
```

簽名的作用：**防止偽造**。

如果有人拿到你的 token，試圖改掉 payload 裡的 `role: "user"` 變成 `role: "admin"`，簽名就會對不上。伺服器一驗證就知道這個 token 被動過手腳，直接拒絕。

-----

## JWT 和傳統 Session 有什麼差？

另一種常見的身份驗證方式是 **Session**：

|          |Session                |JWT               |
|----------|-----------------------|------------------|
|伺服器需要儲存狀態 |✅ 需要（儲存 session 資料）    |❌ 不需要（資訊在 token 裡）|
|適合分散式系統   |❌ 較複雜（多台伺服器要共享 session）|✅ 天然適合（每個伺服器都能驗證） |
|Token 可以撤銷|✅ 容易（刪掉 session 就好）    |⚠️ 較複雜（需要額外機制）     |
|邊緣運算相容    |❌ 困難                   |✅ 完美相容            |

**JWT 在邊緣運算（Cloudflare Workers）特別適合**，因為 Workers 是無狀態的——每個請求可能跑在不同的邊緣節點上，沒辦法共享 session 記憶體。JWT 把所有資訊都帶在 token 裡，任何節點收到請求都能自己驗證，不需要查資料庫。

這就是為什麼 VibeFast 選擇 JWT 而不是 Session。

-----

## JWT 的安全要點

### Secret Key 要夠強、要保密

JWT 的安全性完全建立在 secret key 上。如果有人拿到你的 secret key，他可以偽造任何人的 token，偽裝成任何用戶，包括 admin。

Secret key 要從環境變數讀取，不能硬寫在 code 裡。可以用指令生成一個隨機的強 secret：

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

把生成的值存進 Cloudflare Workers 環境變數，不要讓它出現在任何 code 檔案裡。

### Token 要設定到期時間

通行證不應該永遠有效。JWT 的 `exp` 欄位設定到期時間，超過時間的 token 伺服器會拒絕。

常見做法是 access token 有效期 15 分鐘到 7 天，視安全需求調整。到期後用戶需要重新登入。

### Token 要放在安全的地方

token 放在哪裡很重要：

|儲存位置            |風險            |
|----------------|--------------|
|localStorage    |⚠️ 容易被 XSS 攻擊竊取|
|sessionStorage  |⚠️ 同上          |
|HttpOnly Cookie |✅ 較安全，JS 無法讀取 |
|記憶體（React state）|✅ 安全但重新整理後消失  |

VibeFast 用 HttpOnly Cookie 儲存 token，這是目前最常見的安全做法。

### Payload 不要放敏感資料

JWT 的 payload 是 Base64 編碼，不是加密。任何人只要拿到 token，一秒鐘就能解碼看到裡面的內容。

所以 payload 裡只放身份識別資訊（userId、role、email），絕對不要放密碼、信用卡號、或任何不能公開的資料。

-----

## 一個完整的 JWT 流程

用白話把整個流程走一遍：

```
1. 用戶輸入帳號密碼 → 送到後端

2. 後端查資料庫，確認密碼正確

3. 後端用 secret key 產生 JWT token：
   payload = { userId: 123, role: "user", exp: 明天 }
   token = sign(payload, secret_key)

4. 把 token 放進 HttpOnly Cookie，回傳給瀏覽器

5. 之後每次請求，瀏覽器自動帶上 Cookie

6. 後端收到請求：
   - 取出 token
   - 用 secret key 驗證簽名
   - 確認沒過期
   - 從 payload 取出 userId，知道是誰

7. 處理請求，回傳結果
```

這個流程在 VibeFast 裡已經全部實作好了，你不需要自己寫。

-----

## 常見問題

**Q：JWT token 被偷了怎麼辦？**

這是 JWT 最常被問到的問題。token 一旦發出去，在到期之前很難撤銷（除非你維護一個黑名單，但這樣就失去了 JWT 無狀態的優勢）。

解決方法是：縮短 token 有效期（比如 1 小時），搭配 refresh token 機制自動更新。VibeFast 目前用 7 天有效期，適合大多數 web app。

**Q：一定要用 JWT 嗎？**

不是。Session + Cookie 是另一個成熟的方案，在傳統伺服器環境裡完全可行。但在 Cloudflare Workers 這種邊緣運算環境，JWT 是更自然的選擇。

**Q：JWT 和 OAuth 是什麼關係？**

OAuth 是一個授權框架（讓第三方 app 代表你存取資源，例如「用 Google 登入」）。JWT 是 OAuth 流程中常用的 token 格式，但 JWT 可以獨立使用，不一定要搭配 OAuth。

-----

## 總結

JWT 是一張數位通行證，讓無狀態的 HTTP 系統能夠識別用戶身份。它特別適合 Cloudflare Workers 這種邊緣運算環境，因為：

- 不需要伺服器儲存 session 狀態
- 任何邊緣節點都能自己驗證
- 簽名機制防止偽造

使用 JWT 的三個安全要點：secret key 保密、設定到期時間、payload 不放敏感資料。

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
