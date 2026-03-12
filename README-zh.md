# VibeFast 🚀

> **完全基於 Cloudflare 的 edge-native web app 模板。**  
> Remix · Workers · D1 · R2 · 不用 Next.js · 不用 Vercel · 不用 AWS

[![License](https://img.shields.io/badge/License-Commercial-FF6B2B)](https://vibefast.app)
[![Remix](https://img.shields.io/badge/Frontend-Remix-blue)](https://remix.run)
[![Cloudflare Workers](https://img.shields.io/badge/Backend-CF%20Workers-orange)](https://workers.cloudflare.com)
[![D1](https://img.shields.io/badge/Database-D1-green)](https://developers.cloudflare.com/d1)
[![R2](https://img.shields.io/badge/Storage-R2-yellow)](https://developers.cloudflare.com/r2)
[![Turborepo](https://img.shields.io/badge/Monorepo-Turborepo-purple)](https://turbo.build)

[![Deploy Status](https://img.shields.io/badge/Deploy-Success-brightgreen)](https://vibefast.app)
[![Frontend](https://img.shields.io/badge/Frontend-Remix-blue)](https://remix.run)
[![Backend](https://img.shields.io/badge/Backend-Cloudflare%20Workers-orange)](https://workers.cloudflare.com)
[![Architecture](https://img.shields.io/badge/Architecture-Monorepo-purple)](https://turbo.build)
[![Payments](https://img.shields.io/badge/Payments-Stripe-635bff)](https://stripe.com)
[![Email](https://img.shields.io/badge/Email-Resend-111827)](https://resend.com)

[English README](./README.md) | [線上 Demo](https://vibefast.app) | [立即購買 — 早鳥 $99](https://vibefast.app/#pricing)（6 月 1 日漲價）

-----

## 3 個指令，上線你的 App

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

就這樣。`npm run setup` 自動處理所有事情：

- Cloudflare 登入
- D1 資料庫建立 + bootstrap SQL
- JWT secret 自動生成
- 前後端兩個 Workers 同時部署到正式環境

你的 app 上線了。跑在 Cloudflare 全球 300+ 個節點上，冷啟動幾乎感覺不到。

-----

## 包含什麼

所有你需要的東西，全部接好、隨時可用。

### 核心架構

- ✅ **Remix** 前端跑在 Cloudflare Workers — SSR、巢狀路由、Web 標準
- ✅ **Cloudflare Workers API** — TypeScript 後端，在邊緣執行
- ✅ **Cloudflare D1** — SQLite 資料庫，首次部署自動建立 schema
- ✅ **Cloudflare R2** — S3 相容儲存，零出站費用
- ✅ **Service Binding** — 前後端內部溝通，零 CORS 設定
- ✅ **Bearer JWT Auth** — 無狀態身份驗證，secret 自動生成
- ✅ **Turborepo Monorepo** — 前端、API、共用套件在同一個 repo

### 內建模組

- ✅ **用戶 Auth** — 註冊、登入、登出、路由保護
- ✅ **Admin 後台** — 管理用戶、內容、訂單
- ✅ **Blog CMS** — 建立、編輯、發布文章，支援分類
- ✅ **媒體庫** — 透過 R2 上傳和管理檔案
- ✅ **Stripe 收款** — 數位產品一次性結帳，Webhook 預先設定好
- ✅ **Resend 郵件** — 交易型郵件（購買確認、管理員通知）
- ✅ **頁面瀏覽統計** — 內建流量追蹤，不需要第三方服務
- ✅ **一鍵 setup** — `npm run setup` 自動完成所有設定和部署
- ✅ **所有未來更新** — 終身存取，private repo

-----

## 購買 VibeFast

**早鳥價 $99** — 2026 年 6 月 1 日漲至 $199。

一次付款，終身存取，Private GitHub repo，所有未來更新全包。

👉 **[vibefast.app](https://vibefast.app)**

這個網站本身就是用 VibeFast 建的。免費註冊體驗真實 dashboard——包含過去 7 天的流量數據、你的註冊序號——再決定要不要買。你看到的就是你買到的。

-----

## 免費 Vibe Coding 教程

剛接觸 vibe coding 或 Cloudflare？從這裡開始。

|# |文章                                                                                         |
|--|-------------------------------------------------------------------------------------------|
|01|[什麼是 Vibe Coding？入門概念全解析](./vibe-coding-docs/zh/01-what-is-vibecoding-zh.md)            |
|02|[在 Cloudflare 上 VibeCoding 的最佳方式](./vibe-coding-docs/zh/02-the-best-way-to-vibecoding-on-cloudflare-zh.md)|

持續更新中。⭐ Star 追蹤最新內容。

-----

## 關於作者

**Danko Peng** — 50 歲的 solopreneur，用 AI + Cloudflare 全棧 build-in-public。

- X：[@dankopeng](https://x.com/dankopeng)
- 產品：[vibefast.app](https://vibefast.app)

-----

## 授權

模板原始碼：商業授權——詳見 [LICENSE](./LICENSE)。  
`/vibe-coding-docs` 教程內容：MIT 授權。
