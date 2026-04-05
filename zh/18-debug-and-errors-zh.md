# 遇到錯誤不要慌：用 AI 讀懂錯誤訊息、解決問題

[English](../en/18-debug-and-errors-en.md) · [繁中](../zh/18-debug-and-errors-zh.md) · [Español](../es/18-debug-and-errors-es.md) · [日本語](../jp/18-debug-and-errors-jp.md) · [Português (BR)](../pt-br/18-debug-and-errors-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 8 分鐘

-----

## 看到紅色錯誤訊息，不要關掉它

初學者遇到錯誤的第一反應通常是：慌、關掉、重試、還是一樣。

但錯誤訊息不是你的敵人，**它是你最重要的線索。** 它在告訴你哪裡出了問題、為什麼出問題。學會讀懂它，你解決問題的速度會快十倍。

更重要的是：**你不需要自己讀懂每一個錯誤，直接貼給 AI 問就好。**

-----

## 三種常見錯誤

### 語法錯誤（Syntax Error）

code 寫錯了，缺少括號、引號沒關閉、拼錯關鍵字。這類錯誤最容易修，因為 Cursor 通常會直接在那一行顯示紅色底線。

```
SyntaxError: Unexpected token '}'
```

直接告訴 AI：「這段 code 有語法錯誤，幫我找出來修好。」

### 執行時錯誤（Runtime Error）

code 語法沒問題，但跑起來出錯。常見的例子：

```
TypeError: Cannot read properties of undefined (reading 'name')
```

這通常是你以為某個變數有值，但它其實是 `undefined`。比如從資料庫查出來的資料是空的，你卻直接存取它的屬性。

```
ReferenceError: env is not defined
```

用了一個還沒有宣告的變數。

### 邏輯錯誤（Logic Error）

最難找的一種。code 跑起來沒有報錯，但結果不對——商品價格算錯了、庫存沒有正確扣除、用戶登入之後沒有跳轉到正確頁面。

這類錯誤需要你去追蹤程式的執行過程，找出哪一步的判斷或計算出了問題。

-----

## 第一關：用 npm run build 驗證 AI 寫的 code

AI 幫你寫完一段 code 之後，**不要急著跑 `npm run dev` 或直接部署，先跑 `npm run build`。**

```bash
npm run build
```

`npm run build` 會做兩件事：

1. **TypeScript 型別檢查**——找出型別錯誤、未定義的變數、函式呼叫錯誤
1. **編譯 code**——確認整個專案能成功打包

這個指令通常比直接跑 dev server 快，而且能在你部署之前就抓到很多問題。

**看到 build 成功才繼續：**

```bash
✓ Built in 2.3s   ← 這樣才對
```

如果 build 失敗，終端機會告訴你哪個檔案、哪一行出了什麼問題：

```
src/routes/checkout.ts:23:18 - error TS2532: Object is possibly 'undefined'.
```

這比等到跑起來才發現錯誤快得多。把這個錯誤貼給 AI，讓它修好，再 build 一次確認通過，才繼續下一步。

**養成習慣：AI 改完 code → `npm run build` → 沒問題 → `npm run dev` 測試功能。**

-----

## 遇到錯誤的標準流程

### 第一步：把完整的錯誤訊息複製起來

不要只截圖、不要只記「有個錯誤」。複製完整的錯誤文字，包括：

- 錯誤類型（TypeError、SyntaxError 等）
- 錯誤描述
- 錯誤發生的檔案和行數（stack trace）

```
TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)
    at Object.<anonymous> (api/src/index.ts:45:5)
```

這段訊息告訴你：在 `checkout.ts` 的第 23 行，有個東西是 `undefined`，你試圖讀取它的 `price` 屬性。

### 第二步：貼給 AI，說清楚情況

```
我在做 Cloudflare Workers 的結帳功能，遇到這個錯誤：

TypeError: Cannot read properties of undefined (reading 'price')
    at handleCheckout (api/src/routes/checkout.ts:23:18)

這是我的 code：
[貼上相關的 code]

我在做什麼：用戶點「購買」之後，後端從 D1 查詢商品，然後建立 Stripe Checkout Session。

幫我解釋這個錯誤是什麼意思，可能是什麼原因，怎麼修。
```

### 第三步：理解 AI 的解釋，再讓它修

AI 解釋之後，確認你理解了問題所在，再讓它幫你修。不要直接說「幫我修好」，而是先問「為什麼會這樣」——這樣你下次遇到類似問題才知道怎麼找。

-----

## console.log：最簡單的調試工具

當你不確定某個變數的值是什麼，最直接的方法是用 `console.log` 把它印出來：

```typescript
export async function handleCheckout(request: Request, env: Env) {
  const { productId } = await request.json()
  
  const product = await env.DB.prepare(
    'SELECT * FROM products WHERE id = ?'
  ).bind(productId).first()
  
  // 加這一行，看看 product 到底是什麼
  console.log('product:', product)
  
  // 如果 product 是 null 或 undefined，下面這行就會報錯
  const price = product.price
}
```

### 在 Cloudflare Workers 看 console.log

Cloudflare Workers 的 `console.log` 不會出現在瀏覽器的開發者工具裡，要在 Cloudflare Dashboard 看：

**方法一：即時查看（開發時最方便）**

```bash
npx wrangler tail
```

跑這個指令之後，你的 Worker 的所有 log 都會即時顯示在終端機。

**方法二：Dashboard 查看**
Cloudflare Dashboard → Workers & Pages → 你的 Worker → Logs

-----

## 用 AI 調試的進階技巧

### 問 AI 加調試 log

```
這個函式跑起來結果不對，幫我在關鍵步驟加上 console.log，
讓我能追蹤每一步的值是什麼：

[貼上你的 code]
```

### 問 AI 解釋 stack trace

Stack trace 是錯誤發生時的「呼叫堆疊」，告訴你錯誤是從哪一層函式傳上來的。對初學者來說很難讀，直接貼給 AI 問：

```
幫我解釋這個 stack trace 在說什麼，錯誤是從哪裡開始的：

[貼上完整 stack trace]
```

### 問 AI 解釋你看不懂的 code

遇到看不懂的 code，不要跳過：

```
幫我解釋這段 code 在做什麼，用白話說，
特別是這幾行我不太理解：

[貼上 code]
```

-----

## 最常見的 Cloudflare Workers 錯誤

### `env is not defined` 或 `env.DB is not defined`

你在某個地方用了 `env`，但這個函式沒有接收 `env` 參數。

```typescript
// ❌ 這個函式拿不到 env
async function getProduct(productId: string) {
  const product = await env.DB.prepare(...) // env 從哪來？
}

// ✅ 把 env 傳進來
async function getProduct(productId: string, env: Env) {
  const product = await env.DB.prepare(...)
}
```

### `Cannot read properties of null`

資料庫查詢沒有找到資料，回傳了 `null`，你卻直接存取它的屬性。

```typescript
const product = await env.DB.prepare('SELECT * FROM products WHERE id = ?')
  .bind(productId).first()

// ❌ 如果 product 是 null，這行會報錯
const price = product.price

// ✅ 先確認有沒有找到
if (!product) {
  return Response.json({ error: 'Product not found' }, { status: 404 })
}
const price = product.price
```

### 部署成功但功能不正常

先確認你看的是最新版本——Cloudflare 有 cache，有時候改了 code 部署之後要等幾秒才生效。

用 curl 打你的 API 確認回傳是否正確：

```bash
curl -s https://your-app.com/api/products | jq
```

如果還是有問題，看 `wrangler tail` 的 log，找有沒有錯誤訊息。

-----

## 最直接的做法：截圖給 AI 看

有時候用文字描述問題很累——「頁面上有個東西位置不對」、「按鈕點了沒反應」、「錯誤訊息出現在奇怪的地方」。

**直接截圖，貼給 Cursor 裡的 AI 看。**

截圖可以包含：

- 瀏覽器裡看到的錯誤畫面
- 終端機的錯誤訊息（比複製文字更完整，顏色和格式都看得到）
- 和預期不符的 UI 結果
- Cloudflare Dashboard 的錯誤 log

告訴 AI：

```
這是我的問題，截圖如下：
[貼截圖]

我期望的結果是：[說明]
實際看到的是：[說明]
幫我找出問題在哪裡。
```

截圖比文字描述更準確，AI 能直接看到你看到的東西，不需要靠你翻譯。

特別有用的場景：

- **UI 跑版**：直接截圖，比描述「左邊有個東西跑到右邊」更清楚
- **終端機錯誤**：截圖包含顏色標示和完整 stack trace，比複製純文字更完整
- **Cloudflare log**：截圖整個 log 畫面，AI 能看到時間戳記和完整錯誤

-----

## 一個重要心態

遇到錯誤不代表你失敗了，代表你在學習。

每一個錯誤都有原因，每一個原因都能找到。你不需要自己搞懂所有細節——AI 是你的調試夥伴，把錯誤訊息和 code 貼給它，它能幫你找出大多數問題。

**遇到錯誤的正確流程：複製完整錯誤 → 貼給 AI → 理解原因 → 修好 → 繼續。**

不要慌，不要關掉，不要重試同樣的事期待不同結果。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
