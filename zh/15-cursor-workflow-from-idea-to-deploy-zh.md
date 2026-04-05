# 我怎麼用 Cursor 把一個功能從想法變成上線

[English](../en/15-cursor-workflow-from-idea-to-deploy-en.md) · [繁中](../zh/15-cursor-workflow-from-idea-to-deploy-zh.md) · [Español](../es/15-cursor-workflow-from-idea-to-deploy-es.md) · [日本語](../jp/15-cursor-workflow-from-idea-to-deploy-jp.md) · [Português (BR)](../pt-br/15-cursor-workflow-from-idea-to-deploy-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 10 分鐘

-----

## 一個真實的功能開發過程

我用一個具體的例子來示範：**幫手工皂電商網站加上「庫存不足警示」功能**——商品詳情頁在庫存剩 3 件以下時，顯示「僅剩 X 件」的紅色提示。

從想法到上線，整個過程大概 20 分鐘。

-----

## 第一步：把需求說清楚（5 分鐘）

很多人用 AI 工具的最大問題是：需求說得不夠清楚，AI 給的結果一直跑偏，來回改幾次之後比自己寫還累。

在開始之前，先想清楚三件事：

**我要什麼：** 在商品詳情頁，當庫存 <= 3 時，在加入購物車按鈕上方顯示「僅剩 X 件」，用紅色小字。

**在哪裡改：** 商品詳情頁的 route 檔案（`/shop/:slug`）

**判斷邏輯：** 從 API 回傳的 `stock` 欄位判斷，<= 3 才顯示，0 時顯示「已售完」並把按鈕 disable。

-----

## 第二步：告訴 Cursor（3 分鐘）

打開 Cursor，按 `Cmd+L` 開啟對話框，輸入：

> 「在商品詳情頁（`/shop/:slug`），當商品的 `stock` 欄位 <= 3 時，在加入購物車按鈕上方顯示「僅剩 X 件」紅色小字。stock 為 0 時顯示「已售完」，並把按鈕設為 disabled。用 Tailwind CSS。」

Cursor 看到整個專案的結構，知道這個檔案在哪裡，生成的 code 直接能用。

-----

## 第三步：看 AI 生成的 code（2 分鐘）

Cursor 生成了這樣的東西：

```typescript
// 庫存狀態判斷
const isOutOfStock = product.stock === 0
const isLowStock = product.stock > 0 && product.stock <= 3
```

```tsx
{/* 庫存提示 */}
{isLowStock && (
  <p className="text-sm text-red-500 mb-2">
    僅剩 {product.stock} 件
  </p>
)}
{isOutOfStock && (
  <p className="text-sm text-red-500 mb-2">已售完</p>
)}

{/* 加入購物車按鈕 */}
<button
  disabled={isOutOfStock}
  className={`w-full py-3 rounded-lg font-semibold ${
    isOutOfStock
      ? 'bg-zinc-300 text-zinc-500 cursor-not-allowed'
      : 'bg-orange-500 text-white hover:bg-orange-400'
  }`}
>
  {isOutOfStock ? '已售完' : '加入購物車'}
</button>
```

看起來對，邏輯清楚，沒有明顯問題。

-----

## 第四步：在本地測試（5 分鐘）

```bash
npm run dev
```

打開商品詳情頁，確認：

- 庫存 > 3：正常顯示按鈕，無提示
- 庫存 <= 3：紅色「僅剩 X 件」出現
- 庫存 = 0：顯示「已售完」，按鈕變灰色無法點擊

有時候 AI 生成的樣式和預期有點落差。我直接告訴 Cursor：「已售完按鈕的灰色太淡，改成 zinc-400」，它直接修改。

-----

## 第五步：部署（2 分鐘）

```bash
npm run deploy
```

等待部署完成，打開正式環境 URL 確認功能上線。

整個流程：**想法 → 上線，20 分鐘。**

-----

## 讓 AI 工具更有效的幾個習慣

從我這一年多的 vibe coding 經驗裡，幾個讓 AI 輸出品質更好的習慣：

**說明「在哪個檔案」**
不要只說「幫我加一個庫存警示」，要說「在商品詳情頁的 route 加，檔案是 `shop.$slug.tsx`」。Cursor 能看到整個專案，但給它明確的起點能讓輸出更精準。

**說明「為什麼這樣做」**
「中文以每分鐘 300 字計算」比「計算閱讀時間」更好。背景資訊讓 AI 做出更合適的選擇。

**一次只改一件事**
不要一次說「幫我加閱讀時間、加分享按鈕、改標題樣式」。拆成三次請求，每次確認沒問題再繼續。同時改多個東西，出問題很難定位是哪裡的問題。

**看不懂的 code 問清楚**
「這段 `Math.ceil` 是在做什麼？」直接在 Cursor 裡問，它會解釋。你不需要完全看懂每一行，但對關鍵邏輯要有基本理解，否則之後出問題你不知道去哪裡找。

**本地測試後再部署**
`npm run dev` 在本地跑一遍，確認沒問題再 `npm run deploy`。部署到正式環境之後才發現問題，回滾比較麻煩。

-----

## 什麼樣的功能適合這個流程？

**非常適合：**

- UI 元件的新增和修改
- 新的 API 端點
- 資料庫查詢
- 表單驗證邏輯
- 樣式調整

**需要更謹慎：**

- 涉及付款的邏輯（Stripe webhook、金額計算）
- 身份驗證相關的修改
- 會影響現有資料結構的資料庫變更

對後者，AI 生成的 code 可以作為起點，但要更仔細地 review，必要時手動測試每個邊界情況。

-----

## 總結

Vibe Coding 的核心工作流：

```
1. 想清楚需求（你要什麼、在哪裡、怎麼算）
2. 給 AI 明確的指令（說檔案路徑、說業務邏輯）
3. 看 AI 輸出，判斷對不對
4. 本地測試
5. 部署
```

最重要的一點：**你是導演，AI 是執行。** 每一步都是你在判斷方向，AI 在實現細節。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
