# ドメインとDNS設定ガイド：独自ドメインをCloudflare Workersに接続する

[English](../en/12-domain-and-dns-setup-guide-en.md) · [繁中](../zh/12-domain-and-dns-setup-guide-zh.md) · [Español](../es/12-domain-and-dns-setup-guide-es.md) · [日本語](../jp/12-domain-and-dns-setup-guide-jp.md) · [Português (BR)](../pt-br/12-domain-and-dns-setup-guide-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約8分

-----

## ドメインとは何か？

アプリをデプロイすると、最初は`your-app.workers.dev`のようなURLが割り当てられる。これでも動くが、本番サービスとして使うには`yourapp.com`のような独自ドメインが必要だ。

**ドメイン**はインターネット上の住所だ。`vibefast.app`、`google.com`、`github.com`——これらがドメインだ。

ドメインは**ドメインレジストラ**（取得サービス）で購入する。主なレジストラ：
- [Cloudflare Registrar](https://www.cloudflare.com/products/registrar/)（最も統合がシンプル）
- [Namecheap](https://www.namecheap.com)
- [Google Domains](https://domains.google)

**Cloudflare Workersを使うなら、ドメインもCloudflare Registrarで取得するのが最もシンプルだ。** 設定の手順が大幅に省ける。

-----

## DNSとは何か？

ドメインを取得しただけでは、ユーザーがそのドメインにアクセスしたときにどこに接続するかをインターネットは知らない。それを教える仕組みが **DNS（Domain Name System）** だ。

DNSはドメイン名をIPアドレスやサーバーに対応させる電話帳のようなものだ。

**主なDNSレコードの種類：**

| レコードタイプ | 用途 |
|------------|------|
| `A` | ドメインをIPアドレスに紐付ける |
| `CNAME` | ドメインを別のドメインに転送する |
| `MX` | メール受信先を指定する |
| `TXT` | ドメイン所有権の確認などに使う |

Cloudflare WorkersにはIPアドレスがないため、`CNAME`を使う。

-----

## ドメインをCloudflare Workersに接続する手順

### ステップ1：ドメインをCloudflareに追加する

ドメインをCloudflare Registrar以外で取得した場合（NamecheapやGoogle Domainsなど）、まずDNS管理をCloudflareに移管する必要がある。

1. [Cloudflare Dashboard](https://dash.cloudflare.com) にログイン
2. 「サイトを追加」をクリック
3. ドメイン名を入力
4. プランを選択（個人プロジェクトならFreeで十分）
5. CloudflareがDNSレコードを自動スキャンする
6. Cloudflareのネームサーバーをコピーしてドメインレジストラのダッシュボードで設定

ネームサーバーの変更は通常24時間以内に反映されるが、早ければ数分で完了する。

Cloudflare Registrarで取得したドメインは最初からCloudflareのDNSが使われるので、この手順は不要だ。

### ステップ2：WorkerにカスタムドメインのRoutesを設定する

`wrangler.toml`（バックエンドWorker）または`wrangler.json`（フロントエンドWorker）にルートを追加する：

```toml
# wrangler.toml（バックエンドAPIの場合）
name = "my-api"

[[routes]]
pattern = "api.yourapp.com/*"
zone_name = "yourapp.com"
```

```json
// wrangler.json（フロントエンドの場合）
{
  "name": "my-frontend",
  "routes": [
    {
      "pattern": "yourapp.com/*",
      "zone_name": "yourapp.com"
    },
    {
      "pattern": "www.yourapp.com/*",
      "zone_name": "yourapp.com"
    }
  ]
}
```

### ステップ3：デプロイして確認

```bash
npm run deploy
```

デプロイ後、ブラウザで`yourapp.com`にアクセスして確認する。

### ステップ4：Cloudflare DashboardでカスタムドメインをWorkerに紐付ける（推奨方法）

wrangler設定ではなく、Cloudflare Dashboardから設定する方が視覚的にわかりやすい：

1. Cloudflare Dashboard → Workers & Pages
2. 対象のWorkerをクリック
3. 「Settings」→「Triggers」→「Custom Domains」
4. 「Add Custom Domain」をクリック
5. 使いたいドメイン（例：`yourapp.com`）を入力
6. 「Add Custom Domain」を確認

Cloudflareが自動的にDNSレコードを作成してSSL証明書を発行する。

-----

## HTTPS（SSL）は自動

Cloudflareを使う最大のメリットの一つは、**HTTPSが自動で設定される**ことだ。

Let's Encryptなどで証明書を手動で取得・更新する必要はない。Cloudflareがすべて処理してくれる。ドメインを接続した瞬間から`https://yourapp.com`でアクセスできる。

-----

## wwwなしとwwwありのリダイレクト

`yourapp.com`と`www.yourapp.com`は技術的には別のドメインだ。通常はどちらでアクセスしてもメインのURLに統一したい。

Cloudflare Dashboardの「Bulk Redirects」や「Page Rules」を使って、`www.yourapp.com`を`yourapp.com`にリダイレクトできる：

AIに設定方法を聞こう：

```
Cloudflareで www.yourapp.com にアクセスしたとき、
yourapp.com にリダイレクトするにはどう設定しますか？
```

-----

## よくある問題とその解決方法

**「DNS_PROBE_FINISHED_NXDOMAIN」エラー**  
→ DNSの変更がまだ反映されていない。ネームサーバーの変更後は最大24時間待つ。

**「ERR_SSL_PROTOCOL_ERROR」エラー**  
→ CloudflareのSSLモードの設定を確認する。Cloudflare Dashboard → SSL/TLS → 「Full (strict)」に設定する。

**Workerが反応しない（502エラー）**  
→ `wrangler.toml`または`wrangler.json`のルートパターンが正しいか確認する。`npm run deploy`で再デプロイを試みる。

**カスタムドメインでのみ動かない（workers.devは動く）**  
→ Cloudflare DashboardのTriggersタブでカスタムドメインが正しく設定されているか確認する。

-----

## VibeFastでの設定例

VibeFastはフロントエンドとバックエンドの2つのWorkerを使う。独自ドメインを設定する典型的な構成：

```
yourapp.com → フロントエンドWorker（Remix）
api.yourapp.com → バックエンドWorker（API）
```

または、バックエンドをフロントエンドの内部で処理する場合：

```
yourapp.com → フロントエンドWorker（Service Bindingでバックエンドを呼ぶ）
```

Service Bindingを使う場合、バックエンドWorkerには公開URLが不要だ——フロントエンドWorkerから内部的に呼ばれるだけだ。

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
