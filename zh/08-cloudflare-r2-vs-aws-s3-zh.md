# R2 vs S3：為什麼我不用 AWS 存圖片

[English](../en/08-cloudflare-r2-vs-aws-s3-en.md) · [繁中](../zh/08-cloudflare-r2-vs-aws-s3-zh.md) · [Español](../es/08-cloudflare-r2-vs-aws-s3-es.md) · [日本語](../jp/08-cloudflare-r2-vs-aws-s3-jp.md) · [Português (BR)](../pt-br/08-cloudflare-r2-vs-aws-s3-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 6 分鐘

-----

## 你的 app 需要存圖片嗎？

用戶上傳頭像、產品截圖、部落格封面圖——任何有媒體上傳需求的 app 都需要一個地方存這些檔案。

資料庫不適合存圖片（資料庫是存結構化文字資料的），你需要專門的「物件儲存」服務。

最常見的選擇是 **AWS S3**。但如果你的 app 跑在 Cloudflare 上，有一個更好的選擇：**Cloudflare R2**。

-----

## S3 的隱藏成本：出站費

AWS S3 的定價有一個讓很多人踩坑的地方：**出站費（egress fee）**。

- 存圖片到 S3：收儲存費（每 GB 約 $0.023/月）
- 用戶下載圖片：**收出站費（每 GB 約 $0.09）**

聽起來不多？算一下：

假設你的 app 有 1,000 個用戶，每個用戶每天平均下載 10 張圖片，每張 500KB：

```
每天出站流量 = 1,000 × 10 × 0.5MB = 5,000MB = 5GB
每天出站費 = 5GB × $0.09 = $0.45
每月出站費 = $0.45 × 30 = $13.5
```

你的 app 越成功，帳單越高。有些公司在 app 爆紅之後才發現 S3 出站費已經佔了大頭。

-----

## R2 的定價邏輯

Cloudflare R2 的定價策略完全不同：

|費用項目|AWS S3     |Cloudflare R2      |
|----|-----------|-------------------|
|儲存費 |$0.023/GB/月|$0.015/GB/月        |
|出站費 |$0.09/GB   |**$0 — 完全免費**      |
|免費額度|有限         |10GB 儲存 + 100萬次操作/月|

出站免費這一點讓 R2 對高流量 app 的優勢非常明顯。你的圖片被下載越多次，和 S3 的費用差距越大。

-----

## R2 和 S3 的技術差異

|            |AWS S3   |Cloudflare R2         |
|------------|---------|----------------------|
|API 相容性     |S3 原生 API|相容 S3 API             |
|全球分布        |需要手動設定多區域|自動全球分布                |
|和 Workers 整合|需要跨網路呼叫  |直接 Binding，不走公網       |
|CORS 設定     |需要手動設定   |透過 Workers 處理，可以零 CORS|

**S3 API 相容**這一點很重要——大多數支援 S3 的套件（如 `@aws-sdk/client-s3`）都可以直接用來操作 R2，只需要換掉 endpoint URL。這讓遷移成本很低。

-----

## 在 Cloudflare Workers 裡使用 R2

上傳和讀取的基本模式：

```typescript
// 上傳圖片到 R2
await env.R2.put(`images/${filename}`, fileBuffer, {
  httpMetadata: { contentType: file.type }
})

// 透過 Workers 回傳 R2 的檔案
const object = await env.R2.get(`images/${filename}`)
if (!object) return new Response("Not found", { status: 404 })

return new Response(object.body, {
  headers: { "Content-Type": object.httpMetadata?.contentType || "application/octet-stream" }
})
```

透過 Workers 代理存取 R2（而不是直接暴露公開 URL），讓你可以在 Workers 裡加入任何邏輯：驗證用戶是否有權限、記錄下載次數、加入快取控制等。使用 vibefast.app 的話，R2 的 binding 設定已經處理好，直接在 Worker 裡用 `env.R2` 就能存取。

-----

## 什麼時候考慮 S3？

R2 幾乎在所有面向都對 Cloudflare 生態的 app 更合適，但有幾個情況你可能還是需要 S3：

- 你需要 S3 的進階功能（如 Intelligent Tiering、Glacier 冷儲存）
- 你的 app 不在 Cloudflare 上，R2 的優勢就不那麼明顯
- 你需要和已有 S3 深度整合的服務搭配使用

對大多數跑在 Cloudflare 上的 indie app，R2 是更自然的選擇。

-----

## 總結

如果你的 app 跑在 Cloudflare 上，用 R2 存圖片和媒體檔案比 S3 更划算：

- 出站費免費，流量越大越省
- 和 Workers 直接 Binding，不走公網
- 免費額度夠大多數 app 起步用
- S3 API 相容，遷移成本低

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 8 月 1 日漲至 $199。
