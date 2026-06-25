# Vibe Coding 踩過的 5 個坑（和怎麼避開）

[English](../en/16-vibe-coding-common-mistakes-en.md) · [繁中](../zh/16-vibe-coding-common-mistakes-zh.md) · [Español](../es/16-vibe-coding-common-mistakes-es.md) · [日本語](../jp/16-vibe-coding-common-mistakes-jp.md) · [Português (BR)](../pt-br/16-vibe-coding-common-mistakes-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 8 分鐘

-----

## 一年多的 vibe coding，踩了不少坑

我從 2024 年底開始認真用 AI 工具做產品。vibefast.app 是這一年多累積下來的成果，但在這個過程裡，我踩過很多不必要的坑。

這篇是我希望一開始就有人告訴我的事。

-----

## 坑一：需求沒想清楚就丟給 AI

**症狀：** AI 生成了 code，你改了改，又改了改，改了五六次還是不對，最後花的時間比自己寫還多。

**根本原因：** 不是 AI 不好，是你的需求本身就不清楚。

AI 非常善於把模糊的需求變成「看起來能跑」但不是你要的東西。你說「幫我做一個登入功能」，AI 做出來了，但你想要的是「用 email 登入，不需要密碼，發一次性連結到信箱」——這兩個完全不同。

**怎麼避：** 在問 AI 之前，先用一兩句話把需求說清楚：

> 「我要做什麼、在哪個檔案、輸入是什麼、輸出是什麼、有什麼限制條件。」

花 5 分鐘想清楚需求，能省下 30 分鐘的來回修改。

-----

## 坑二：一次改太多東西

**症狀：** 你告訴 AI 「幫我加登入功能、用戶頭像上傳、email 通知」，AI 一次改了很多檔案，然後 app 壞掉了，你不知道是哪裡出問題。

**根本原因：** 同時改多個功能，任何一個出問題都很難定位。

**怎麼避：** 一次只做一件事。確認沒問題之後，commit 一次，再繼續下一個功能。

```bash
# 每完成一個功能就 commit
git add .
git commit -m "add low stock warning to product detail page"
```

這樣出問題的時候，你知道問題一定在最後一次 commit 裡，範圍大幅縮小。

-----

## 坑三：沒有本地測試就直接部署

**症狀：** `npm run deploy` 之後，打開正式環境，壞了。

**根本原因：** AI 生成的 code 不是每次都完美，有時候有語法錯誤，有時候邏輯有細微問題。

**怎麼避：** 每次改完，先跑 `npm run dev` 在本地測試，確認功能正常、沒有 console error，再 `npm run deploy`。

特別要測試的幾個點：

- 正常流程走一遍
- 邊界情況（空白輸入、最大值、特殊字元）
- 登入/未登入的狀態

-----

## 坑四：讓 AI 做安全相關的決定

**症狀：** 你問 AI「怎麼儲存用戶密碼」，AI 給了一個方案，你直接用了，結果是明文存密碼或用不安全的 MD5 hash。

**根本原因：** AI 有時候會給出「能跑但不安全」的方案，因為它在優先解決你的功能需求，不一定主動考慮安全性。

**怎麼避：** 安全相關的決定要主動問 AI：「這個方案安全嗎？有什麼潛在風險？」

密碼儲存：一定要用 bcrypt 或 argon2，不要用 MD5 或 SHA1。
JWT secret：一定要用夠長的隨機字串，不要用簡單的字串。
SQL 查詢：一定要用參數化查詢，不要用字串拼接。

這些在資安篇有更完整的說明。

-----

## 坑五：不看 AI 生成的 code，直接貼上去

**症狀：** App 跑起來了，但幾週後你發現有一個功能行為很奇怪，去看 code，完全看不懂那段在做什麼，也不知道怎麼改。

**根本原因：** 你把 AI 當成黑盒子，code 進去，功能出來，但你對中間發生什麼完全不了解。

**怎麼避：** 不需要看懂每一行，但關鍵邏輯要問清楚。

直接在 Cursor 裡問：

> 「解釋一下這段 code 在做什麼，用白話說」

AI 會解釋，你把解釋記在注釋裡：

```typescript
// 庫存 <= 3 時顯示低庫存警示
// isOutOfStock: stock 為 0，按鈕 disabled
// isLowStock: stock 在 1-3 之間，顯示「僅剩 X 件」
const isOutOfStock = product.stock === 0
const isLowStock = product.stock > 0 && product.stock <= 3
```

這樣幾個月後再回來看，你能快速想起這段在做什麼。

-----

## 一個整體建議

Vibe Coding 讓開發速度大幅提升，但速度帶來的副作用是：你很容易在沒有理解的情況下堆疊功能，直到某天整個 app 變成一個你不敢碰的黑盒子。

**解法不是慢下來，而是建立幾個基本習慣：**

1. 需求想清楚再問 AI
1. 一次一件事，commit 頻繁
1. 本地測試後再部署
1. 安全相關的決定主動確認
1. 關鍵 code 問清楚再繼續

這五個習慣不會讓你慢，但會讓你少踩很多不必要的坑。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 8 月 1 日漲至 $199。
