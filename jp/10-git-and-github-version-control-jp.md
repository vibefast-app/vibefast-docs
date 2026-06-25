# GitとGitHub：バージョン管理入門

[English](../en/10-git-and-github-version-control-en.md) · [繁中](../zh/10-git-and-github-version-control-zh.md) · [Español](../es/10-git-and-github-version-control-es.md) · [日本語](../jp/10-git-and-github-version-control-jp.md) · [Português (BR)](../pt-br/10-git-and-github-version-control-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約10分

-----

## なぜバージョン管理が必要なのか

Vibe Codingで開発していると、こんな状況が起きる：

- AIに変更を加えてもらったら、以前は動いていた機能が壊れた
- 「あの動いていたときのバージョンに戻したい」
- 「どこで何が変わったのか思い出せない」

これを解決するのが **Git** だ。

Gitを使えば：
- コードの変更履歴をすべて記録できる
- いつでも過去の状態に戻れる
- 何がいつ変わったかを確認できる
- 複数人（またはAIと自分）で同じコードを安全に編集できる

-----

## GitとGitHubの違い

混同しやすいが、GitとGitHubは別物だ：

**Git**：バージョン管理ツール。あなたのコンピュータ上で動く。変更履歴を管理する仕組みを提供する。

**GitHub**：Gitリポジトリのホスティングサービス。コードをオンライン上に保存して、バックアップしたり、他の人と共有したりできる。

例えると：
- **Git** = 日記の書き方（どうやって記録するか）
- **GitHub** = 日記を保管する本棚（どこに保管するか）

-----

## 基本的な用語

**リポジトリ（Repository / Repo）**  
プロジェクトのすべてのファイルとその変更履歴を格納する場所。ローカル（自分のPC）とリモート（GitHub）の両方にある。

**コミット（Commit）**  
特定の時点での変更のスナップショット。「この状態を記録しておく」という操作。

**ブランチ（Branch）**  
メインのコードから分岐した作業場所。実験的な変更を安全に試せる。

**クローン（Clone）**  
GitHubのリポジトリをローカルにコピーする操作。vibefast.appの`git clone`がこれだ。

**プッシュ（Push）**  
ローカルの変更をGitHubに送る操作。

**プル（Pull）**  
GitHubの最新の変更をローカルに取り込む操作。

-----

## 基本的なGitのワークフロー

日々の開発で使うGitのコマンドは5つだけだ：

### 1. 現在の状態を確認する

```bash
git status
```

どのファイルが変更されたか、何がコミット待ちかを確認できる。

### 2. 変更をステージングする

```bash
git add .
```

`.`はすべての変更をステージングする意味。特定のファイルだけなら`git add ファイル名`。

### 3. コミットする

```bash
git commit -m "商品一覧ページのレイアウトを修正"
```

`-m`の後にコミットメッセージを書く。「何をしたか」が後から見てわかるメッセージを書く習慣をつけよう。

### 4. GitHubにプッシュする

```bash
git push
```

ローカルのコミットをGitHubに送る。

### 5. 過去の状態に戻す

```bash
# 直前のコミットを取り消す（変更はローカルに残す）
git revert HEAD

# 特定のコミットIDに戻す
git checkout コミットID
```

-----

## .gitignoreは必須

`.gitignore`はGitにトラッキングさせないファイルを指定するファイルだ。

**必ず.gitignoreに含めるもの：**

```bash
# .gitignore
.env
.env.local
.env.production
node_modules/
.wrangler/
dist/
.DS_Store
```

vibefast.appには設定済みの`.gitignore`が含まれているが、新しい機密ファイルを作った場合は自分で追加する。

-----

## コミットメッセージの書き方

良いコミットメッセージは後から見ても何をしたかわかる：

```bash
# ✅ 良い例
git commit -m "ユーザー登録フォームにメール確認フィールドを追加"
git commit -m "Stripe Webhookの署名検証エラーを修正"
git commit -m "商品画像をR2にアップロードする機能を実装"

# ❌ 悪い例
git commit -m "fix"
git commit -m "変更"
git commit -m "update"
```

AIと一緒に開発していると、たくさんの変更を一気に加えることになる。こまめにコミットして、各コミットに何をしたかを記録しておくと、問題が起きたときに特定しやすい。

-----

## こまめにコミットする習慣

Vibe Codingでの推奨ワークフロー：

```
1つの機能・修正を完成させる
↓
動作確認する
↓
git add . && git commit -m "〇〇を実装"
↓
次の機能・修正へ
```

**コミット間隔の目安：**
- 1つの機能が完成したらコミット
- 動いていたものを壊したらすぐに気づけるように、変更が大きくなる前にコミット
- 作業を終えるときは必ずコミット

「あとでまとめてコミットしよう」と思っていると、どこで何が壊れたかわからなくなる。

-----

## GitHubリポジトリのセットアップ

vibefast.appを購入した場合は既にリポジトリがある。新しいプロジェクトをゼロから始める場合：

```bash
# 新しいGitリポジトリを初期化
git init

# GitHubでリポジトリを作成してからリモートを追加
git remote add origin https://github.com/あなたのユーザー名/リポジトリ名.git

# 最初のコミットとプッシュ
git add .
git commit -m "Initial commit"
git push -u origin main
```

-----

## フォークとクローンの違い

**クローン（Clone）**：リポジトリをローカルにコピーする。元のリポジトリに紐付いたまま。

**フォーク（Fork）**：GitHubで他人のリポジトリを自分のアカウントにコピーする。自分の管理下に置いて自由に変更できる。

vibefast.appはcloneして使う（フォークではない）。

-----

## 秘密のキーがコミット履歴に入ってしまったら

これは実際によくある事故だ。

**手順1：即座にAPIキーを無効化**  
各サービス（Stripe、Resend等）のダッシュボードで、該当のキーを無効化し、新しいキーを発行する。これが最優先だ。

**手順2：コミット履歴を書き換える（上級）**  
`git-filter-repo`などのツールを使って履歴から機密情報を削除できるが、これは複雑なので最初はAIに手順を聞きながら実行する。

**手順3：新しいキーを環境変数に設定**  
無効化したキーの代わりに新しいキーを環境変数に設定する。

一番の対策は「事前に起こさない」こと。`.env`を`.gitignore`に追加し、コードに直接キーを書かない習慣を徹底する。

-----

## Cursorを使った日常のGit操作

CursorにはGit操作のUIが内蔵されている（Source Control パネル）。ターミナルでコマンドを打つのが苦手な場合は、このUIからコミット、プッシュ、差分確認ができる。

また、AIに頼むのも手だ：

```
git add して git commit して git push するコマンドを教えてください。
コミットメッセージは「Stripeの決済フローを実装」にしてください。
```

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年8月1日より $199 に値上がり。
