# SEO 基礎：讓你的產品被 Google 找到

[English](../en/22-seo-basics-for-indie-makers-en.md) · [繁中](../zh/22-seo-basics-for-indie-makers-zh.md) · [Español](../es/22-seo-basics-for-indie-makers-es.md) · [日本語](../jp/22-seo-basics-for-indie-makers-jp.md) · [Português (BR)](../pt-br/22-seo-basics-for-indie-makers-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 8 分鐘

-----

## SEO 不神秘，但很多人做錯方向

很多人把 SEO 想得很複雜，花大量時間研究演算法、反向連結、關鍵字密度。

對 indie hacker 來說，SEO 其實只有兩件事：**讓 Google 看得懂你的頁面、讓真實的人願意點進來。**

做好這兩件事，流量自然會來。其他的技巧都是進階優化，一開始不需要管。

-----

## 第一件事：讓 Google 看得懂你的頁面

### Title 和 Meta Description

每個頁面最重要的兩個 SEO 元素：

**Title**：出現在瀏覽器標籤和 Google 搜尋結果的藍色連結。

```html
<title>vibefast.app — Cloudflare 全棧 Web App 模板</title>
```

- 長度控制在 50-60 字元
- 包含你的核心關鍵字
- 每個頁面要不同，不要所有頁面都用一樣的 title

**Meta Description**：出現在 Google 搜尋結果 title 下方的灰色說明文字。

```html
<meta name="description" content="用 Remix + Cloudflare Workers 快速建立全棧 app。內建 Auth、Stripe、D1、R2，一個指令啟動。" />
```

- 長度控制在 150-160 字元
- 清楚說明這個頁面是什麼、對訪客有什麼好處
- 這段文字 Google 不保證會用，但大多數時候會

在 Remix 裡設定：

```typescript
// app/routes/_index.tsx
export function meta() {
  return [
    { title: "vibefast.app — Cloudflare 全棧 Web App 模板" },
    { name: "description", content: "用 Remix + Cloudflare Workers 快速建立全棧 app。" },
  ]
}
```

### 標題結構（H1、H2、H3）

每個頁面只有一個 `<h1>`，說明這個頁面的主題。然後用 `<h2>`、`<h3>` 組織內容層次。

```html
<h1>手工皂電商網站</h1>
  <h2>天然成分</h2>
    <h3>薰衣草系列</h3>
    <h3>玫瑰系列</h3>
  <h2>購買方式</h2>
```

Google 用標題結構理解你的頁面在說什麼。

### 圖片 Alt 文字

每張圖片要有 `alt` 屬性，描述圖片內容：

```html
<!-- ❌ 沒有說明 -->
<img src="soap.jpg" />

<!-- ✅ 清楚說明 -->
<img src="lavender-soap.jpg" alt="手工製作薰衣草皂，天然精油成分" />
```

Alt 文字讓 Google 知道這張圖片是什麼，也讓視障用戶的螢幕閱讀器能理解內容。

-----

## 第二件事：讓 Google 發現你的頁面

### Sitemap

Sitemap 是一份告訴 Google「我的網站有哪些頁面」的 XML 文件。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
  <url>
    <loc>https://yoursite.com/</loc>
    <lastmod>2026-03-01</lastmod>
  </url>
  <url>
    <loc>https://yoursite.com/shop</loc>
    <lastmod>2026-03-01</lastmod>
  </url>
</urlset>
```

告訴 AI「幫我在 Remix 建立一個動態 sitemap，自動包含所有商品頁面」，它會生成一個從資料庫讀取 URL 的 sitemap route。

然後到 [Google Search Console](https://search.google.com/search-console) 提交你的 sitemap URL。

### robots.txt

`robots.txt` 告訴搜尋引擎哪些頁面可以爬、哪些不行：

```
User-agent: *
Allow: /
Disallow: /admin
Disallow: /api

Sitemap: https://yoursite.com/sitemap.xml
```

把這個放在 `/public/robots.txt`，Remix 會自動回傳。

-----

## 第三件事：內容是長期流量的根本

技術 SEO 只是起點。長期來看，**有價值的內容才是持續帶來流量的關鍵。**

這不是說你要每天寫部落格。而是說：

你的產品頁面本身，要清楚回答「這個產品是什麼、能解決什麼問題、適合誰用」。

你的 FAQ 頁面，要回答用戶真正會搜尋的問題，用他們搜尋的語言來寫，不是用你自己的術語。

假設你在賣手工皂，潛在用戶可能搜尋「天然手工皂推薦」、「薰衣草皂好處」、「手工皂和工廠皂差別」——這些問題的答案，就是你的 SEO 內容。

### 用 vibefast.app 內建的 Blog 功能寫引流文章

寫 SEO 內容最大的阻力通常是「要自己搭一個 blog 系統」——資料表、後台編輯、前台顯示、文章的 meta title 和 description……光是把這些做好就要花不少時間。

vibefast.app 已經把這些都做好了。內建的 Blog 功能包含：

- **後台文章管理**：建立、編輯、發布、下架文章，不需要動 code
- **Markdown 編輯器**：直接在後台用 Markdown 寫文章，所見即所得
- **SEO 欄位**：每篇文章都有獨立的 meta title 和 meta description 欄位，讓你針對每篇文章設定 Google 看到的標題和說明
- **自動 Sitemap**：新文章發布後，自動加進 sitemap，讓 Google 更快發現

實際的工作流程是：

1. 用 AI 找出你的目標用戶會搜尋的長尾關鍵字（下一節有說明）
1. 用 AI 起草文章內容
1. 進 vibefast.app 後台，貼進 Markdown 編輯器，調整
1. 填入針對這篇文章的 meta title 和 description
1. 發布

從關鍵字到文章上線，最快一個小時。這是 Vibe Coder 做內容 SEO 最有效率的方式。

-----

## 用 AI 幫你找關鍵字

直接告訴 AI：

```
我有一個賣天然手工皂的電商網站，
目標用戶是注重成分、偏好天然產品的消費者。

幫我找 10 個他們可能會搜尋的長尾關鍵字，
以及每個關鍵字適合放在網站的哪個頁面。
```

AI 給的關鍵字建議：

```
「天然手工皂推薦香港」→ 首頁或精選商品頁
「薰衣草精油手工皂功效」→ 薰衣草系列商品頁
「手工皂和一般皂有什麼不同」→ FAQ 或部落格
「敏感肌適合用手工皂嗎」→ FAQ 或部落格
「手工皂保存方法」→ 使用說明頁
```

長尾關鍵字（較長、較具體的搜尋詞）競爭少、轉換率高，是 indie hacker 最有效的 SEO 策略。

-----

## 技術 SEO 檢查清單

上線之前確認：

```
□ 每個頁面有獨特的 <title>（50-60 字元）
□ 每個頁面有 meta description（150-160 字元）
□ 每個頁面只有一個 <h1>
□ 所有圖片有 alt 文字
□ sitemap.xml 存在並已提交到 Google Search Console
□ robots.txt 設定正確
□ 網站用 HTTPS（Cloudflare 自動處理）
□ 頁面載入速度夠快（Cloudflare 邊緣節點幫你加速）
□ 網站在手機上正常顯示
```

-----

## 一個不要做的事：不要買連結或用黑帽 SEO

Google 的演算法越來越聰明，黑帽技巧（買連結、關鍵字堆疊、隱藏文字）短期可能有效，但一旦被懲罰，排名會掉得很慘，而且很難恢復。

對 indie hacker 來說，最可持續的 SEO 策略就是：**做一個真的對用戶有幫助的網站，把內容寫清楚，把技術基礎做好。**

其他的事，讓 Google 自己來。

-----

## 總結

SEO 的本質很簡單：**讓 Google 看得懂你的頁面，讓真實的人願意點進來。**

從這幾件事開始：

1. 每個頁面設定好 title 和 meta description
1. 用正確的標題結構（h1、h2、h3）
1. 提交 sitemap 到 Google Search Console
1. 用 AI 找你的目標用戶會搜尋的長尾關鍵字，用這些語言寫內容

這些做好了，剩下的就是等待——SEO 需要時間，通常要 3-6 個月才能看到明顯效果。但一旦排名上去，是持續的免費流量。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
