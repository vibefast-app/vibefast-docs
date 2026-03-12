# 常見問題

[English](../en/faq.md) | [返回索引](./index-zh.md)

-----

## 關於產品

### VibeFast 是開源的嗎？

不是。這是一個商業產品，公開倉庫只承擔介紹與學習資源入口的角色。

### 公開倉庫和 private repo 的差別是什麼？

公開倉庫提供產品介紹與 Vibe Coding 學習內容；private repo 承載實際的完整原始碼與買家文件。

### 我可以把 VibeFast 用在自己的產品上嗎？

可以，這正是它的主要用途。多數買家會把它當作品牌重塑的起點，或直接作為自己 SaaS / web app 的基礎架構。

### 我一定要保留所有預設模組嗎？

不一定。VibeFast 提供的是完整參考產品，你可以依照自己的需求保留、調整或移除模組。例如不需要 blog 的話可以直接刪除，不影響其他功能。

### 這裡會公開所有技術細節嗎？

不會。這裡只提供公開層級的介紹與教學，完整的架構說明、部署指南、API 文件都在 private repo 裡。

-----

## 關於購買

### 怎麼購買？付款之後如何取得 code？

前往 [vibefast.app](https://vibefast.app) 完成 Stripe 付款後，系統會自動發送 GitHub 邀請到你的信箱，接受邀請即可存取 private repo。整個流程全自動，通常在幾分鐘內完成。

### 早鳥價是限時優惠嗎？

是的，限時優惠。2026 年 6 月 1 日前維持 $99，之後漲至 $199。現在買和 6 月後買，差 $100。

### 一次付款包含什麼？

$99 包含：private repo 存取權限、完整原始碼、所有買家文件，以及所有未來的模板更新。沒有月費，沒有隱藏費用。

### 有退款政策嗎？

因為這是數位產品，付款後即可存取原始碼，原則上不提供退款。如果購買後遇到技術問題，請先聯絡 [hello@dankoai.com](mailto:hello@dankoai.com)，我們會盡力協助解決。

-----

## 關於技術

### 我需要多少技術背景才能使用 VibeFast？

能夠閱讀和理解本repo的教學內容即可。VibeFast 設計上對 Vibe Coder 友好——配合 Cursor 或 Claude 等 AI 工具，沒有深厚工程背景的人也能上手。如果你能跑 `npm install` 並且看懂 AI 生成的代碼，就足夠了。

### 需要有 Cloudflare 付費帳號嗎？

不需要。Cloudflare 的免費方案對大多數 app 的起步階段完全夠用——Workers 每天 10 萬次請求、D1 每天 10 萬次讀取、R2 每月 100 萬次操作。等到你的 app 真的有大量流量，再考慮升級方案。

### VibeFast 之後會支援其他 stack 嗎？

目前專注在 Cloudflare 全棧（Remix + Workers + D1 + R2），這是 VibeFast 的核心定位。支援其他 stack 不在目前的 Roadmap 上。

### 我買了之後可以用在多個專案上嗎？

是的，目前的授權支持多專案授權。你可以自由在多個專案使用。

-----

## 關於支援

### 買了之後如果有問題，可以找誰？

可以透過以下方式聯絡：

- 信箱：[hello@dankoai.com](mailto:hello@dankoai.com)
- X：[@dankopeng](https://x.com/dankopeng)

### 我想先了解 Vibe Coding 的方法論，從哪裡開始？

從這裡開始：

- [什麼是 Vibe Coding？](../../vibe-coding-docs/zh/01-what-is-vibecoding-zh.md) — 核心概念與關鍵術語
- [在 Cloudflare 上 VibeCoding 的最佳方式](../../vibe-coding-docs/zh/02-the-best-way-to-vibecoding-on-cloudflare-zh.md) — 實戰介紹

-----

還有其他問題？歡迎在 [GitHub Issues](https://github.com/vibefast-app/vibefast-docs/issues) 提問，或直接聯絡 [@dankopeng](https://x.com/dankopeng)。
