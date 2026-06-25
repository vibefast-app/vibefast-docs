# 如何用 AI 設計漂亮的網站：Cursor Prompt 完整指南

[English](../en/11-ai-frontend-design-with-cursor-en.md) · [繁中](../zh/11-ai-frontend-design-with-cursor-zh.md) · [Español](../es/11-ai-frontend-design-with-cursor-es.md) · [日本語](../jp/11-ai-frontend-design-with-cursor-jp.md) · [Português (BR)](../pt-br/11-ai-frontend-design-with-cursor-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 12 分鐘

-----

## 你不需要學會設計，但你需要會說清楚你要什麼

很多人用 AI 做前端的結果是：「能跑，但醜。」

不是 AI 不會設計，是你給的指令不夠具體。「幫我做一個漂亮的首頁」和「幫我做一個深色背景、橙色 CTA 按鈕、IBM Plex Mono 標題字體的 SaaS 首頁」，AI 給出來的結果天差地遠。

這篇教你怎麼給出第二種指令。

-----

## 先理解幾個基本概念

在開始之前，你需要知道幾個詞——不需要深入學，能看懂 AI 在說什麼就夠了。

### React 和 JSX 是什麼？

**React** 是目前最主流的前端框架。你的頁面是由一個個「元件（component）」組成的，每個元件負責一個區塊的 UI。

**JSX** 是 React 用的語法，讓你可以在 JavaScript 裡直接寫 HTML 結構：

```tsx
// 這就是一個 React 元件
export default function ProductCard({ name, price }) {
  return (
    <div className="p-4 border rounded-lg">
      <h2>{name}</h2>
      <p>${price}</p>
    </div>
  )
}
```

花括號 `{}` 裡面是 JavaScript 表達式，其他部分看起來像 HTML。

### Remix 是什麼？

**Remix** 是建立在 React 上的全棧框架，vibefast.app 用它來做前端。Remix 的特色是：

- **loader**：頁面載入時，從後端取資料的函式
- **action**：表單送出時，處理用戶操作的函式
- **route**：每個檔案對應一個 URL 路徑，例如 `app/routes/shop.tsx` 對應 `/shop`

你不需要完全理解這些，遇到不懂的直接問 AI 解釋。

### Tailwind CSS

**Tailwind** 是 vibefast.app 用的 CSS 框架。你不需要自己寫 CSS，而是用 class 名稱直接在 HTML 上描述樣式：

```tsx
// 不用 Tailwind
<button style={{ backgroundColor: 'orange', padding: '12px 24px', borderRadius: '8px' }}>
  購買
</button>

// 用 Tailwind
<button className="bg-orange-500 px-6 py-3 rounded-lg text-white font-semibold">
  購買
</button>
```

Tailwind 的 class 名稱很直觀：`bg-orange-500` 是橙色背景，`px-6` 是左右 padding，`rounded-lg` 是圓角。你不需要記住所有 class，AI 知道，你告訴它你要什麼效果就好。

### 幾個你會常聽到的排版詞

- **Hero**：頁面最頂部的大區塊，通常有標題、副標題、CTA 按鈕
- **CTA（Call to Action）**：引導用戶做某個動作的按鈕，例如「立即購買」「免費試用」
- **Card**：卡片，帶邊框或陰影的獨立內容區塊
- **Grid**：格線排列，例如三欄並排的功能介紹
- **Responsive**：響應式，在手機和桌機都能正確顯示

-----

## 怎麼告訴 AI 你要的設計風格

這是最關鍵的技能。給 AI 設計指令，要從這幾個維度說清楚：

### 1. 整體風格

```
# 說明你想要的感覺
"深色主題（dark mode）"
"極簡白色，大量留白"
"科技感，有光暈效果（glow effect）"
"溫暖、有人情味，適合個人品牌"
```

### 2. 顏色

不要說「藍色」，要說具體的 Tailwind 顏色或 hex 值：

```
# 不夠具體
"用藍色主題"

# 夠具體
"主色用 orange-500（#f97316），背景用 zinc-950，文字用 zinc-100"
```

常用的 Tailwind 顏色名稱：`slate`、`zinc`、`neutral`（灰色系）、`orange`、`blue`、`violet`、`emerald`。每個顏色有 50–950 的深淺。

### 3. 字體

```
"標題用 IBM Plex Mono（等寬字體，科技感）"
"內文用 Inter（現代、易讀）"
"標題用 Bricolage Grotesque（有個性的無襯線）"
```

如果不知道要用什麼，直接說「幫我選一個適合 SaaS 產品的字體組合」，AI 會推薦。

### 4. 元素細節

```
"按鈕用圓角（rounded-full）"
"卡片有淡淡的邊框（border border-zinc-800）"
"有 hover 動畫（hover:scale-105 transition）"
"標題有橙色底線裝飾"
```

-----

## 實戰：一步步做出 vibefast.app 風格的首頁 Hero

我用 vibefast.app 自己的首頁設計當例子，示範完整的 prompt 流程。

### 第一輪：建立基本結構

在 Cursor 裡按 `Cmd+L`，輸入：

```
在 apps/web/app/routes/_index.tsx 建立一個 Hero section。

設計規格：
- 深色背景：bg-zinc-950
- 置中排列
- 頂部有一個小標籤（badge），文字「Edge-native · No Next.js · No Vercel」，橙色邊框
- 主標題：大字，白色，使用 IBM Plex Mono 字體，文字「The Cloudflare-Native Web App Template」
- 副標題：灰色，較小，文字「From idea to production in 40 minutes.」
- 兩個按鈕並排：主按鈕橙色背景「Buy Now — $99」，次按鈕透明邊框「View Demo」
- 整個 section 垂直置中，最小高度 100vh
```

AI 會生成一個完整的 JSX 結構。

### 第二輪：調整細節

看到 AI 的輸出之後，你會發現一些想調整的地方。繼續在 Cursor 說：

```
調整幾個地方：
1. 標題字體太小，改成 text-5xl md:text-7xl
2. 兩個按鈕之間加多一點間距，gap-4 改成 gap-6
3. 主按鈕加 hover 效果：hover:bg-orange-400 transition-colors duration-200
4. 整個 Hero 加一個很淡的橙色放射狀光暈在背景中間
```

### 第三輪：加入細節裝飾

```
在 Hero 背景加一個 grid 點狀圖案，用非常淡的 zinc-800 顏色，
讓頁面有深度感但不搶眼。類似 linear.app 的背景風格。
```

三輪下來，你有了一個完整的 Hero section，設計質量接近專業水準。

-----

## 用截圖給 AI 看參考網站

這是讓 AI 輸出品質大幅提升的技巧。

你看到一個喜歡的網站設計，截圖，直接拖進 Cursor 的對話框，然後說：

```
參考這張截圖的設計風格，幫我做一個類似的 pricing card 元件。
技術要求：
- 用 Tailwind CSS
- Remix 的 JSX 格式
- 保留我現在的 zinc-950 深色主題
- 把文字內容換成 vibefast.app 的定價資訊：$99 早鳥價，包含 private repo、終身更新
```

Cursor 有視覺理解能力，它能看懂截圖裡的設計，然後用你的技術棧重新實作。

**適合截圖參考的網站：**

- [linear.app](https://linear.app) — 深色極簡，科技感強
- [vercel.com](https://vercel.com) — 現代 SaaS 風格
- [stripe.com](https://stripe.com) — 乾淨、信任感強
- [tailwindui.com](https://tailwindui.com) — 直接參考元件

-----

## 常見錯誤和怎麼修正

### 錯誤一：AI 給了「看起來可以」但手機版壞掉的設計

**症狀：** 桌機版漂亮，手機版文字太大或排版亂掉。

**修正方式：**

```
目前的設計在手機版有問題，幫我加上響應式調整：
- 標題在手機用 text-3xl，平板用 text-5xl，桌機用 text-7xl
- 兩個按鈕在手機改成垂直排列（flex-col），桌機才並排（sm:flex-row）
- 左右 padding 在手機用 px-4，桌機用 px-8
```

### 錯誤二：設計太「AI 感」，看起來千篇一律

**症狀：** 藍白色、圓角卡片、陰影，像所有 AI 生成的網站一樣。

**修正方式：** 給更具體的個性指令：

```
目前設計太 generic，幫我做幾個調整讓它更有個性：
1. 把藍色全部換成橙色（orange-500）
2. 主標題加刪除線效果在「No Next.js」上（text-decoration: line-through）
3. 加一個終端機樣式的 code block 展示三行安裝指令
4. 字體換成 IBM Plex Mono，有工程師感
```

### 錯誤三：想改一個小地方，但 AI 改了太多東西

**症狀：** 你說「把按鈕顏色換成橙色」，AI 順便把其他東西也改了。

**修正方式：** 說得更精確：

```
只改這一件事，其他不要動：
把 <button className="bg-blue-500..."> 的 bg-blue-500 換成 bg-orange-500。
其他所有內容保持不變。
```

-----

## 幾個讓設計立刻提升的小技巧

這些是我在做 vibefast.app 首頁時學到的，每一個單獨看都很小，但加在一起效果很明顯。

**加入過渡動畫**

```tsx
// hover 時輕微放大
className="hover:scale-105 transition-transform duration-200"

// hover 時顏色漸變
className="hover:bg-orange-400 transition-colors duration-200"
```

**文字漸層效果（Gradient Text）**

```tsx
className="bg-gradient-to-r from-white to-zinc-400 bg-clip-text text-transparent"
```

**卡片的細緻邊框**

```tsx
// 比純白邊框更有質感
className="border border-zinc-800 hover:border-zinc-600 transition-colors"
```

**在深色背景上加光暈**

```tsx
// 一個橙色放射狀光暈
<div className="absolute inset-0 bg-[radial-gradient(ellipse_at_center,rgba(249,115,22,0.15),transparent_70%)] pointer-events-none" />
```

-----

## 完整的 Prompt 模板

你可以把這個模板複製，換掉括號裡的內容，直接用：

```
幫我在 [檔案路徑] 建立一個 [元件名稱]。

設計風格：
- 主題：[深色/淺色]
- 主色：[顏色名稱，例如 orange-500]
- 背景色：[例如 zinc-950]
- 字體風格：[例如 IBM Plex Mono 標題，Inter 內文]

內容：
- [描述這個元件要顯示什麼]

技術要求：
- 使用 Tailwind CSS
- Remix JSX 格式
- 要有 hover 動畫
- 需要響應式（手機和桌機都要正確顯示）

參考風格：[如果有參考網站，說明或附截圖]
```

-----

## 總結

用 AI 做出漂亮的前端，關鍵不是「AI 會不會設計」，而是**你能不能把想法說清楚**。

記住這個流程：

1. **確定風格方向**——深色/淺色、主色、字體
1. **給第一輪 prompt**——結構 + 設計規格一起說
1. **迭代細節**——每次只調整一件事
1. **用截圖加速**——參考你喜歡的網站，截圖給 AI 看
1. **加入細節裝飾**——動畫、光暈、漸層，小細節讓設計有質感

設計沒有「完成」，只有「夠好了可以上線」。先做出 80 分，上線，再慢慢迭代。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 8 月 1 日漲至 $199。
