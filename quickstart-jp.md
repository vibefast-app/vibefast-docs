# VibeFast クイックスタートガイド

[English](./quickstart.md) · [繁中](./quickstart-zh.md) · [日本語](./quickstart-jp.md) · [Español](./quickstart-es.md) · [Português (BR)](./quickstart-pt-br.md)

**更新：** 2026年3月  
**読了時間：** 約5分

-----

## クローンから本番公開まで、たった3つのコマンド

```bash
git clone https://github.com/vibefast-app/vibefast.git my-app
cd my-app && npm install
npm run setup
```

以上です。

`npm run setup` はVibeFast体験の核心です。手動で行うはずだったすべてを自動で処理します：

- Cloudflareにログインし、アカウントを確認
- D1データベースを作成し、bootstrap SQLを実行、すべてのテーブルを自動構築
- JWTシークレットを生成し、Workers環境変数に書き込み
- フロントエンド（Remix）とバックエンド（Workers API）を同時に本番環境にデプロイ

ターミナルが完了すると、2つのライブURL（フロントエンド用とバックエンドAPI用）が表示されます。あなたのアプリはすでにCloudflareの全世界300以上のエッジロケーションで稼働しています。

-----

## 必要な環境

開始前に以下を確認してください：

- **Node.js 20+**
- **npm 10+**
- **Cloudflareアカウント**（無料プランで十分）
- macOSユーザー：`jq` のインストールが必要（`brew install jq`）

Cloudflareアカウントがまだですか？[こちらから無料登録](https://dash.cloudflare.com/sign-up) — クレジットカード不要です。

-----

## まずは動いているところを見てみませんか？

説明文を鵜呑みにする必要はありません。

[vibefast.app](https://vibefast.app) はVibeFastで完全に構築されています — マーケティングページ、ブログ、料金ページ、ユーザーログイン、ダッシュボードすべてが、このテンプレートの実際の機能として本番環境で動いています。

**無料アカウントを登録**してログインすると、以下が表示されます：

- サイトの直近7日間のリアルなトラフィックデータ
- あなたの登録番号 — 何番目のユーザーか

体験した認証フロー、ダッシュボードUI、ページ速度 — それがまさにあなたが購入するものです。デモではありません。本物の本番環境です。

![VibeFast traffic analytics dashboard](./asset/analytics_light.png)

-----

## 最初の1時間でできること

VibeFastの設計目標はひとつ：**購入者が最初の1時間で、セットアップからカスタマイズされたライブアプリまで到達できること。**

### 0〜10分：インストールとデプロイ

```bash
npm install
npm run setup
```

完了すると、以下が手に入ります：

- Cloudflare上で動作する完全なWebアプリ
- ユーザー、投稿、注文のテーブルが作成済みのD1データベース
- フロントエンドとバックエンドのWorkerが本番環境で稼働
- 今すぐ開けるURL

### 10〜15分：ローカル開発

```bash
npm run dev
```

1つのコマンドでフロントエンドとバックエンドの両方が起動します。ターミナルに表示されるローカルURLを開くと：

- 完全なマーケティングホームページ
- 料金ページ
- ブログシステム
- ユーザー登録とログイン
- 管理ダッシュボード

これらはプレースホルダーではありません。すべての機能が接続され、動作しています。

![VibeFast signup flow](./asset/signup.png)

### 15〜40分：Stripe、Resend、ブランディング

Stripe APIキーとResend APIキーを設定に追加し、`npm run deploy` を実行してから：

1. 設定した管理者メールでアカウントを登録
1. `/admin` を開いてダッシュボードにアクセスできることを確認
1. Stripeテスト決済を実行し、Webhookが発火することを確認
1. 購入確認メールと管理者通知メールの両方が届くことを確認

エンドツーエンドのフローが通れば、あなたのアプリは準備完了です。

ブランディングも簡単です — VibeFastは変更が必要なすべてのテキストを1つの設定ファイルに集約しています：サイト名、ドメイン、料金コピー、ホームページコピー、SEO設定。変更して `npm run deploy` を実行すれば、すべて更新されます。

![VibeFast blog editor](./asset/blogedit.png)

-----

## コマンドリファレンス

|コマンド                    |説明                                    |
|-------------------------|---------------------------------------|
|`npm run setup`          |初回セットアップ：DB作成、シークレット生成、全Workersデプロイ|
|`npm run dev`            |ローカル開発環境の起動（フロントエンド+バックエンド同時）    |
|`npm run deploy`         |本番環境へデプロイ（フロントエンド+バックエンド同時）      |
|`npm run deploy:frontend`|フロントエンドのみデプロイ                       |
|`npm run deploy:backend` |バックエンドのみデプロイ                        |
|`npm run build`          |全パッケージをビルド                          |
|`npm run typecheck`      |プロジェクト全体のTypeScript型チェック             |

-----

## アーキテクチャをもっと深く知りたい方へ

- [なぜCloudflareがVibe Codingに最適なのか](./jp/05-the-best-way-to-vibecoding-on-cloudflare-jp.md) — Next.js + Vercelとの直接比較
- [Cloudflare Workersと従来のサーバーの違い](./jp/06-cloudflare-workers-vs-traditional-server-jp.md) — エッジアーキテクチャの実際のメリット

-----

## 準備はできましたか？

**アーリーバード $99 — 2026年6月1日より $199 に値上がり。**  
一回払い。永久アクセス。プライベートGitHubリポジトリ。将来のすべてのアップデート含む。

👉 **[vibefast.app](https://vibefast.app)**
