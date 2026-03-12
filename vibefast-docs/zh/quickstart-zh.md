# VibeFast 快速上手指南

**更新：** 2026 年 3 月  
**閱讀時間：** 約 5 分鐘

-----

## 從 clone 到上線，只需要三個指令

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

就這樣。

`npm run setup` 是整個 VibeFast 體驗的核心——它在背後自動完成了所有你不想手動處理的事：

- 登入 Cloudflare，確認帳號設定
- 建立 D1 資料庫，執行 bootstrap SQL，自動建好所有資料表
- 生成 JWT secret，寫入 Workers 環境變數
- 把前端（Remix）和後端（Workers API）同時部署到正式環境

終端機跑完之後，你會看到兩個上線的 URL——一個前端、一個後端 API。你的 app 已經跑在 Cloudflare 全球 300+ 個節點上了。

-----

## 環境需求

開始之前確認你有：

- **Node.js 20+**
- **npm 10+**
- **Cloudflare 帳號**（免費就夠）
- macOS 用戶：需要安裝 `jq`（`brew install jq`）

沒有 Cloudflare 帳號？[點這裡免費註冊](https://dash.cloudflare.com/sign-up)，不需要綁信用卡。

-----

## 第一個小時你能做到什麼

VibeFast 設計的目標是：**買家在第一個小時內就能完成從上線到客製化的完整流程。**

### 0–10 分鐘：安裝和一鍵部署

```bash
npm install
npm run setup
```

跑完之後你會有：

- 一個跑在 Cloudflare 上的完整 web app
- 已建立的 D1 資料庫（用戶、文章、訂單表都建好了）
- 前後端 Workers 都在正式環境上線
- 一個可以打開的 URL

### 10–15 分鐘：本地開發

```bash
npm run dev
```

一個指令啟動前端和後端。打開終端機印出的本地 URL，你會看到：

- 完整的行銷首頁
- 定價頁面
- Blog 系統
- 用戶註冊和登入流程
- Admin 後台入口

這不是 demo 頁面，是真實可用的功能，每一個都接好了，可以直接使用。

### 15–40 分鐘：設定 Stripe、Resend，完成品牌替換

把你的 Stripe API key 和 Resend API key 填入設定，跑一次 `npm run deploy`，然後：

1. 用你設定的 admin 信箱註冊帳號
1. 打開 `/admin` 確認後台可以進入
1. 跑一次 Stripe 測試付款，確認 Webhook 收到
1. 確認購買確認信和管理員通知信都有發出

整個端到端流程跑通，你的 app 就準備好了。

品牌替換也很直接——VibeFast 把所有需要改的文字集中在一個設定檔裡：網站名稱、域名、定價文案、首頁文案、SEO 設定，改完跑 `npm run deploy`，全部更新。

-----

## 部署指令總覽

|指令                       |用途                               |
|-------------------------|---------------------------------|
|`npm run setup`          |首次設定，建立資料庫、生成 secret、部署所有 Workers|
|`npm run dev`            |啟動本地開發環境（前後端同時）                  |
|`npm run deploy`         |部署到正式環境（前後端同時）                   |
|`npm run deploy:frontend`|只部署前端                            |
|`npm run deploy:backend` |只部署後端                            |
|`npm run build`          |建構所有套件                           |
|`npm run typecheck`      |全專案 TypeScript 型別檢查              |

-----

## 這個 stack 跑起來之後長什麼樣子？

不需要相信文字描述。

[vibefast.app](https://vibefast.app) 這個網站本身就是用 VibeFast 建的——行銷首頁、blog、定價頁面、用戶登入、dashboard，全部都是。

**免費註冊一個帳號**，登入後你會看到：

- 過去 7 天的真實流量數據
- 你是第幾個註冊用戶

你體驗到的 auth 流程、dashboard UI、頁面速度，就是你買到的東西。不是 demo，是真實的正式環境。

-----

## 想深入了解架構？

- [為什麼選擇 Cloudflare 全棧？](./why-cloudflare-fullstack-zh.md) — 和 Next.js + Vercel 方案的直接對比
- [為什麼用 Monorepo？](./why-monorepo-zh.md) — Turborepo 架構的實際好處

-----

## 準備好了嗎？

**早鳥價 $99 — 2026 年 6 月 1 日漲至 $199**  
一次付款，終身存取，Private GitHub repo，所有未來更新全包。

👉 **[vibefast.app](https://vibefast.app)**
