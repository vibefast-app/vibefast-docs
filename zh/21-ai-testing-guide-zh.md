# 如何測試你的 App：讓 AI 幫你用 curl 驗證每一個功能

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 7 分鐘

-----

## 部署完之後，你怎麼知道它真的能用？

很多人部署完之後，打開瀏覽器點幾下，看起來沒壞，就以為沒問題了。

但瀏覽器只能測試你看得到的部分。你的 API 有沒有正確回傳資料？前端頁面真的有回傳 200 嗎？登入失敗的時候有沒有回傳正確的錯誤碼？這些靠點點看是測不出來的。

我的習慣是：**每做好一個功能，馬上用 curl 測一遍。** 不管是本地開發還是部署到正式環境，curl 都能用，結果一目了然。

-----

## curl 是什麼？

curl 是一個在終端機裡發送 HTTP 請求的工具，Mac 和 Linux 預裝，Windows 也有。

你不需要安裝任何 npm 套件，不需要寫任何 code，一行指令就能測試任何網址——包括前端頁面和後端 API。

**而且你不需要記住 curl 的語法，直接叫 AI 幫你生成指令就好。**

-----

## 第一步：讓 AI 幫你生成 curl 指令

做完任何功能，直接告訴 AI：

```
我剛把手工皂電商部署到 https://soapco.com。

幫我用 curl 測試這些功能，生成對應的指令：
1. 首頁有沒有正常回傳
2. 商品列表 API（GET /api/products）
3. 單一商品詳情（GET /api/products/lavender-soap）
4. 登入 API（POST /api/auth/login），用 email: test@example.com, password: test123
5. 用登入拿到的 token，測試需要登入的訂單列表 API（GET /api/orders）
6. 不帶 token 打訂單列表，確認被擋下來（應該回傳 401）
```

AI 會生成一套完整的 curl 指令，你直接複製貼上到終端機跑就好。

**這是 Vibe Coder 測試的核心心態：你負責說要測什麼，AI 負責寫怎麼測。**

-----

## curl 不只能測 API，也能測前端頁面

很多人以為 curl 只能測後端 API，其實前端頁面也能測：

```
幫我用 curl 測試這些前端頁面：
1. 首頁 https://soapco.com/ 有沒有回傳 200
2. 商品列表頁 /shop 有沒有正常回傳
3. 一個不存在的頁面 /xyz 有沒有回傳 404（不是 200）
4. /admin 這個後台頁面，不登入的情況下會不會被導向登入頁（應該回傳 302 或 401）
```

AI 生成的指令大概長這樣：

```bash
# 測試首頁
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/
# 預期看到：200

# 測試不存在的頁面
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/xyz
# 預期看到：404

# 測試後台頁面不能未登入存取
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/admin
# 預期看到：302 或 401，不能是 200
```

`-o /dev/null` 是把 HTML 內容丟掉不顯示，`-w "%{http_code}"` 只顯示狀態碼——前端測試通常只需要確認狀態碼對不對。

-----

## 看完整回應的時候

有時候你需要看 API 回傳的完整內容，告訴 AI：

```
幫我用 curl 打 GET https://soapco.com/api/products，
要把回傳的 JSON 格式化顯示，方便閱讀。
```

AI 會生成：

```bash
curl -s https://soapco.com/api/products | jq
```

`jq` 是格式化 JSON 的工具，Mac 用 `brew install jq` 安裝，或者直接問 AI 「幫我安裝 jq」。

-----

## 看狀態碼的時候

不確定 API 回傳的是 200、401 還是 500，告訴 AI：

```
幫我用 curl 打這個端點，同時顯示 HTTP 狀態碼和回傳內容：
POST https://soapco.com/api/auth/login
body: {"email": "test@example.com", "password": "wrongpassword"}
```

AI 生成：

```bash
curl -i -X POST https://soapco.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "wrongpassword"}'
```

`-i` 會把 HTTP 狀態碼和 headers 一起顯示出來。

-----

## 本地開發也一樣，換個網址就好

不需要等到部署，本地跑起來就能測：

```
幫我用 curl 測試本地的商品 API，
本地跑在 http://localhost:8787
```

AI 直接把網址換成 localhost，其他都一樣。

**本地測試 → 沒問題 → 部署 → 用正式 URL 再測一遍。**

這兩步都過了才算真的沒問題。

-----

## 一個好習慣：讓 AI 幫你把測試指令整理成 script

測試的功能多了之後，讓 AI 幫你整理：

```
幫我把以下這些測試整理成一個 test.sh，
每個測試顯示「通過」或「失敗」，
最後顯示幾個通過、幾個失敗：

1. 首頁回傳 200
2. 商品列表 API 回傳 200
3. 不存在的商品回傳 404
4. 不帶 token 打訂單 API 回傳 401

URL 是 https://soapco.com
```

之後每次部署完跑一遍：

```bash
bash test.sh
```

幾秒鐘確認主要功能都正常，不用一條一條手動打。

-----

## 總結

curl 是 Vibe Coder 最直觀的測試工具，而且前端後端都能用。核心流程只有兩步：

1. **用自然語言告訴 AI 你要測什麼**——頁面、API、正常情況、失敗情況
1. **AI 生成 curl 指令，你複製貼上跑**——看結果符不符合預期

每做好一個功能就測一遍，本地測完部署再測一遍。問題越早發現越好處理。

-----

有問題歡迎在 X [@dankopeng](https://x.com/dankopeng) 找我。  
👉 **[vibefast.app](https://vibefast.app)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。