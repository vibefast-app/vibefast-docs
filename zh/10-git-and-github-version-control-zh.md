# Git 和 GitHub 入門：Vibe Coder 必須知道的版本控制基礎

[English](../en/10-git-and-github-version-control-en.md) · [繁中](../zh/10-git-and-github-version-control-zh.md) · [Español](../es/10-git-and-github-version-control-es.md) · [日本語](../jp/10-git-and-github-version-control-jp.md) · [Português (BR)](../pt-br/10-git-and-github-version-control-pt-br.md)

**作者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026 年 3 月  
**閱讀時間：** 約 12 分鐘

-----

## 你為什麼需要 Git？

你用 AI 快速寫了一個功能，上線了。

隔天發現這個功能把另一個東西搞壞了。你想回到昨天的版本，但你已經改了幾十個檔案，不知道改了哪些，也不記得原來長什麼樣子。

**Git 就是解決這個問題的。**

Git 是一個**版本控制工具**——它記錄你每一次「存檔」的狀態，讓你隨時可以回到過去任何一個時間點。它跑在你自己的電腦上，不需要網路也能用。

**GitHub** 是一個雲端平台，讓你把 Git 的版本記錄備份到網路上。你的 code 有了雲端備份，換電腦不怕遺失，也可以讓別人看到你的專案。

簡單說：**Git 是工具，GitHub 是倉庫。**

-----

## GitHub 的核心概念

### 倉庫（Repository / Repo）

**倉庫**是 GitHub 上存放一個專案所有 code 和歷史記錄的地方。每個專案對應一個 repo。

你的 repo 可以是：

- **Public（公開）**：任何人都能看到，適合開源專案或教學內容
- **Private（私有）**：只有你和你邀請的人能看到，適合商業產品的原始碼

vibefast-docs 這個 repo 是公開的，裡面的教學內容任何人都能看。vibefast.app 模板的原始碼放在另一個 private repo，只有付費買家才能存取——這也是很多商業模板的交付方式。

### Clone

**Clone** 是把 GitHub 上的 repo 複製一份到你的本地電腦：

```bash
git clone https://github.com/vibefast-app/vibefast-docs.git
```

Clone 之後，你本地就有了這個專案的完整 code 和歷史記錄。

### Fork

**Fork** 是在 GitHub 上把別人的 repo 複製一份到你自己的帳號下，讓你可以在自己的版本上自由修改，不影響原本的 repo。這是開源社群的常見協作方式。

-----

## 第一步：安裝 Git 並設定身份

### 確認 Git 是否已安裝

macOS 通常已內建 Git：

```bash
git --version
# 有輸出版本號碼就是已安裝，例如：git version 2.39.3
```

如果沒有，用 Homebrew 安裝：

```bash
brew install git
```

### 設定你的身份

Git 每次 commit 都會記錄是誰做的。告訴 Git 你是誰（只需要做一次）：

```bash
git config --global user.name "Danko Peng"
git config --global user.email "danko@example.com"
```

email 要和你的 GitHub 帳號一致，這樣 GitHub 才能把 commit 對應到你的帳號。

### 建立 GitHub 帳號

