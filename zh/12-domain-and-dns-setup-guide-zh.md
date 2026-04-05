# 什麼是 Domain 和 DNS？買了域名之後要做什麼

[English](../en/12-domain-and-dns-setup-guide-en.md) · [繁中](../zh/12-domain-and-dns-setup-guide-zh.md) · [Español](../es/12-domain-and-dns-setup-guide-es.md) · [日本語](../jp/12-domain-and-dns-setup-guide-jp.md) · [Português (BR)](../pt-br/12-domain-and-dns-setup-guide-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 7 分鐘

-----

## 你買了域名，然後呢？

很多人買了域名之後不知道下一步要做什麼。

域名買好了，app 部署好了，但兩個沒有連起來——打開域名是空白頁或「找不到網站」。

這篇解釋清楚域名和 DNS 的關係，以及怎麼把你的域名指向 Cloudflare Workers。

-----

## 域名是什麼？

**域名（Domain）** 是你 app 的地址，比如 `vibefast.app`。

你的 app 實際上跑在某個 IP 地址上，比如 `104.21.45.123`。但沒有人想記 IP，所以有了域名——用一個好記的名字代替數字地址。

域名需要向域名註冊商購買（Namecheap、GoDaddy、Cloudflare Registrar 都可以）。`.app`、`.com`、`.io` 是不同的頂級域名（TLD），價格和用途略有不同。

-----

## DNS 是什麼？

**DNS（Domain Name System）** 是把域名翻譯成 IP 地址的系統。

你可以把它想成一本電話簿：

```
vibefast.app → 104.21.45.123
google.com → 142.250.80.46
```

當有人在瀏覽器輸入 `vibefast.app`，背後發生的事：

```
1. 瀏覽器問 DNS：vibefast.app 的 IP 是多少？
2. DNS 查表，回答：104.21.45.123
3. 瀏覽器連到這個 IP，載入網站
```

整個過程在幾毫秒內完成，你感覺不到。

-----

## DNS 記錄的種類

設定 DNS 時你會看到幾種記錄類型：

|記錄類型   |用途           |例子                               |
|-------|-------------|---------------------------------|
|`A`    |把域名指向一個 IP 地址|`vibefast.app → 104.21.45.123`   |
|`CNAME`|把域名指向另一個域名   |`www.vibefast.app → vibefast.app`|
|`MX`   |郵件伺服器設定      |讓 `@vibefast.app` 的信能收到          |
|`TXT`  |驗證用途         |Google、Stripe 等服務驗證你的域名所有權       |

-----

## 把域名連到 Cloudflare Workers

整個設定分兩個部分：**第一步把域名的 DNS 管到 Cloudflare，第二步在 Workers 裡綁定自訂域名。**

### 第一步：把 DNS 管理轉移到 Cloudflare（推薦）

如果域名是在 Cloudflare Registrar 買的，DNS 已經由 Cloudflare 管理，直接跳到第二步。

如果域名在其他地方買的（Namecheap、GoDaddy 等）：

1. 登入 [Cloudflare Dashboard](https://dash.cloudflare.com)，點擊「Add a domain」
1. 輸入你的域名，選擇免費方案
1. Cloudflare 自動掃描並匯入現有 DNS 記錄，確認沒有遺漏
1. Cloudflare 給你兩個 Nameserver 地址，例如：
   
   ```
   aria.ns.cloudflare.com
   bob.ns.cloudflare.com
   ```
1. 去你的域名註冊商，把 Nameserver 換成上面這兩個
1. 等待生效，通常幾分鐘到幾小時，最多 48 小時

生效之後，你所有的 DNS 設定都在 Cloudflare 裡管理。

### 第二步：在 Workers 設定自訂域名

DNS 管到 Cloudflare 之後，進入 Workers 的設定頁面綁定域名：

1. 登入 [Cloudflare Dashboard](https://dash.cloudflare.com)
1. 左側選單點「Workers & Pages」
1. 點擊你的**前端 Worker**（VibeFast 裡是 web worker，不是 API worker）
1. 進入「Settings」→「Domains & Routes」
1. 點擊「Add」→「Custom Domain」
1. 從下拉選單選擇已經登記在你 Cloudflare 帳號下的域名
1. 點擊「Add Custom Domain」

注意：域名必須已經在你的 Cloudflare 帳號裡（即第一步完成後），才會出現在下拉選單。

Cloudflare 會自動：

- 新增對應的 DNS 記錄
- 申請和設定 SSL 憑證
- 把這個域名的流量路由到你的 Worker

幾分鐘後，打開瀏覽器輸入你的域名，就能看到你的 app。

-----

## 設定完之後怎麼驗證？

```bash
# 查詢域名的 DNS 記錄
nslookup vibefast.app

# 或用 dig
dig vibefast.app

# 確認 HTTPS 憑證
curl -I https://vibefast.app
```

如果看到正確的 IP 或 CNAME，代表 DNS 設定生效了。

-----

## 常見問題

**Q：DNS 要多久才生效？**

理論上最多 48 小時，實際上通常幾分鐘到幾小時。如果等超過 2 小時還沒生效，先確認記錄填寫正確。

**Q：`www.vibefast.app` 和 `vibefast.app` 要分開設定嗎？**

是的。你需要分別設定：

- `vibefast.app`（裸域名，apex domain）
- `www.vibefast.app`（www 子域名）

通常會把 `www` 用 CNAME 指向裸域名，或者兩個都指向 Workers，並設定一個重定向。

**Q：SSL 憑證要自己申請嗎？**

不需要。把域名指向 Cloudflare 之後，Cloudflare 自動幫你申請和更新 SSL 憑證，你的 app 自動支援 HTTPS。

-----

## 總結

域名是你 app 的地址，DNS 是把地址翻譯成 IP 的系統。設定流程：

1. 買域名
1. 把 DNS 管理轉到 Cloudflare（推薦）或在現有 DNS 加 CNAME
1. 在 Cloudflare Workers 設定自訂域名
1. Cloudflare 自動處理 HTTPS

整個過程通常在 30 分鐘內完成。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
