# 環境変数とシークレット管理：APIキーを安全に扱う方法

[English](../en/09-environment-variables-and-secrets-en.md) · [繁中](../zh/09-environment-variables-and-secrets-zh.md) · [Español](../es/09-environment-variables-and-secrets-es.md) · [日本語](../jp/09-environment-variables-and-secrets-jp.md) · [Português (BR)](../pt-br/09-environment-variables-and-secrets-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約7分

-----

## コードに直接書いてはいけない

Vibe Codingを始めたばかりの人がよくやる間違いがある：

```typescript
// ❌ 絶対にやってはいけない
const stripe = new Stripe('sk_live_xxxxxxxxxxxxxxxxxxxxx')
const resend = new Resend('re_xxxxxxxxxxxxxxxxxxxxxxxx')
```

このコードをGitHubにpushした瞬間、世界中の人がそのAPIキーを見ることができる。悪意のある人がそのキーを使ってStripeで不正な請求を行ったり、Resendで大量のスパムメールを送ったりすることができる。

これは理論上の話ではない。実際にGitHubをスキャンしてAPIキーを探すボットが存在し、公開したその日のうちに悪用されることもある。

-----

## 環境変数とは何か？

**環境変数**とは、コードの外側に保存する設定値のことだ。

コードとキーを分離することで：
- コードはGitHubに公開できる
- キーは自分のコンピュータとサーバーだけに保存される
- チームで開発しても、全員が同じコードで異なるキーを使える

-----

## ローカル開発：.envファイル

ローカル開発では `.env` ファイルを使う：

```bash
# .env
STRIPE_SECRET_KEY=sk_test_xxxxx
RESEND_API_KEY=re_xxxxx
JWT_SECRET=your_random_secret_here
DATABASE_URL=your_d1_database_id
```

このファイルはプロジェクトのルートに置き、コードからは以下のように読み込む：

```typescript
// Workersの場合、環境変数はenv経由でアクセス
const stripe = new Stripe(env.STRIPE_SECRET_KEY)
const resend = new Resend(env.RESEND_API_KEY)
```

### .envファイルは必ずGitIgnoreに追加する

```bash
# .gitignore
.env
.env.local
.env.production
```

`.env`が`.gitignore`に含まれていれば、`git push`してもGitHubには送られない。

**VibeFastには`.gitignore`が設定済みで、`.env`は最初から除外されている。**

-----

## 本番環境：Cloudflare Workers のシークレット

ローカルの`.env`は本番環境には使えない。本番環境のシークレットはCloudflare Workersに直接登録する。

### コマンドラインで設定

```bash
npx wrangler secret put STRIPE_SECRET_KEY
# 入力を求められるので、値を貼り付けてEnter
# sk_live_xxxxx

npx wrangler secret put RESEND_API_KEY
# re_xxxxx

npx wrangler secret put JWT_SECRET
# your_very_long_random_secret
```

このコマンドで設定した値は：
- Cloudflareのサーバーに暗号化して保存される
- `wrangler.toml`には記録されない
- GitHubには絶対に残らない

### Cloudflare Dashboardで設定

コマンドラインが難しければ、ブラウザからも設定できる：

Cloudflare Dashboard → Workers → あなたのWorker → Settings → Variables → Add variable

「Encrypt」のトグルをオンにすると、シークレットとして暗号化保存される。

-----

## wrangler.toml と wrangler.json

`wrangler.toml`（バックエンド）や`wrangler.json`（フロントエンド）には**機密でない**設定を書く：

```toml
# wrangler.toml
name = "my-api"
compatibility_date = "2024-01-01"

[[d1_databases]]
binding = "DB"
database_name = "my-database"
database_id = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

ここに書くのは：
- Workerの名前
- データベースのバインディング設定
- R2バケットの設定
- 互換性の日付

**APIキーやシークレットをここに書いてはいけない。**

-----

## 各サービスのシークレット

VibeFastを使う場合に設定するシークレット一覧：

| 変数名 | 取得場所 | 用途 |
|--------|---------|------|
| `JWT_SECRET` | 自分で生成 | JWT署名 |
| `STRIPE_SECRET_KEY` | Stripe Dashboard → API keys | Stripe決済 |
| `STRIPE_WEBHOOK_SECRET` | Stripe Dashboard → Webhooks | Webhook検証 |
| `RESEND_API_KEY` | Resend Dashboard → API keys | メール送信 |

JWTシークレットの生成方法：

```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

このコマンドが出力する64文字の文字列を`JWT_SECRET`として使う。

-----

## よくある間違い

**間違い1：.envを間違えてpushしてしまった**

もしこれが起きたら、即座に：
1. 該当のAPIキーを無効化（各サービスのダッシュボードで）
2. 新しいキーを発行
3. 新しいキーを環境変数に設定

Gitの履歴からキーを削除することも可能だが（`git-filter-repo`などを使う）、キーの無効化が最優先だ。

**間違い2：本番用キーをテストに使う**

Stripeなどは本番用（`sk_live_...`）とテスト用（`sk_test_...`）の2種類のキーがある。ローカル開発と本番デプロイのテスト段階ではテスト用キーを使い、正式公開の直前に本番用に切り替える。

**間違い3：シークレットをコードのコメントに書く**

```typescript
// ❌ これもダメ
// API Key: sk_live_xxxxx  ← コメントに書いても同じ
const stripe = new Stripe(env.STRIPE_SECRET_KEY)
```

コメントもGitHubに残る。

-----

## チェックリスト

デプロイ前の確認：

```
□ .envが.gitignoreに含まれている
□ コードにAPIキーが直接書かれていない
□ すべてのシークレットがCloudflare Workersに設定されている
□ 本番用とテスト用のキーを区別している
□ GitHubのリポジトリを検索して、キーが漏れていないか確認した
```

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