前往 [github.com](https://github.com) 註冊，免費帳號就夠用。

-----

## 第二步：在 GitHub 上建立新 Repo

1. 登入 GitHub，點右上角的 **+** → **New repository**
1. 填 **Repository name**（例如 `my-app`，只能用英文、數字、連字號）
1. 選 **Public** 或 **Private**
1. **不要**勾選「Add a README file」——因為你本地已經有專案了，讓本地的來
1. 點 **Create repository**

GitHub 會建立一個空的 repo，並顯示下一步要跑的指令——你等一下會用到。

-----

## 第三步：git init 並連接到 GitHub

你本地有專案資料夾，GitHub 上有空的 repo，現在把兩者連起來。

### 情境一：本地已有專案，推上 GitHub

```bash
# 進入你的專案資料夾
cd my-app

# 初始化 Git（讓這個資料夾開始被 Git 追蹤）
git init

# 先建好 .gitignore，把不該追蹤的檔案排除（下面會詳細說）
echo ".env" >> .gitignore
echo "node_modules/" >> .gitignore

# 第一次把所有檔案加進來並 commit
git add .
git commit -m "initial commit"

# 連接到 GitHub repo（換成你的帳號名稱和 repo 名稱）
git remote add origin https://github.com/YOUR_USERNAME/my-app.git

# 第一次推上 GitHub
git push -u origin main
```

`git remote add origin` 告訴本地的 Git：「我的雲端備份放在這個 GitHub 網址，幫它取名叫 origin。」

`-u origin main` 只有第一次 push 才需要，之後直接 `git push` 就好。

### 情境二：從 GitHub clone 下來的專案

如果你是從 GitHub clone 的（比如 clone 了 vibefast.app），Git 已經自動幫你連好 origin 了，直接用：

```bash
git add .
git commit -m "my first change"
git push
```

### 確認連接是否成功

```bash
git remote -v
```

輸出應該像這樣：

```
origin  https://github.com/YOUR_USERNAME/my-app.git (fetch)
origin  https://github.com/YOUR_USERNAME/my-app.git (push)
```

-----

## 第四步：每天的工作流程

設定好之後，日常開發就是這三個指令：

### git add .

```bash
git add .
```

告訴 Git：「把我現在改過的所有檔案，準備好要存檔。」

`.` 是「目前目錄下的所有變更」。如果只想加特定檔案：

```bash
git add apps/web/app/routes/blog.tsx
```

### git commit -m “update order features”

```bash
git commit -m "update order features"
```

真正的存檔動作。`-m` 後面是這次改了什麼的說明。

好的 commit 訊息讓三個月後的你看到，馬上知道這次做了什麼：

```bash
# ✅ 清楚
git commit -m "add reading time display to blog posts"
git commit -m "fix order status not updating after Stripe webhook"
git commit -m "remove unused imports in api/users.ts"

# ❌ 沒意義
git commit -m "update"
git commit -m "fix bug"
git commit -m "asdfgh"
```

### git push

```bash
git push
```

把本地的 commit 同步到 GitHub。你的 code 有了雲端備份。

-----

## 查看歷史記錄

```bash
git log --oneline
```

輸出大概長這樣：

```
a3f2c1d add email notification for new orders
9b8e4a2 fix order status not updating after webhook
3d7f1c5 add reading time to blog posts
1a2b3c4 initial commit
```

每一行是一次 commit，前面那串字是 commit ID。想回到某個版本：

```bash
git checkout 9b8e4a2
```

-----

## 重要：push 到 GitHub 的東西有歷史記錄

這是很多人不知道的事，也是最容易造成嚴重問題的地方。

**你刪掉一個檔案，push 上去，不代表那個檔案消失了。**

Git 的設計就是要保留所有歷史。你刪掉的東西，在 commit 歷史裡還找得到。任何人 clone 你的 repo、翻看舊的 commit，都能看到那個檔案曾經存在的內容。

而且 GitHub 上有自動化程式 24 小時掃描新 push 的內容，專門尋找洩漏的 API key。從你 push 的那一刻起，就可能已經被掃描到了。

### 最常見的危險情境

你不小心把 `.env` commit 進去了：

```bash
git add .   # 不小心把 .env 也加進去了
git commit -m "add stripe integration"
git push    # 推上 GitHub
```

然後你發現了，立刻刪掉再 push：

```bash
git rm .env
git commit -m "remove .env"
git push
```

**這樣做沒有用。**

現在的 commit 沒有 `.env`，但上一個 commit 裡還在。任何人翻 commit 歷史，就能看到你的 Stripe key、JWT secret、所有秘密。

### 正確的補救步驟

**第一步（最重要）：立刻去對應平台撤銷那個 key，重新生成新的。** 不管後面怎麼清理 git 歷史，都要先做這件事。key 已經洩漏，必須視為無效。

**第二步：從歷史裡徹底移除。** 用 `git filter-repo`：

```bash
# 安裝
pip install git-filter-repo

# 從所有 commit 歷史裡移除 .env
git filter-repo --path .env --invert-paths

# 強制推上 GitHub（覆蓋歷史）
git push --force
```

但最好的方法是**一開始就不讓它進 git**。

-----

## 防範於未然：.gitignore

`.gitignore` 告訴 Git 哪些檔案永遠不要追蹤：

```
# .gitignore
.env
.env.local
.env.production
node_modules/
.wrangler/
dist/
```

**關鍵順序：在 `git init` 之後、第一次 `git add .` 之前，先建好 `.gitignore`。**

如果你先 `git add .` 再建 `.gitignore`，`.env` 可能已經被加進追蹤了，之後加進 `.gitignore` 也沒用。順序很重要。

vibefast.app 的 `.gitignore` 已經設定好了，你不需要手動加。自己建新專案時記得這個步驟。

-----

## push 之前掃一眼

```bash
# 看這次準備 commit 的檔案
git status

# 看具體改了什麼內容
git diff --staged
```

養成 push 之前看一眼 `git status` 的習慣，確認沒有不該上去的檔案混進來。

-----

## 總結

**從零開始的完整流程：**

```bash
# 1. 在 GitHub 網頁建立新 repo

# 2. 本地初始化
cd my-app
git init
echo ".env" >> .gitignore    # 先建 .gitignore
echo "node_modules/" >> .gitignore
git add .
git commit -m "initial commit"

# 3. 連接 GitHub 並推上去
git remote add origin https://github.com/YOUR_USERNAME/my-app.git
git push -u origin main

# 4. 之後每天
git add .
git commit -m "說清楚你改了什麼"
git push
```

**最重要的認知：GitHub 有完整歷史，刪掉不等於消失。** API key 一旦推上去，就算立刻刪掉，那個 key 也必須視為已洩漏——立刻撤銷重生成，沒有例外。

預防永遠比補救容易：`.gitignore` 在第一次 commit 前設定好，push 之前看一眼 `git status`。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [回到教程首頁](../README-zh.md)

想直接上手完整的 Cloudflare 全棧模板？  
👉 **[vibefast.app](https://vibefast.app/pricing)** — 早鳥 $99，2026 年 8 月 1 日漲至 $199。
