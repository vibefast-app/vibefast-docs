# 我為什麼選 Cloudflare，而不是 Vercel 或 AWS

[English](../en/19-cloudflare-vs-vercel-vs-aws-en.md) · [繁中](../zh/19-cloudflare-vs-vercel-vs-aws-zh.md) · [Español](../es/19-cloudflare-vs-vercel-vs-aws-es.md) · [日本語](../jp/19-cloudflare-vs-vercel-vs-aws-jp.md) · [Português (BR)](../pt-br/19-cloudflare-vs-vercel-vs-aws-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 8 分鐘

-----

## 這不是一篇中立的比較文

網路上已經有很多「Vercel vs AWS vs Cloudflare 完整比較」，每一篇都會告訴你「各有優缺點，視情況而定」。

這篇不是那種文章。

我是一個 solopreneur，用自己的錢、自己的時間做產品。這篇是我的真實決定：**我選 Cloudflare，而且我認為大多數 Vibe Coder 也應該選 Cloudflare。**

如果你想看中立的比較，這篇可能不適合你。如果你想知道一個實際在用的人怎麼想，繼續讀。

-----

## Vercel 的問題不是技術，是商業模式

Vercel 的開發體驗很好，我不否認。`git push` 自動部署、Preview URL 自動生成，這些細節做得很用心。

但 Vercel 的商業模式建立在一個假設上：**你的 app 越成功，你付的錢越多。**

出站頻寬收費。Serverless Function 執行時間收費。超過免費額度之後，帳單會跟著流量一起漲。

這不是壞事——Vercel 是一家公司，要賺錢。但對 indie hacker 來說，這意味著你的成本曲線和你的用戶成長是掛鉤的。你的 app 剛開始爆紅的那段時間，往往也是你還沒有足夠收入的時候——偏偏就在那個時候，帳單開始炸。

更麻煩的是：**Vercel 沒有資料庫，沒有儲存。**

你要自己找 Supabase 接資料庫、找 AWS S3 存圖片、找 Resend 發郵件。每加一個服務，就多一個帳號、多一份帳單、多一個出問題的地方。你以為你在用一個平台，其實你在管理一個分散的服務網絡。

-----

## AWS 的問題不是費用，是複雜度

AWS 的費用其實沒有那麼誇張，對比一些人說的「帳單嚇死人」，實際算起來很多場景比 Vercel 便宜。

但 AWS 有另一個問題：**它是為工程師團隊設計的，不是為 solopreneur 設計的。**

VPC、Security Group、IAM Role、Load Balancer、Auto Scaling Group——光是把一個基本的 web app 跑起來，你需要理解十幾個概念，設定幾十個選項。每一個設定錯了都可能導致安全漏洞、服務中斷、或者莫名其妙的帳單。

AWS 的設計哲學是「彈性最大化」，你可以設定所有細節。但對大多數 indie app 來說，你不需要那種彈性，你需要的是「設定好就不用管它」。

AI 可以幫你生成 Terraform config、CloudFormation template，但你還是需要自己理解那些東西在做什麼，否則出問題的時候你完全不知道從哪裡找。

-----

## Cloudflare 解決的是什麼問題？

我選 Cloudflare 不是因為它最便宜，也不是因為它最快，而是因為它解決了我最在意的問題：**讓我可以把所有精力放在產品上，而不是基礎設施。**

**一個帳號，一個 dashboard，一份帳單。**

Workers 跑 Code，D1 存資料，R2 存圖片，全部在 Cloudflare 裡。你不需要在四五個平台之間切換，不需要管理四五套 API key，出問題只有一個地方要看。

**出站費是零。**

R2 的圖片被下載一百萬次，出站費是零。你的 app 越成功，這個優勢越明顯。Vercel 和 AWS 的出站費在高流量的時候會非常可觀。

**從第一天就是全球部署。**

Cloudflare 有 300+ 個邊緣節點，你的 code 自動跑在離用戶最近的地方。香港用戶連香港節點，不需要你設定任何 CDN 或多區域部署。

**免費額度夠大。**

Workers 每天 10 萬次請求免費，D1 每月 500 萬次讀取免費，R2 10GB 儲存免費。對剛起步的 app 來說，很可能完全用不完免費額度，等到有了足夠的用戶和收入再考慮付費計劃。

-----

## Cloudflare 的限制你也要知道

我不是在賣廣告，所以也說清楚 Cloudflare 不適合什麼：

**Workers 有 CPU 時間限制。** 免費方案每個請求最多 10ms CPU 時間，付費方案最多 30 秒。影片轉檔、大量資料運算、AI 模型推理這些不適合跑在 Workers 上。

**D1 是 SQLite，不是 PostgreSQL。** 如果你需要複雜的資料庫功能——複雜的 join、存儲過程、全文搜索——D1 可能不夠。大多數 indie app 用不到這些，但如果你的業務邏輯特別複雜，要考慮清楚。

**生態系統比較新。** D1 相對較新，有些功能還在演進。主流的功能都穩定夠用，但如果你的 app 有很特殊的需求，可能需要多查文件確認。

-----

## 一個簡單的判斷方式

不需要決策樹，一個問題就夠了：

**你是一個人在做產品，還是一個團隊？**

一個人做產品，選 Cloudflare。你的時間是最貴的資源，任何可以幫你減少「管理基礎設施」時間的選擇，都是正確的選擇。

團隊做產品，看你的技術背景。如果團隊已經很熟 Next.js，Vercel 沒有什麼問題。如果有複雜的後端需求，Cloudflare 或 AWS 都可以，取決於團隊的技術能力。

-----

## 總結

Vercel 很好，但它的成本結構和分散服務的特性讓獨立開發者管理起來很費力。

AWS 很強大，但它的複雜度讓大多數 indie hacker 花太多時間在設定上，而不是做產品。

Cloudflare 不完美，但它給了我最想要的東西：**一個地方管理所有事、成本可預測、從第一天就是全球部署。**

這是我的選擇，你的情況可能不同。但如果你是第一次做產品、一個人在跑、不想花時間管理基礎設施——我的建議是：從 Cloudflare 開始。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 8 月 1 日漲至 $199。
