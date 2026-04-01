# Vibe Coder 的資安入門：上線之前要確認這些事

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 10 分鐘

-----

## 你不需要成為資安專家，但你需要知道這些

很多 Vibe Coder 在 app 上線之後才開始想資安問題。這很正常——當你用 AI 快速把功能做出來，資安很容易被推到「之後再說」的清單裡。

但有幾件事，不是「之後再說」，是「上線之前必須確認」。

這篇文章不是要把你變成資安工程師，而是幫你把最重要的幾個檢查點說清楚，用你能理解的語言。

-----

## 1. Secret Key 絕對不能出現在 code 裡

這是最常見、也最嚴重的錯誤。

```typescript
// ❌ 錯誤：secret key 寫死在 code 裡
const token = jwt.sign(payload, "my-super-secret-key-123")

// ✅ 正確：從環境變數讀取
const token = jwt.sign(payload, env.JWT_SECRET)
```

為什麼這麼嚴重？因為你的 code 通常會推到 GitHub。只要 repo 是公開的，或者有一天不小心設成公開，任何人都能看到你的 secret key，然後偽造任何用戶的身份、繞過所有驗證。

**VibeFast 怎麼處理：** `npm run setup` 自動生成 JWT secret 並寫入 Cloudflare Workers 的環境變數。你永遠不會在 code 裡看到 secret key 的值。

**你要確認：**

- `.env` 檔案有加進 `.gitignore`
- 所有 secret（Stripe key、Resend key、JWT secret）都從 `env` 讀取，不要硬寫在 code 裡
- 推 code 上 GitHub 之前，搜尋一下 repo 裡有沒有出現 `sk_live_`、`re_`、`secret` 這類字串

-----

## 2. 用戶輸入永遠不要直接信任

你的 app 接收用戶輸入的地方——表單、URL 參數、API 請求——每一個都是潛在的攻擊入口。

### SQL Injection

如果你直接把用戶輸入拼進 SQL 查詢，攻擊者可以注入惡意的 SQL 語句：

```typescript
// ❌ 危險：直接拼字串
const query = `SELECT * FROM users WHERE email = '${userInput}'`
// 攻擊者輸入：' OR '1'='1
// 結果：SELECT * FROM users WHERE email = '' OR '1'='1'
// 這會回傳所有用戶資料

// ✅ 安全：用參數化查詢
const result = await db.prepare(
  "SELECT * FROM users WHERE email = ?"
).bind(userInput).first()
```

D1 支援參數化查詢，VibeFast 的所有資料庫操作都用這個方式。如果你自己寫 SQL，永遠用 `?` 佔位符，不要用字串拼接。

### XSS（跨站腳本攻擊）

如果你把用戶輸入直接渲染成 HTML，攻擊者可以注入惡意腳本：

```typescript
// ❌ 危險
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ 安全：讓 React/Remix 自動跳脫
<div>{userInput}</div>
```

Remix 預設會自動處理跳脫，只要你不使用 `dangerouslySetInnerHTML`，基本上是安全的。

-----

## 3. API 端點要驗證身份和權限

每一個需要登入才能使用的 API 端點，都要驗證 JWT token。這聽起來是廢話，但很多人在快速開發的時候會忘記。

```typescript
// ❌ 忘記驗證
export async function action({ request }: ActionFunctionArgs) {
  // 直接處理，沒有確認用戶是否登入
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}

// ✅ 先驗證身份
export async function action({ request, context }: ActionFunctionArgs) {
  const user = await requireAuth(request, context.env)
  // 確認有登入才繼續
  const data = await request.json()
  await db.prepare("DELETE FROM posts WHERE id = ?").bind(data.id).run()
}
```

更進一步：**不只驗身份，也要驗權限**。用戶 A 不應該能刪除用戶 B 的文章，即使兩個人都登入了。

```typescript
// ✅ 驗身份 + 驗權限
const post = await db.prepare(
  "SELECT * FROM posts WHERE id = ?"
).bind(postId).first()

if (post.authorId !== user.userId) {
  throw new Response("Forbidden", { status: 403 })
}
```

-----

## 4. 敏感操作要有速率限制

如果你的登入 API 沒有速率限制，攻擊者可以用程式自動嘗試幾百萬個密碼組合（暴力破解）。

Cloudflare 在這方面有先天優勢——你可以用 Cloudflare 的 Rate Limiting 功能，在流量進入 Workers 之前就擋掉異常的請求頻率，不需要自己在 code 裡實作。

至少要限制的端點：

- `/login`、`/signup`——防止暴力破解
- `/api/send-email`——防止被用來發垃圾郵件
- 任何會觸發費用的操作（Stripe 付款、R2 上傳）

-----

## 5. HTTPS 是標配，不是選配

你的 app 一定要用 HTTPS，不能用 HTTP。

好消息是：**部署在 Cloudflare 上的 app 自動啟用 HTTPS**，不需要任何額外設定。Cloudflare 幫你處理 SSL 憑證的申請和更新，你完全不需要操心。

但要確認的一點：如果你有自訂域名，在 Cloudflare Dashboard 確認 SSL/TLS 設定是 Full 或 Full (Strict)，不要用 Flexible（Flexible 模式下，Cloudflare 到你的 origin 那段還是 HTTP，有安全隱患）。

-----

## 6. 錯誤訊息不要洩漏內部資訊

開發時你可能習慣把詳細錯誤訊息顯示出來方便 debug：

```typescript
// ❌ 正式環境不應該這樣
catch (error) {
  return json({ error: error.message, stack: error.stack })
}
```

`error.stack` 會暴露你的 code 結構、檔案路徑、甚至函式名稱，給攻擊者提供有用的資訊。

```typescript
// ✅ 正式環境
catch (error) {
  console.error(error) // 只在 log 裡記錄完整錯誤
  return json({ error: "Something went wrong" }, { status: 500 })
}
```

-----

## 7. 定期更新依賴套件

你的 app 依賴很多第三方套件，這些套件偶爾會發現安全漏洞並發布修復版本。如果你一直用舊版，就會暴露在已知漏洞裡。

```bash
# 查看有哪些套件有安全問題
npm audit

# 自動修復輕微問題
npm audit fix
```

不需要每天做，但至少每個月跑一次 `npm audit`，有高嚴重性（high/critical）的漏洞要優先處理。

-----

## 上線前的資安檢查清單

```
□ 所有 secret key 都在環境變數裡，沒有硬寫在 code 裡
□ .env 已加入 .gitignore
□ 所有 SQL 查詢使用參數化方式
□ 所有需要登入的 API 都有驗證 JWT
□ 敏感 API 端點有速率限制（或使用 Cloudflare Rate Limiting）
□ 正式環境的錯誤訊息不暴露 stack trace
□ 跑過 npm audit，沒有 high/critical 漏洞
□ Cloudflare SSL/TLS 設定為 Full 或 Full (Strict)
```

-----

## 總結

資安不是「有空再說」的事，但也不需要一次做到完美。

對大多數 indie app 來說，把這篇提到的七個要點做好，就已經比市面上 80% 的小型 app 安全了。攻擊者通常找的是最容易下手的目標——只要你不是最弱的那一個，就能避掉大多數風險。

-----

有問題歡迎在 X [@dankopeng](https://x.com/dankopeng) 找我。

VibeFast 已經幫你把最基礎的資安架構設定好，讓你專注在產品本身：  
👉 **[vibefast.app](https://vibefast.app)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。