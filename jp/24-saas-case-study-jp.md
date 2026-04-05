# ケーススタディ：VibeFastでSaaSを作る——InvoiceTrack

[English](../en/24-saas-case-study-en.md) · [繁中](../zh/24-saas-case-study-zh.md) · [Español](../es/24-saas-case-study-es.md) · [日本語](../jp/24-saas-case-study-jp.md) · [Português (BR)](../pt-br/24-saas-case-study-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約9分

-----

## このケーススタディについて

抽象的なチュートリアルより、実際の開発ストーリーを見た方がわかりやすい。

この記事では、フリーランサー向けの請求書管理SaaS「InvoiceTrack」をVibeFastで構築するプロセスを追う。

アイデアから、データベース設計、コア機能の実装、ブランディング、Stripe課金設定まで——実際のVibe Coding開発フローを順番に見ていこう。

-----

## プロダクト概要：InvoiceTrack

**何を解決するのか：** フリーランサーや一人起業家は複数の顧客の請求書を管理するのが難しく、「あの顧客の請求書は支払われたか」「来月の請求予定はいくらか」を一目で把握したい。

**MVPの核心機能：**
- 顧客情報の管理
- 請求書の作成と状態管理（下書き、送付済み、支払い済み、期限超過）
- ダッシュボードで未収金総額を表示
- 期限前のリマインダーメール

**明確に除外：**（MVPでは作らない）
- PDFエクスポート
- 多通貨対応
- チームアカウント

-----

## ステップ1：AIと需要分析書を作る

最初にCursorで、コードを書く前にAIと会話する：

```
先にコードを書かないでください。

InvoiceTrackというフリーランサー向け請求書管理SaaSを作ります。

ターゲット：10〜50社の顧客を持つフリーランサー
技術基盤：VibeFast（Remix + Cloudflare Workers + D1 + Stripe + Resend）

MVP機能：
- 顧客一覧
- 請求書作成・管理（ステータス：draft / sent / paid / overdue）
- ダッシュボード（未収金合計、期限超過の件数）
- 支払い期限7日前リマインダーメール

このプロダクトに必要なデータベーステーブルの設計を提案してください。
各テーブルのカラム、型、関係性を含めてください。
```

AIが提案するスキーマ：

```sql
-- 顧客テーブル
CREATE TABLE clients (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id INTEGER NOT NULL,          -- InvoiceTrackを使っているユーザー
  name TEXT NOT NULL,
  email TEXT NOT NULL,
  company TEXT,
  address TEXT,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP,
  FOREIGN KEY (user_id) REFERENCES users(id)
);

-- 請求書テーブル
CREATE TABLE invoices (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  user_id INTEGER NOT NULL,
  client_id INTEGER NOT NULL,
  invoice_number TEXT NOT NULL,
  status TEXT NOT NULL DEFAULT 'draft',  -- draft / sent / paid / overdue
  due_date TEXT NOT NULL,
  subtotal REAL NOT NULL,
  tax_rate REAL DEFAULT 0,
  total REAL NOT NULL,
  notes TEXT,
  created_at TEXT DEFAULT CURRENT_TIMESTAMP,
  sent_at TEXT,
  paid_at TEXT,
  FOREIGN KEY (user_id) REFERENCES users(id),
  FOREIGN KEY (client_id) REFERENCES clients(id)
);

-- 請求明細テーブル
CREATE TABLE invoice_items (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  invoice_id INTEGER NOT NULL,
  description TEXT NOT NULL,
  quantity REAL NOT NULL,
  unit_price REAL NOT NULL,
  amount REAL NOT NULL,
  FOREIGN KEY (invoice_id) REFERENCES invoices(id)
);
```

設計を確認して納得したら実装へ。

-----

## ステップ2：Layer 1の実装（地盤を作る）

```
# InvoiceTrack 作業計画

## Layer 1 - 地盤
- [ ] D1マイグレーション（clients, invoices, invoice_items）
- [ ] VibeFastのAuth動作確認
- [ ] 環境変数の整理

## Layer 2 - コア機能
- [ ] 顧客一覧（/clients）
- [ ] 顧客追加・編集フォーム
- [ ] 請求書作成フォーム（顧客選択 + 明細行追加）
- [ ] 請求書一覧（/invoices）
- [ ] 請求書ステータスの更新
- [ ] ダッシュボード（/dashboard）
- [ ] リマインダーメール（Cron Trigger）

## Layer 3 - 改善
- [ ] デザインブラッシュアップ
- [ ] 通貨フォーマット
- [ ] 請求書番号の自動採番
```

### マイグレーションの実行

AIに生成させたSQLをマイグレーションファイルとして保存：

```bash
# migrations/0002_invoicetrack_schema.sql を作成
npm run deploy  # スキーマを本番にも反映
```

-----

## ステップ3：コア機能を段階的に実装する

VibeFastはAuthが実装済みなので、すぐに機能実装から始められる。

**顧客一覧ページ（/clients）の実装：**

```
Remixで /clients ページを実装してください。

- D1のclientsテーブルから、ログインユーザーの顧客のみ取得
- テーブル形式で表示（名前、メール、会社名、登録日）
- 右上に「顧客を追加」ボタン
- 空の状態（顧客が0件）のUIも実装してください
- VibeFastの既存スタイル（Tailwind）に合わせてください
```

**請求書作成フォーム（/invoices/new）の実装：**

```
請求書作成ページを実装してください。

- 顧客の選択（ドロップダウン）
- 請求書番号（自動入力、例：INV-2026-001）
- 支払い期限（日付ピッカー）
- 明細行（description, quantity, unit_price → amount を自動計算）
- 明細行を動的に追加・削除できる
- 小計、税率、合計の自動計算
- 「下書き保存」と「確定して送付」の2つのボタン

フォームのデータはRemixのactionでD1に保存してください。
```

-----

## ステップ4：ダッシュボードで重要指標を表示する

```
/dashboard ページを実装してください。

表示する指標：
- 今月の請求合計（sent + paid の合計金額）
- 未収金合計（status = 'sent' で due_date が過去のもの）
- 期限超過の請求書数
- 最近5件の請求書のリスト

D1の集計クエリでこれらの数値を取得してください。
ログインユーザーのデータのみ表示してください。
```

AIが生成するダッシュボードクエリの例：

```sql
-- 未収金合計
SELECT COALESCE(SUM(total), 0) as outstanding
FROM invoices
WHERE user_id = ? 
  AND status IN ('sent', 'overdue')

-- 期限超過数
SELECT COUNT(*) as overdue_count
FROM invoices  
WHERE user_id = ?
  AND status = 'sent'
  AND due_date < date('now')
```

-----

## ステップ5：リマインダーメールの設定

支払い期限7日前にリマインダーメールを自動送信する。Cloudflare WorkersのCron Triggerを使う：

```
Cloudflare WorkersのCron Triggerを使って、
毎朝9時（JST）に支払い期限7日前の請求書に
リマインダーメールを送る機能を実装してください。

- D1から「status = 'sent' かつ due_date = 今日から7日後」の請求書を取得
- 各請求書の顧客メールアドレスにResendでメールを送る
- 送信済みフラグを立てて重複送信を防ぐ
- wrangler.tomlにCron Trigger設定を追加してください
```

`wrangler.toml`に追加される設定：

```toml
[triggers]
crons = ["0 0 * * *"]  # 毎日 UTC 00:00 = JST 09:00
```

-----

## ステップ6：ブランディングとデザイン

機能が動いたらデザインを整える。

```
InvoiceTrackのブランドデザインを整えてください。

ブランドイメージ：シンプル、プロフェッショナル、信頼感
カラー：slate-900（ダーク）+ blue-600（アクセント）
フォント：システムフォントスタック（シンプルに）

以下を実装してください：
- ナビゲーションバー（VibeFastの管理レイアウトを活用）
- ダッシュボードのサマリーカードのデザイン改善
- 請求書一覧テーブルのスタイル改善
- ステータスバッジ（draft:gray, sent:blue, paid:green, overdue:red）
```

-----

## ステップ7：Stripe課金の設定

InvoiceTrackはSaaSなので、月額課金モデルを設定する。VibeFastはStripeが設定済みなので、料金プランを設定するだけだ。

Cloudflare Dashboard環境変数を設定：

```
STRIPE_SECRET_KEY = sk_test_xxxxx
STRIPE_WEBHOOK_SECRET = whsec_xxxxx
STRIPE_CURRENCY = jpy
STRIPE_PRICE_CENTS = 2000  # 月額 ¥2,000
```

VibeFastのSubscription機能が自動的に動作する：
- ユーザーが「プランに加入」をクリック → Stripe Checkoutに誘導
- 支払い成功 → Webhookで`user.is_pro = true`に更新
- 無料枠（例：顧客3社まで）と有料プランの制限を設定

-----

## 完成したプロダクト

VibeFastを使ったことで、通常数週間かかる設定をスキップできた：

**VibeFastが提供したもの（最初から動いていた）：**
- ユーザー認証（メール登録、ログイン、パスワードリセット）
- Stripe Webhookハンドラ
- Resendメール送信の設定
- Cloudflare Workers + D1 + R2のインフラ
- 管理画面の基本レイアウト

**VibeFastの上に実装したもの（今回作ったもの）：**
- 顧客管理
- 請求書CRUD
- ダッシュボード集計
- リマインダーCron Job
- ブランディング・デザイン

開発期間：集中して作業して約3日間。

-----

## SaaSとEコマースの違い

InvoiceTrackのようなSaaSの特徴：

- **課金モデル**：月額サブスクリプション（Stripeのサブスクリプション機能）
- **データ**：ユーザーごとに独立したデータ（マルチテナント）
- **価値提供**：時間を節約し、業務効率を上げる継続的な価値
- **チャーン対策**：ユーザーがデータを蓄積するほどやめにくくなる（lock-in）

Eコマースのケーススタディは次の記事で紹介する。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
