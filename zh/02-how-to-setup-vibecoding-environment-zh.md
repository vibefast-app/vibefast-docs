# 開始 Vibe Coding：從零設定你的開發環境

[English](../en/02-how-to-setup-vibecoding-environment-en.md) · [繁中](../zh/02-how-to-setup-vibecoding-environment-zh.md) · [Español](../es/02-how-to-setup-vibecoding-environment-es.md) · [日本語](../jp/02-how-to-setup-vibecoding-environment-jp.md) · [Português (BR)](../pt-br/02-how-to-setup-vibecoding-environment-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 10 分鐘

-----

## 這篇做完，你的環境就設定好了

很多人在這一步卡住——不是因為太難，而是不知道要做什麼、按什麼順序做。

這篇把所有前置條件一步一步說清楚。跟著做完，你就有一個完整的 Vibe Coding 開發環境，而且 vibefast.app 已經部署到 Cloudflare 上跑起來了。

需要安裝的東西：Node.js、Git、Cursor。需要做的事：clone repo、npm install、npm run setup。就這些。

-----

## 第一步：安裝 Node.js

Node.js 是讓你的電腦能跑 JavaScript 的環境。沒有它，`npm` 和 `npx` 都不能用。

前往 [nodejs.org](https://nodejs.org)，下載 **LTS 版本**（Long Term Support，長期支援版）。

安裝完之後，打開終端機確認：

```bash
node --version
# 應該看到類似 v22.0.0 的版本號

npm --version
# 應該看到類似 10.0.0 的版本號
```

看到版本號就代表安裝成功。

-----

## 先說一下終端機

如果你對終端機（黑色視窗）不熟悉，先不要怕它。

**Mac**：按 `Cmd + Space`，搜尋「Terminal」，打開就是了。  
**Windows**：搜尋「PowerShell」或安裝 [Windows Terminal](https://aka.ms/terminal)。

終端機就是一個讓你用文字指令和電腦溝通的地方。你在這裡輸入指令，電腦執行。整個 Vibe Coding 的開發流程，大概有一半的操作在這裡完成。

-----

## npm 和 npx 是什麼？（30 秒說清楚）

**npm（Node Package Manager）** 是 JavaScript 的套件管理工具。全世界的開發者把自己寫的工具和函式庫放在 npm 倉庫，你可以免費下載使用。

```bash
npm install stripe   # 下載 Stripe 套件到你的專案
npm run dev          # 跑 package.json 裡定義的 dev 指令
```

**npx** 是直接執行一個工具，不需要先安裝它：

```bash
npx wrangler deploy  # 直接執行 wrangler，不需要先 npm install
```

記住這兩個就夠了。遇到其他 npm 指令，讓 AI 解釋。

-----

## 第二步：安裝 Git

Git 是版本控制工具，讓你能 clone 別人的 repo、追蹤自己的 code 變更。

**Mac**：終端機輸入：

```bash
git --version
```

如果 Mac 還沒裝 Git，系統會自動提示你安裝 Xcode Command Line Tools，按照提示安裝就好。

**Windows**：前往 [git-scm.com](https://git-scm.com) 下載安裝。

安裝完確認：

```bash
git --version
# 應該看到類似 git version 2.40.0
```

-----

## 第三步：申請 Cloudflare 帳號

前往 [cloudflare.com](https://cloudflare.com) 免費註冊帳號。

vibefast.app 部署在 Cloudflare 上，你需要一個帳號才能完成 setup。免費方案的額度對起步的 app 完全夠用。

### 開通 R2 使用權限（需要信用卡）

vibefast.app 使用 Cloudflare R2 來儲存檔案和圖片。**R2 預設是關閉的**，需要手動開通，而且開通時 Cloudflare 會要求你綁定信用卡。

步驟：

1. 登入 Cloudflare Dashboard
2. 左側選單點選 **R2**
3. 點選「Purchase R2 Plan」或「Enable R2」，按照頁面指示輸入信用卡資訊

**輸入信用卡後不會立刻扣款。** R2 每月提供免費額度（10 GB 儲存空間、100 萬次讀取操作），在免費額度內不會產生任何費用。信用卡的用途是身份驗證，以及超出免費額度時才計費。對於剛起步的 app，完全不需要擔心費用問題。

-----

## 第四步：Clone vibefast.app

買了 vibefast.app 之後，你會收到 private repo 的 collaborator 邀請。接受邀請，然後在終端機執行：

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app
```

`git clone` 把整個 repo 複製到你的電腦，`cd my-app` 進入那個資料夾。

-----

## 第五步：npm install

```bash
npm install
```

這個指令讀取專案的 `package.json`，把所有需要的套件下載到 `node_modules` 資料夾。

第一次跑可能需要幾分鐘，看到完成訊息就好了。

**為什麼要跑這個？**  
`node_modules` 這個資料夾很大（幾百 MB），不會放在 GitHub 上。每次 clone 一個新專案，都要先跑 `npm install` 把套件下載回來。

-----

## 第六步：npm run setup

```bash
npm run setup
```

這是整個流程最神奇的一步。

加上前面的 clone 和 install，你總共只需要三個指令：

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

`npm run setup` 會自動完成所有事：

1. 引導你登入 Cloudflare 帳號
1. 建立 D1 資料庫，執行 bootstrap SQL
1. 自動生成 JWT secret，寫入 Cloudflare 環境變數
1. 把前端和後端兩個 Workers 同時部署到正式環境

跑完之後，你的 app 已經在 Cloudflare 全球 300+ 個節點上跑起來了。從 clone 到全球上線，三個指令，幾分鐘內完成。

終端機會顯示你的 app URL，打開瀏覽器確認一下。

-----

## 第七步：安裝 Cursor

Cursor 是目前最主流的 AI 編輯器，Vibe Coding 的核心工具。

**Cursor 是付費軟體，每月 $20。** 註冊後可以免費試用 7 天，試用期間功能完整，不需要填信用卡。7 天之後決定要不要繼續訂閱。

前往 [cursor.com](https://cursor.com) 下載安裝，用 email 註冊帳號，開始 7 天試用。

安裝完之後，用 Cursor 打開你的專案：

```bash
cursor .
```

或者在 Cursor 裡選 File → Open Folder，選你的 `my-app` 資料夾。

打開之後，按 `Cmd+L`（Mac）或 `Ctrl+L`（Windows）打開 AI 對話框。試著輸入：

```
這個專案是什麼結構？幫我解釋一下主要的資料夾和檔案。
```

Cursor 會分析你的整個專案，告訴你每個部分是幹什麼的。

-----

## 本地開發

環境設定好之後，之後每次開發：

```bash
npm run dev
```

這會在本地啟動開發伺服器，打開 `http://localhost:5173` 就能看到你的 app。

**本地修改 → 確認沒問題 → `npm run deploy` 部署到正式環境。**

這是基本的開發循環。

-----

## 你現在有了什麼

做完這七步，你有：

- ✅ Node.js 和 npm 安裝好
- ✅ Git 安裝好
- ✅ Cloudflare 帳號
- ✅ vibefast.app 部署到正式環境，全球可以訪問
- ✅ Cursor 安裝好，可以開始 Vibe Coding

接下來，繼續讀下一篇，開始學怎麼用 Vibe Coding 做出你想要的功能。

-----

## 看不懂 Code 怎麼辦？

環境設定好之後，你會開始看到很多不熟悉的 code——TypeScript 的型別標註、async/await、JSX 語法……

**不需要先學會這些才能開始。** 這就是 Vibe Coding 的核心：

遇到看不懂的 code，直接在 Cursor 裡問：

```
解釋一下這段 code 在做什麼，用白話說
```

遇到不知道什麼意思的語法，直接問：

```
async/await 是什麼意思？為什麼要用它？
```

遇到紅色錯誤訊息，直接複製貼給 AI：

```
我遇到這個錯誤：[貼上錯誤]
這是我的 code：[貼上 code]
幫我解釋原因和怎麼修
```

你不需要先讀完所有教程才能動手。邊做邊問，遇到不懂的就問，這是最快的學習方式。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 6 月 1 日漲至 $199。
