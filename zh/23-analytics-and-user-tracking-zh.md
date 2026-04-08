# Analytics 和流量分析：了解你的用戶在做什麼

[English](../en/23-analytics-and-user-tracking-en.md) · [繁中](../zh/23-analytics-and-user-tracking-zh.md) · [Español](../es/23-analytics-and-user-tracking-es.md) · [日本語](../jp/23-analytics-and-user-tracking-jp.md) · [Português (BR)](../pt-br/23-analytics-and-user-tracking-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 6 分鐘

-----

## 你需要數據，才能做對決定

「用戶從哪裡來？」「哪個頁面最多人看？」「流量在哪一天突然掉了？」

沒有數據，你只能猜。猜對了算運氣，猜錯了在浪費時間優化錯的地方。

好消息是：如果你的 app 跑在 Cloudflare 上，基本的流量數據你已經有了，而且不需要安裝任何東西。

-----

## Cloudflare Analytics：免費、零設定、已經在跑

只要你的 app 部署在 Cloudflare Workers 上，Cloudflare 就自動幫你記錄所有流量數據。

進入 [Cloudflare Dashboard](https://dash.cloudflare.com)，點擊你的 Worker，選擇「Analytics」標籤，你就能看到：

- **請求量**：每天、每小時有多少請求進來
- **狀態碼分布**：200、404、500 各佔多少，一眼看出有沒有異常
- **回應時間**：平均回應速度，有沒有變慢
- **流量來源地區**：用戶從哪些國家和地區來

這些數據：

- 完全免費
- 不需要在 code 裡加任何東西
- 隱私友好，不追蹤個人身份，不需要 cookie consent banner
- 即時更新，不像某些工具有幾十小時的數據延遲

對剛起步的 app 來說，這些數據完全夠用。

-----

## Cloudflare Analytics 能告訴你什麼？

### 有沒有人來？

最基本的問題。每天的請求量圖表一眼就能看出流量趨勢——有沒有在成長、哪天突然掉了、發文之後有沒有帶來流量。

### 有沒有東西壞掉？

狀態碼分布是很實用的健康指標。如果 500 錯誤突然暴增，代表後端有問題。如果 404 很多，代表有一些頁面連結已經失效。部署新版本之後看一下這個圖，確認沒有引入新問題。

### 用戶從哪裡來？

地區分布讓你知道主要用戶在哪裡，幫助你決定要針對哪個市場做推廣。

-----

## Cloudflare Web Analytics：追蹤前端頁面瀏覽

Cloudflare Workers Analytics 記錄的是所有 HTTP 請求（包含 API）。如果你想專門追蹤前端頁面的瀏覽量，可以用 **Cloudflare Web Analytics**——同樣免費，同樣不需要 cookie。

只要你的 domain 已經加入 Cloudflare，Web Analytics 會自動啟用，不需要任何設定、不需要在 code 裡加任何 script。

進入 Cloudflare Dashboard → 選擇你的 domain → Web Analytics，就能看到：

- 每個頁面的瀏覽量
- 訪客數和頁面停留時間
- 流量來源（直接訪問、搜尋、社交媒體）
- 設備類型（手機、桌機）

-----

## 從哪個指標開始看

數據太多也是問題——你會陷入「分析癱瘓」，看數字但不知道要做什麼。

**剛上線時，只看三個數字：**

1. **每天的請求量**——有沒有人來？趨勢是往上還是往下？
1. **錯誤率**——500 錯誤有沒有異常？部署後有沒有引入新問題？
1. **流量來源地區**——用戶主要在哪裡？和你的目標市場一致嗎？

有了穩定的流量之後，再開始深入看頁面層級的數據。

-----

## vibefast.app 內建流量分析：Dashboard 直接看

如果你用的是 vibefast.app，流量分析功能已經做好了。

登入 vibefast.app 的 admin 後台，首頁 Dashboard 就能直接看到流量分析圖表——不需要打開 Cloudflare Dashboard，不需要設定任何東西，你的 app 的流量數據就在眼前。

這對剛起步的 web app 來說完全夠用。你可以：

- 一眼看出今天的訪客量和昨天比有沒有變化
- 看出哪些頁面最多人訪問
- 確認你的推廣活動有沒有帶來流量

-----

## 如果之後需要更深入的分析

Cloudflare 的基本數據夠用於起步。當你的 app 成長，有更多用戶，想追蹤更細緻的用戶行為（點擊哪個按鈕、在哪一步放棄購買），可以考慮接入第三方工具。

但這是之後的事。先把 app 做好、先有流量，再考慮深入分析。

-----

## 總結

Analytics 的目標不是收集數據，是用數據做決定。

起步的順序：

1. **Cloudflare Workers Analytics**：免費、零設定，部署完就有，先看請求量和錯誤率
1. **Cloudflare Web Analytics**：domain 加入 Cloudflare 自動啟用，追蹤頁面瀏覽量和流量來源
1. **vibefast.app Dashboard**：如果用 vibefast.app，流量圖表直接在 admin 首頁

數據驅動不是大公司的專利。知道用戶在做什麼，比猜測要有效得多。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
