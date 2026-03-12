# 為什麼 VibeFast 選擇 Cloudflare 全棧？

**更新：** 2026 年 3 月  
**閱讀時間：** 約 6 分鐘

-----

## 大多數模板的架構長這樣

前端部署在 Vercel，資料庫用 Supabase 或 PlanetScale，檔案儲存用 AWS S3，郵件服務用 SendGrid。

四個供應商，四個帳單，四個 dashboard，四個可能出問題的地方。

每個服務各自有自己的免費額度、自己的限制、自己的故障紀錄。某天早上你打開信箱，發現 Supabase 說你的連線數超過免費方案上限，或者 Vercel 說你的 bandwidth 用超了——你的 app 掛了，但你甚至不確定是哪個環節的問題。

VibeFast 的選擇是：**把所有東西都放在 Cloudflare 上。**

-----

## Cloudflare 全棧是什麼意思？

VibeFast 的每一層都跑在 Cloudflare 上：

|層級  |服務                |說明              |
|----|------------------|----------------|
|前端  |Remix on Workers  |頁面渲染、路由、用戶介面    |
|後端  |Cloudflare Workers|API 邏輯、身份驗證、業務處理|
|資料庫 |Cloudflare D1     |用戶資料、文章、訂單      |
|檔案儲存|Cloudflare R2     |圖片、媒體、上傳檔案      |

一個供應商，一個帳單，一個 dashboard。

-----

## 為什麼這對你很重要？

### 速度：你的 app 跑在離用戶最近的地方

傳統的伺服器放在某個固定的機房，例如美國東岸。台灣的用戶每次點一個按鈕，請求要跑到美國再跑回來，來回可能要 200–300ms。

Cloudflare 有 **300+ 個邊緣節點**，遍布全球。你的 code 部署之後，自動分散到所有節點。台灣的用戶連台灣的節點，德國的用戶連德國的節點，每個人都得到最快的回應。

而且 Cloudflare Workers 沒有「冷啟動」問題——傳統 serverless 函式第一次被呼叫時需要幾秒鐘啟動，Workers 隨時待命，延遲幾乎感覺不到。

-----

### 零 CORS：前後端直接對話，不走公開網路

這是 Cloudflare 特有的架構優勢，叫做 **Service Binding**。

一般的前後端架構：前端透過公開網路呼叫後端 API。這意味著你要設定 CORS（跨域請求的安全限制），要管理 API 金鑰的暴露問題，每次前端呼叫後端都要多一次網路往返。

VibeFast 的架構：前端 Worker 和後端 Worker 在 Cloudflare 內部直接溝通，不走公開網路。

```
用戶 → Cloudflare 邊緣節點
              ↓（內部，不走公網）
         前端 Worker ←→ 後端 Worker
                              ↓
                          D1 資料庫
```

結果：

- **零 CORS 設定**——這個問題從源頭消失
- **API 不暴露在外**——後端不需要公開 URL
- **更快的回應**——少一次網路往返

對 Vibe Coder 來說，CORS 錯誤是最常見的 debug 時間殺手之一。Service Binding 讓這個問題完全不存在。

-----

### 零出站費：R2 的最大優勢

如果你的 app 有圖片上傳、媒體檔案、用戶頭像，最常見的選擇是 AWS S3。

S3 本身不貴，但**出站流量費**是個坑。每次用戶下載一個圖片，AWS 都收出站費。流量越大、app 越成功，帳單越嚇人。很多 indie dev 在 app 成長之後才發現這個問題。

Cloudflare R2 的定價策略完全不同：**出站流量免費**。用戶下載多少次圖片，你都不需要為流量付費。

-----

### 免費額度夠用，從第一天就能上線

Cloudflare 的免費方案對 indie dev 非常友好：

|服務     |免費額度                      |
|-------|--------------------------|
|Workers|每天 10 萬次請求                |
|D1 資料庫 |5GB 儲存 + 每天 10 萬次讀取、5 萬次寫入|
|R2 儲存  |10GB + 每月 100 萬次操作        |

對大多數 app 的冷啟動階段，這些額度綽綽有餘。你可以從第一天就用正式環境測試，不需要付伺服器費用，等到有了真實用戶和收入再考慮升級方案。

vibefast.app 本身就是用這個 stack 跑的——用戶註冊、dashboard、訂單系統，前期完全在免費額度內。

-----

### 一個供應商，少掉很多不確定性

多個供應商組合在一起，除了帳單複雜，還有一個更隱形的問題：**服務之間的整合點是最容易出問題的地方。**

Vercel 和 Supabase 之間的連線超時？Supabase 和 AWS S3 的權限設定哪裡錯了？每個整合點都是一個潛在的故障點，而且出問題的時候，你要在兩個供應商的文件和 issue tracker 之間找原因。

Cloudflare 全棧的優點是：所有服務在同一個網路裡，官方支援它們之間的整合，文件在同一個地方，出問題也只需要找一個地方。

-----

## 和 Next.js + Vercel 的直接對比

|       |Next.js + Vercel 方案|VibeFast（Cloudflare）|
|-------|-------------------|--------------------|
|冷啟動    |有時明顯               |幾乎感覺不到              |
|CORS 設定|需要手動處理             |零設定，Service Binding |
|資料庫    |需要外部服務             |D1，邊緣原生             |
|檔案儲存   |需要外部服務             |R2，零出站費             |
|全球節點數  |有限                 |300+ PoPs           |
|供應商數量  |3–4 個              |1 個（Cloudflare）     |
|部署指令   |多步驟                |`npm run setup`     |
|免費起步   |有限制                |額度充足                |

-----

## 為什麼不是 Vercel Edge 或其他「edge-compatible」方案？

你可能看過一些方案說自己支援「edge 部署」。但 **edge-compatible（相容邊緣）** 和 **edge-native（原生邊緣）** 是兩件不同的事。

相容邊緣是說：你的 code 可以在邊緣跑，但架構本身不是為邊緣設計的。Next.js 的 App Router 在邊緣有諸多限制，很多 Node.js API 在 edge runtime 裡不能用，你要不斷查文件確認哪些功能在邊緣支援。

VibeFast 的整個 stack 從一開始就為 Cloudflare 的環境設計——Workers、D1、R2、Service Binding，全部是 Cloudflare 原生的概念，沒有相容性問題，也沒有「這個功能在 edge 不支援」的意外。

-----

## 總結

Cloudflare 全棧不只是技術選擇，是**降低複雜度**的選擇。

一個供應商取代四個，Service Binding 消除 CORS，R2 消除出站費，300+ 節點讓全球用戶都快——這些加在一起，讓你把更多時間放在產品本身，而不是基礎設施。

-----

想了解 VibeFast 的另一個核心設計決定？

👉 [為什麼用 Monorepo？](./why-monorepo-zh.md)

還沒買 VibeFast？  
👉 **[vibefast.app](https://vibefast.app)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
