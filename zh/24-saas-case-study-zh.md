# 實戰案例：用 VibeFast 做一個 SaaS 產品

[English](../en/24-saas-case-study-en.md) · [繁中](../zh/24-saas-case-study-zh.md) · [Español](../es/24-saas-case-study-es.md) · [日本語](../jp/24-saas-case-study-jp.md) · [Português (BR)](../pt-br/24-saas-case-study-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 10 分鐘

-----

## 假設：你買了 VibeFast，想做一個 SaaS

這篇不是介紹 VibeFast 有什麼功能——那在 quickstart 文件裡都有。

這篇是展示一個真實的流程：**從有一個 SaaS 想法，到用 VibeFast 把它跑起來，整個過程是什麼樣子。**

用的例子是：一個幫 solopreneur 追蹤客戶發票的 SaaS——**InvoiceTrack**。功能很簡單：建立發票、記錄付款狀態、到期自動提醒。

-----

## 第一步：clone 之後先跑起來

買了 VibeFast 之後，你會拿到 private repo 的 collaborator 邀請。接受邀請，clone 下來：

```bash
git clone https://github.com/vibefast-app/vibefast.git invoicetrack
cd invoicetrack
npm install
```

按照 quickstart 文件跑 setup，幾分鐘後你有一個完整跑起來的 app，包含登入、付款流程、admin 後台——但都是 VibeFast 的預設內容。

**在這個基礎上開始改，比從零開始快十倍。**

-----

## 第二步：寫需求分析書

在動任何 code 之前，先讓 AI 幫你整理需求（上一篇工作計劃篇有說過這個流程）：

```
我想用 VibeFast 做一個幫 solopreneur 管理發票的 SaaS，
叫 InvoiceTrack。

核心功能（MVP）：
1. 用戶可以建立客戶資料（公司名、聯絡人、email）
2. 可以對客戶建立發票（項目、金額、到期日）
3. 記錄發票狀態（草稿、已發送、已付款、逾期）
4. 到期前 3 天自動發提醒 email
5. Dashboard 顯示未收款總額

技術基礎：VibeFast（已有 Auth、Stripe、Resend 整合）
這個版本不做：發票 PDF 匯出、多幣別、子帳號
```

-----

## 第三步：設計資料庫結構

需求清楚之後，讓 AI 設計資料表：

```
先不要動手。

基於這個需求，幫我設計 D1 的資料表結構：
- clients（客戶）
- invoices（發票）
- invoice_items（發票項目）

每個資料表需要哪些欄位？表之間的關係是什麼？
```

AI 會給你一份 schema，你逐條確認，不清楚的問清楚，不喜歡的改掉，滿意之後再請 AI 生成 SQL 建立資料表。

-----

## 第四步：一個功能一個功能做

確認了資料結構，開始按 Layer 1 → Layer 2 → Layer 3 的順序做：

**Layer 1 — 地基**

```
□ 建立 clients、invoices、invoice_items 資料表
□ 確認 VibeFast 的 auth 可以保護所有新的路由
```

**Layer 2 — 核心功能**

```
□ 客戶管理（新增、編輯、列表）
□ 發票建立（選客戶、加項目、設到期日）
□ 發票狀態更新
□ Dashboard 顯示未收款總額
□ 到期提醒 email（用 Cron Trigger 定時跑）
```

**Layer 3 — 完善**

```
□ 發票頁面設計優化
□ Email 樣板美化
□ 搜尋和篩選功能
```

每做一個功能，都是「先討論 → Plan 模式確認 → 實作 → 本地測試 → commit」的循環。

-----

## 第五步：修改品牌

VibeFast 的預設 UI 是你的起點，不是終點。改成 InvoiceTrack 的品牌：

告訴 AI：

```
幫我把 VibeFast 預設的品牌換成 InvoiceTrack。
包含：
- 網站標題和 meta title
- Logo 文字（先用文字，之後再換圖片）
- 主色調從橙色改成藍色（blue-600）
- 首頁的 hero 文字
```

品牌修改通常一個小時內搞定。

-----

## 第六步：設定 Stripe 收費

InvoiceTrack 的定價：每月 $9，最多管理 50 張發票。

VibeFast 已經整合了 Stripe，你只需要：

1. 在 Stripe Dashboard 建立一個 $9/月的訂閱商品
1. 把 Stripe Price ID 更新到環境變數
1. 告訴 AI「幫我修改訂閱邏輯，讓未付費用戶最多只能建立 3 張發票，引導他們升級」

付款流程本身不需要重寫，VibeFast 的 Stripe 整合直接可用。

-----

## 從想法到第一個用戶：時間表

這個案例從 clone 到有第一個付費用戶，實際用了多久？

```
第 1 天：clone、setup、熟悉專案結構
第 2-3 天：資料庫設計、Layer 1 地基
第 4-7 天：Layer 2 核心功能
第 8-9 天：品牌修改、Stripe 設定
第 10 天：測試、修 bug、部署
第 11 天：上線，開始推廣
```

十一天從零到上線。不是因為我很厲害，而是因為 VibeFast 把 auth、付款、部署這些最花時間的事都做好了，讓我可以把所有精力放在 InvoiceTrack 的核心功能上。

-----

## 關鍵心得

**不要一次做太多。** 發票 PDF 匯出、多幣別支援——這些都很好，但都不是第一版需要的。先做能解決核心問題的最小版本，上線，有用戶回饋之後再迭代。

**auth 和付款是最難做的部分。** 如果沒有 VibeFast，光是把這兩個做好可能要兩三週。有了模板，這兩個直接跳過，直接做產品核心。

**每天 commit。** 每天結束工作前 commit 一次，哪怕只是做了一小部分。這讓你有回頭的餘地，也讓你清楚每天的進度。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
