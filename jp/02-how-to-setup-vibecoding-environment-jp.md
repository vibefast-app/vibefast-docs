# Vibe Codingを始める：開発環境のゼロからのセットアップ

[English](../en/02-how-to-setup-vibecoding-environment-en.md) · [繁中](../zh/02-how-to-setup-vibecoding-environment-zh.md) · [Español](../es/02-how-to-setup-vibecoding-environment-es.md) · [日本語](../jp/02-how-to-setup-vibecoding-environment-jp.md) · [Português (BR)](../pt-br/02-how-to-setup-vibecoding-environment-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約10分

-----

## この記事を終えれば、環境は整う

多くの人がこのステップで詰まる——難しいからではなく、何をどの順番でやればいいかわからないからだ。

この記事ではすべての前提条件を一つひとつ説明する。やり終えれば、完全なVibe Coding開発環境が整い、VibeFastがCloudflare上でデプロイされて動いている状態になる。

インストールするものはNode.js、Git、Cursor。やることはリポジトリのclone、npm install、npm run setupだけだ。

-----

## ステップ1：Node.jsのインストール

Node.jsはJavaScriptをコンピュータ上で実行するための環境だ。これがないと`npm`も`npx`も使えない。

[nodejs.org](https://nodejs.org) にアクセスして **LTS版**（Long Term Support、長期サポート版）をダウンロードする。

インストール後、ターミナルで確認：

```bash
node --version
# v22.0.0 のようなバージョン番号が表示されればOK

npm --version
# 10.0.0 のようなバージョン番号が表示されればOK
```

バージョン番号が表示されればインストール成功だ。

-----

## ターミナルについて

ターミナル（黒い画面）に慣れていない場合でも心配しなくていい。

**Mac**：`Cmd + Space` を押して「Terminal」を検索して開く。  
**Windows**：「PowerShell」を検索するか、[Windows Terminal](https://aka.ms/terminal) をインストールする。

ターミナルはテキストコマンドでコンピュータと通信する場所だ。ここにコマンドを入力してコンピュータが実行する。Vibe Codingの開発フロー全体の約半分の操作がここで行われる。

-----

## npmとnpxとは？（30秒で解説）

**npm（Node Package Manager）** はJavaScriptのパッケージ管理ツールだ。世界中の開発者が自分のツールやライブラリをnpmリポジトリに公開しており、無料でダウンロードして使える。

```bash
npm install stripe   # Stripeパッケージをプロジェクトにダウンロード
npm run dev          # package.jsonに定義されたdevコマンドを実行
```

**npx** はインストールせずにツールを直接実行する：

```bash
npx wrangler deploy  # wranglerを事前にインストールせずに直接実行
```

この2つを覚えておけば十分だ。他のnpmコマンドに出会ったらAIに説明を求めよう。

-----

## ステップ2：Gitのインストール

Gitはバージョン管理ツールで、他のリポジトリをcloneしたり、自分のコード変更を追跡したりできる。

**Mac**：ターミナルで入力：

```bash
git --version
```

MacにまだGitがインストールされていない場合、システムが自動的にXcode Command Line Toolsのインストールを促すので、指示に従ってインストールする。

**Windows**：[git-scm.com](https://git-scm.com) からダウンロードしてインストール。

インストール後に確認：

```bash
git --version
# git version 2.40.0 のような表示がされればOK
```

-----

## ステップ3：Cloudflareアカウントの作成

[cloudflare.com](https://cloudflare.com) にアクセスして無料でアカウントを作成する。

VibeFastはCloudflare上にデプロイするため、セットアップを完了するにはアカウントが必要だ。無料プランの枠は起動したてのアプリには十分すぎるほどだ。

### R2の利用を有効化する（クレジットカードが必要）

VibeFastはファイルや画像の保存にCloudflare R2を使用する。**R2はデフォルトで無効**になっており、手動で有効化する必要がある——その際、Cloudflareはクレジットカードの入力を求める。

手順：

1. Cloudflare Dashboardにログイン
2. 左側メニューで **R2** をクリック
3. 「Purchase R2 Plan」または「Enable R2」をクリックし、画面の指示に従ってクレジットカード情報を入力する

**入力してもすぐに請求されることはない。** R2は毎月無料枠が付いている（ストレージ10GB、読み取り操作100万回）。料金が発生するのはこの無料枠を超えた場合のみだ。クレジットカードは本人確認と無料枠超過時の課金のために使われる。起動したてのアプリでは、この枠に近づくこともないだろう。

-----

## ステップ4：VibeFastをClone

VibeFastを購入すると、プライベートリポジトリのコラボレーター招待が届く。招待を承認し、ターミナルで実行：

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app
```

`git clone` でリポジトリ全体をコンピュータにコピーし、`cd my-app` でそのフォルダに移動する。

-----

## ステップ5：npm install

```bash
npm install
```

このコマンドはプロジェクトの`package.json`を読み込み、必要なすべてのパッケージを`node_modules`フォルダにダウンロードする。

初回は数分かかることがある。完了メッセージが表示されればOKだ。

**なぜこれが必要なのか？**  
`node_modules`フォルダは非常に大きいため（数百MB）、GitHubには含まれていない。新しいプロジェクトをcloneするたびに、`npm install`でパッケージを再ダウンロードする必要がある。

-----

## ステップ6：npm run setup

```bash
npm run setup
```

これがフロー全体で最も「魔法的な」ステップだ。

前述のcloneとinstallを合わせて、必要なコマンドは合計3つだけ：

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

`npm run setup` がすべてを自動で完了させる：

1. Cloudflareアカウントへのログインをガイドする
1. D1データベースを作成し、bootstrap SQLを実行する
1. JWT secretを自動生成し、Cloudflareの環境変数に書き込む
1. フロントエンドとバックエンド両方のWorkerを本番環境に同時デプロイする

完了すると、アプリはCloudflareのグローバル300以上のノードで動作している。cloneからグローバルデプロイまで、3つのコマンド、数分で完了だ。

ターミナルにアプリのURLが表示されるので、ブラウザで確認しよう。

-----

## ステップ7：Cursorのインストール

Cursorは現在最も主流のAIエディタで、Vibe Codingのコアツールだ。

**Cursorは有料ソフトウェアで月額$20。** 登録後に7日間の無料トライアルが使えて、トライアル期間中は機能が完全に利用でき、クレジットカードの入力も不要だ。7日後に継続するかどうかを決める。

[cursor.com](https://cursor.com) からダウンロードしてインストールし、メールでアカウントを登録して7日間のトライアルを開始しよう。

インストール後、Cursorでプロジェクトを開く：

```bash
cursor .
```

またはCursorでFile → Open Folderを選択して、`my-app`フォルダを選ぶ。

開いたら、`Cmd+L`（Mac）または`Ctrl+L`（Windows）でAIチャットを開く。試しに入力してみよう：

```
このプロジェクトの構造を教えてください。主要なフォルダとファイルを説明してください。
```

Cursorがプロジェクト全体を分析して、各部分の役割を教えてくれる。

-----

## ローカル開発

環境が整ったら、以降の開発は毎回：

```bash
npm run dev
```

これでローカルの開発サーバーが起動する。`http://localhost:5173` を開けばアプリが見える。

**ローカルで変更 → 問題なければ → `npm run deploy` で本番環境にデプロイ。**

これが基本の開発サイクルだ。

-----

## これで何が手に入ったか

7ステップを終えると、手元には：

- ✅ Node.jsとnpmのインストール完了
- ✅ Gitのインストール完了
- ✅ Cloudflareアカウント
- ✅ VibeFastが本番環境にデプロイ済み、世界中からアクセス可能
- ✅ Cursorのインストール完了、Vibe Codingを始める準備完了

次の記事に進んで、Vibe Codingで欲しい機能を作る方法を学ぼう。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
