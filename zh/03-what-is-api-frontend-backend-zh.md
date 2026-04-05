# 什麼是 API？用白話解釋前後端怎麼溝通

[English](../en/03-what-is-api-frontend-backend-en.md) · [繁中](../zh/03-what-is-api-frontend-backend-zh.md) · [Español](../es/03-what-is-api-frontend-backend-es.md) · [日本語](../jp/03-what-is-api-frontend-backend-jp.md) · [Português (BR)](../pt-br/03-what-is-api-frontend-backend-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 7 分鐘

-----

## 一個你每天都在用的比喻

你去餐廳吃飯。

你不會走進廚房自己煮，你會告訴服務生你想要什麼。服務生把你的需求傳給廚房，廚房做好之後，服務生把食物端出來給你。

**服務生就是 API。**

API 是前端（你）和後端（廚房）之間的溝通橋樑。前端不需要知道後端怎麼運作，只需要知道怎麼跟 API 說話。

-----

## API 到底是什麼？

**API（Application Programming Interface）** 是一組規則，定義了兩個系統之間怎麼溝通。

在 web app 的脈絡裡，通常指的是：

- **前端**（瀏覽器裡的頁面）發送請求
- **後端 API**（伺服器）接收請求、處理、回傳結果

一個典型的 API 請求長這樣：

```
前端問：「給我 userId = 123 的用戶資料」
→ GET /api/users/123

後端答：「好，這是他的資料」
→ { "id": 123, "name": "Danko", "email": "danko@example.com" }
```

-----

## HTTP 方法：你在對 API 說什麼？

API 請求有四種常見的動作，對應不同的 HTTP 方法：

|方法             |用途  |比喻        |
|---------------|----|----------|
|`GET`          |讀取資料|「給我看菜單」   |
|`POST`         |新增資料|「我要點這道菜」  |
|`PUT` / `PATCH`|修改資料|「我要換成另一道菜」|
|`DELETE`       |刪除資料|「取消這道菜」   |

-----

## API 回應：後端說了什麼？

後端回應通常包含兩個部分：

**HTTP 狀態碼**——一個數字，告訴你結果如何：

|狀態碼  |意思                  |
|-----|--------------------|
|`200`|成功                  |
|`201`|新增成功                |
|`400`|你的請求有問題（Bad Request）|
|`401`|你沒有登入（Unauthorized） |
|`403`|你沒有權限（Forbidden）    |
|`404`|找不到（Not Found）      |
|`500`|伺服器出錯（Server Error） |

**回應內容**——通常是 JSON 格式：

```json
{
  "success": true,
  "data": {
    "id": 123,
    "name": "Danko"
  }
}
```

-----

## VibeFast 的 API 架構

VibeFast 用 **Service Binding** 讓前端和後端直接在 Cloudflare 內部溝通，不走公開網路。

```
瀏覽器
  ↓ HTTP 請求
Remix 前端 Worker
  ↓ Service Binding（內部，零 CORS）
Workers API
  ↓
D1 資料庫
```

這意味著：

- 後端 API 沒有公開 URL，外部無法直接存取
- 前端和後端之間不需要設定 CORS
- 每次 API 呼叫少一次網路往返，速度更快

-----

## 第三方 API：你的 app 也在呼叫別人的 API

你的 app 不只有自己的 API，也會呼叫別人的 API：

- **Stripe API**：「幫我建立一筆付款」
- **Resend API**：「幫我發一封郵件給這個用戶」
- **Cloudflare API**：`npm run deploy` 背後就是在呼叫 Cloudflare 的 API

這些第三方 API 需要 API key 才能使用——這就是為什麼上一篇說 API key 要好好保管在環境變數裡。

-----

## 用 AI 設計 API 端點

這是 Vibe Coding 的實際應用場景。你可以直接告訴 AI：

> 「幫我在 Cloudflare Workers 裡建立一個 API 端點，GET /api/posts，回傳所有已發布的文章，按照建立時間倒序排列，每頁 10 筆。」

AI 會生成類似這樣的 code：

```typescript
// apps/api/src/routes/posts.ts
app.get("/api/posts", async (c) => {
  const page = Number(c.req.query("page") || 1)
  const limit = 10
  const offset = (page - 1) * limit

  const posts = await c.env.DB.prepare(`
    SELECT id, title, slug, created_at 
    FROM posts 
    WHERE status = 'published'
    ORDER BY created_at DESC
    LIMIT ? OFFSET ?
  `).bind(limit, offset).all()

  return c.json({ success: true, data: posts.results })
})
```

你不需要自己寫這些，但看懂它在做什麼，能讓你更好地判斷 AI 的輸出是否正確。

-----

## 總結

API 是前後端之間的溝通規則。記住三件事：

1. 前端用 HTTP 方法（GET/POST/PUT/DELETE）告訴 API 要做什麼
1. 後端用狀態碼（200/404/500）告訴前端結果如何
1. 資料通常用 JSON 格式傳遞

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
