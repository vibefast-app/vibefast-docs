# Changelog

[English](../en/changelog.md) | [返回索引](./index-zh.md)

所有重要的版本更新都記錄在這裡。格式參考 [Keep a Changelog](https://keepachangelog.com/zh-TW/1.0.0/)。

-----

## [v1.0.0] — 2026 年 3 月

### 初始發佈 🎉

VibeFast 正式對外發售。這是第一個公開版本，也是 [vibefast.app](https://vibefast.app) 本身運行的版本。

**核心架構**

- Remix 前端，跑在 Cloudflare Workers 上
- Cloudflare Workers API（TypeScript）
- Cloudflare D1 資料庫，首次部署自動建立 schema
- Cloudflare R2 物件儲存
- Service Binding — 前後端零 CORS 內部溝通
- Bearer JWT 身份驗證，secret 自動生成
- Turborepo Monorepo — 前端、API、共用套件統一管理

**內建模組**

- 用戶 Auth — 註冊、登入、登出、路由保護
- Admin 後台 — 管理用戶、內容、訂單
- Blog CMS — 建立、編輯、發布文章，支援分類
- 媒體庫 — 透過 R2 上傳和管理檔案
- Stripe 一次性收款 — 數位產品結帳，Webhook 預先設定
- Resend 交易型郵件 — 購買確認信、管理員通知信
- 內建頁面瀏覽統計 — 不依賴第三方服務
- 一鍵 setup 腳本 — `npm run setup` 處理所有初始化和部署

**買家文件**

- Quickstart 指南
- Rebrand 指南
- Required Secrets 參考
- Stripe & Resend 設定指南
- 模組移除指南
- Deployment 指南
- API 參考文件

-----

*更新記錄持續維護中。每次版本更新都會在這裡記錄變更內容。*

*購買 VibeFast 即可獲得所有未來更新：[vibefast.app](https://vibefast.app)*
