# 環境變數是什麼？為什麼不能把 API Key 寫在 Code 裡

[English](../en/09-environment-variables-and-secrets-en.md) · [繁中](../zh/09-environment-variables-and-secrets-zh.md) · [Español](../es/09-environment-variables-and-secrets-es.md) · [日本語](../jp/09-environment-variables-and-secrets-jp.md) · [Português (BR)](../pt-br/09-environment-variables-and-secrets-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 6 分鐘

-----

## 先從一個真實的災難開始

2022 年，一個開發者把含有 AWS API key 的 code 推上了 GitHub。

四分鐘內，有自動化程式掃描到了這個 key。

八小時後，他收到一張 $50,000 的 AWS 帳單。

這不是特例。GitHub 上每天有數千個 API key 被意外公開，被自動化工具掃描並濫用。

-----

## 什麼是環境變數？

你的 app 需要一些「秘密資訊」才能運作：

- Stripe 的 API key（用來收款）
- Resend 的 API key（用來發郵件）
- JWT secret（用來簽發登入 token）
- 資料庫連線字串

這些東西不能寫在 code 裡，因為 code 會被推上 GitHub。

**環境變數**就是把這些秘密資訊放在 code 以外的地方——作業系統或部署平台的「環境」裡。你的 code 在執行時去讀取這些值，但這些值本身不存在於 code 裡。

-----

## 在本地開發：.env 檔案

本地開發時，你通常會建立一個 `.env` 檔案：

```
STRIPE_SECRET_KEY=sk_live_xxxxx
RESEND_API_KEY=re_xxxxx
JWT_SECRET=my-super-secret-key
```

然後在 code 裡這樣讀取：

```typescript
// Node.js / Remix
const stripeKey = process.env.STRIPE_SECRET_KEY

// Cloudflare Workers
const stripeKey = env.STRIPE_SECRET_KEY
```

**關鍵：`.env` 檔案絕對不能推上 GitHub。**

確認你的 `.gitignore` 裡有這一行：

```
.env
.env.local
.env.production
```

-----

## 在正式環境：平台的 Secrets 管理

本地的 `.env` 只在你自己的電腦上有效。部署到正式環境，你需要在平台上設定環境變數。

**Cloudflare Workers 的做法：**

```bash
# 用 Wrangler CLI 設定 secret
npx wrangler secret put JWT_SECRET
# 輸入值之後，Cloudflare 會加密儲存，不會出現在任何 log 裡
```

或者在 Cloudflare Dashboard 的 Workers → Settings → Variables 手動設定。

-----

## 常見錯誤

### 錯誤一：.env 推上了 GitHub

```bash
# 補救方法：從 git 歷史裡移除
git rm --cached .env
git commit -m "remove .env from tracking"
```

但如果已經推上去了，假設這個 key 已經洩漏，立刻去對應的平台撤銷並重新生成新的 key。

### 錯誤二：key 硬寫在 code 裡

```typescript
// ❌ 這樣等於公開你的 key
const stripe = new Stripe("sk_live_xxxxx")

// ✅ 正確做法
const stripe = new Stripe(env.STRIPE_SECRET_KEY)
```

### 錯誤三：把 .env 的值 console.log 出來

```typescript
// ❌ log 可能被記錄下來
console.log("Stripe key:", env.STRIPE_SECRET_KEY)

// ✅ 只確認有沒有值，不印出值
console.log("Stripe key exists:", !!env.STRIPE_SECRET_KEY)
```

-----

## 總結

環境變數的核心概念很簡單：**秘密的東西不能在 code 裡，要放在環境裡。**

三個記住的原則：

1. `.env` 加進 `.gitignore`
1. 正式環境的 secret 在平台上設定，不要在 code 裡
1. 不小心洩漏了就立刻撤銷重生成，不要心存僥倖

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
