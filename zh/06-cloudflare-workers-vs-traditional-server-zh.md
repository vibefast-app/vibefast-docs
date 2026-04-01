# Cloudflare Workers 和傳統伺服器有什麼不同？

[English](../en/06-cloudflare-workers-vs-traditional-server-en.md) · [繁中](../zh/06-cloudflare-workers-vs-traditional-server-zh.md) · [Español](../es/06-cloudflare-workers-vs-traditional-server-es.md) · [日本語](../jp/06-cloudflare-workers-vs-traditional-server-jp.md) · [Português (BR)](../pt-br/06-cloudflare-workers-vs-traditional-server-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 8 分鐘

-----

## 你不需要租伺服器了

五年前，如果你想部署一個 web app 後端，你的選項大概是：

- 租一台 AWS EC2 虛擬機，自己裝 Node.js、設定防火牆、管理更新
- 用 Heroku 這類 PaaS，簡單一點但還是要想伺服器規格
- 進階一點用 Lambda 這類 serverless，但冷啟動問題讓人頭痛

現在有了 Cloudflare Workers，這些問題大部分消失了。

-----

## 傳統伺服器是什麼概念？

傳統的後端部署模型：

```
用戶請求
  ↓
某個固定地點的伺服器（例如 AWS us-east-1）
  ↓
處理請求
  ↓
回傳結果
```

問題在「某個固定地點」——如果你的伺服器在美國東岸，香港的用戶每次請求都要跨太平洋來回，延遲可能高達 200–300ms。

而且你需要：

- 選擇伺服器規格（多少 CPU、多少記憶體）
- 管理伺服器的作業系統和安全更新
- 在流量高峰時手動或自動擴容
- 付固定的月費，不管有沒有流量

-----

## Cloudflare Workers 是什麼概念？

Workers 完全不一樣：

```
用戶請求（香港）
  ↓
離用戶最近的 Cloudflare 節點（香港/亞洲）
  ↓
在那個節點直接執行你的 code
  ↓
回傳結果
```

**300+ 個邊緣節點遍布全球**，你的 code 自動在離用戶最近的地方執行。香港用戶連香港節點，德國用戶連德國節點，延遲從 200ms 降到幾毫秒。

而且你不需要管任何伺服器。你只寫 code，Cloudflare 幫你處理執行環境、擴容、硬體維護。

-----

## Workers 和傳統 Serverless（Lambda）的差異

你可能聽過 AWS Lambda 也是「serverless」，兩者有什麼不同？

|     |傳統 Serverless（Lambda）|Cloudflare Workers|
|-----|---------------------|------------------|
|執行位置 |固定區域（如 us-east-1）    |全球 300+ 邊緣節點      |
|冷啟動  |有，可能長達數秒             |幾乎沒有（< 1ms）       |
|執行環境 |Node.js 容器           |V8 Isolate        |
|記憶體計費|按分配記憶體計費             |按實際 CPU 時間計費      |
|免費額度 |有限                   |每天 100K 請求免費      |

**冷啟動**是 Lambda 最常被詬病的問題。第一次呼叫或長時間沒有請求後，Lambda 需要幾秒鐘「冷啟動」才能回應。這對用戶體驗是明顯的停頓。

Workers 用的是 **V8 Isolate** 技術（同樣的技術讓 Chrome 可以在分頁之間快速切換），啟動時間不到 1 毫秒，用戶幾乎感覺不到。

-----

## Workers 的限制

Workers 不是萬能的，有幾個重要限制你需要知道：

**執行時間限制**

- 免費方案：每個請求最多 10ms CPU 時間
- 付費方案：最多 30 秒

對大多數 API 請求，10ms 完全夠用。但如果你需要做複雜的影片處理、大量計算，Workers 可能不適合。

**不支援完整的 Node.js API**
Workers 跑在 V8 環境，不是 Node.js。很多 Node.js 的內建模組（如 `fs`、`path`）不能用。這也是為什麼你用的套件要確認「edge compatible」。

**記憶體上限**
每個 Worker 實例有 128MB 記憶體上限，大多數 web app 用不到這個上限。

-----

## 為什麼 VibeFast 選擇 Workers？

對 Vibe Coder 來說，Workers 的最大優勢不只是速度：

**你不需要想伺服器的事。** 沒有要選的規格，沒有要管的 OS，沒有要設定的防火牆。你只寫 code，`npm run deploy`，上線。

**從第一天就是全球部署。** 不需要等到有足夠預算才考慮 CDN 或多區域部署，你的 app 第一天就跑在全球 300+ 個節點上。

**免費額度夠用來驗證想法。** 每天 10 萬次請求免費，對剛起步的 app 完全夠用，等到有了真實用戶和收入再考慮升級。

-----

## 總結

|       |傳統伺服器           |Cloudflare Workers|
|-------|----------------|------------------|
|你需要管理什麼|伺服器、OS、擴容       |只寫 code           |
|全球延遲   |高（固定地點）         |低（就近處理）           |
|冷啟動    |有（Lambda 問題尤其明顯）|幾乎沒有              |
|起步成本   |固定月費            |免費額度起步            |
|適合場景   |複雜計算、長時間任務      |Web API、SSR、邊緣邏輯  |

Workers 不是所有場景的最佳選擇，但對大多數 web app 和 API 來說，它是目前最省心、最快、起步成本最低的方案。

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
