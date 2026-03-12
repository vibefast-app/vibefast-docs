# 為什麼 VibeFast 用 Monorepo？

**更新：** 2026 年 3 月  
**閱讀時間：** 約 5 分鐘

-----

## 先說一個你可能熟悉的痛苦

你開始做一個新 app。

前端是一個 repo，後端是另一個 repo。兩個各自有自己的 `package.json`、自己的部署流程、自己的環境變數設定。

三個月後，前端多了幾個新的 API 欄位，後端也改了幾個介面。然後你發現——兩邊的資料格式悄悄對不上了，但沒有任何錯誤訊息告訴你，直到某個用戶回報說頁面壞了。

這是前後端分開管理的經典問題：**兩邊各自演化，沒有強制同步的機制。**

Monorepo 就是解決這個問題的。

-----

## 什麼是 Monorepo？

**Monorepo**（單一倉庫）是把一個專案裡所有相關的 code 放在同一個 GitHub repo 裡統一管理。

VibeFast 的結構長這樣：

```
vibefast/
├── apps/
│   ├── web/        ← Remix 前端
│   └── api/        ← Cloudflare Workers 後端
├── packages/
│   ├── ui/         ← 共用 UI 元件
│   └── config/     ← 共用設定和型別定義
└── ...
```

前端、後端、共用元件——全部在同一個地方。

-----

## 對 Vibe Coder 來說，這意味著什麼？

### 一個指令啟動所有東西

傳統多 repo 的開發流程：

1. 打開終端機，進入後端 repo，跑 `npm run dev`
1. 再開一個終端機，進入前端 repo，跑 `npm run dev`
1. 兩邊的 port 和設定還要確認對得上

VibeFast 的開發流程：

```bash
npm run dev
```

一個指令，前端和後端同時啟動，同時監聽變更，同時熱更新。

部署也一樣：

```bash
npm run deploy
```

前後端一起上線，不需要記得先部署哪個、後部署哪個。

-----

### 型別錯誤當場抓到，不是上線後才發現

這是 Monorepo 最實際的好處之一。

VibeFast 的前後端共用同一套 TypeScript 型別定義，放在 `packages/config` 裡。假設你的 API 回傳一個用戶物件：

```typescript
// packages/config/src/types.ts
export type User = {
  id: number
  email: string
  createdAt: string
}
```

前端和後端都 import 同一個 `User` 型別。如果你在後端把 `createdAt` 改成 `created_at`，前端所有用到這個欄位的地方**馬上報錯**，在你的編輯器裡，在你按下儲存的那一刻。

不是在 code review，不是在測試，不是在用戶回報之後。

對 Vibe Coder 來說，這個機制特別重要——因為 AI 生成的 code 很容易在前後端之間製造這種細微的不一致，有型別保護就能即時發現。

-----

### AI 工具看得到整個專案

這一點很少人提，但影響很大。

當你用 Cursor 或 Claude 幫你改 code，如果前後端在不同 repo，你每次都要說明「這是前端的邏輯，後端在另一個 repo 長這樣……」。AI 沒辦法同時看到兩邊，只能靠你描述。

Monorepo 裡，整個專案在同一個地方。你可以直接說「幫我在 API 新增一個端點，然後在前端的這個頁面呼叫它」，Cursor 或 Claude 同時看到前後端，生成的 code 直接對應，不需要你在兩個視窗之間複製貼上。

**Monorepo + AI 工具，是讓 40 分鐘上線成為可能的組合之一。**

-----

### 建構速度不會因為專案變大而變慢

VibeFast 用 **Turborepo** 管理 Monorepo 的建構流程。

Turborepo 有一個聰明的機制：它會記住每個套件上次建構的結果。如果你只改了前端的 code，下次建構時後端不會重新編譯——Turborepo 直接用快取的結果。

對一個小專案來說感覺不明顯，但隨著你加更多模組、更多套件，這個差距會越來越大。VibeFast 設計上考慮了你的專案會成長，不只是跑起來就好。

-----

## 對比：多 Repo vs Monorepo

|       |多個分散 Repo      |VibeFast Monorepo|
|-------|---------------|-----------------|
|啟動開發環境 |多個終端機，多個指令     |`npm run dev`    |
|部署     |記得順序，分開執行      |`npm run deploy` |
|前後端型別同步|手動維護，容易脫節      |自動，改一處全部更新       |
|AI 工具輔助|需要在多個 repo 之間切換|整個專案一次看到         |
|建構速度   |每次全部重建         |Turborepo 增量快取   |

-----

## 總結

Monorepo 不是一個時髦的技術選擇，它解決的是真實的開發摩擦：**前後端分開管理帶來的同步問題、上下文切換成本、以及 AI 工具無法同時看到整個專案的限制。**

VibeFast 選擇 Monorepo，是因為這是讓 vibe coding 真正順暢的基礎架構決定。

-----

想深入了解 VibeFast 的另一個核心設計決定？

👉 [為什麼選擇 Cloudflare 全棧？](./why-cloudflare-fullstack-zh.md)

還沒買 VibeFast？  
👉 **[vibefast.app](https://vibefast.app)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
