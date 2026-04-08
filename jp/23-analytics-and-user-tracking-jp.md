# アナリティクスとユーザートラッキング：Cloudflareの無料ツールで使用状況を把握する

[English](../en/23-analytics-and-user-tracking-en.md) · [繁中](../zh/23-analytics-and-user-tracking-zh.md) · [Español](../es/23-analytics-and-user-tracking-es.md) · [日本語](../jp/23-analytics-and-user-tracking-jp.md) · [Português (BR)](../pt-br/23-analytics-and-user-tracking-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約7分

-----

## 「誰が使っているか」を知らずに改善できない

アプリをデプロイした後に聞かれる最初の質問：

- ユーザーは実際に来ているのか？
- どのページが一番見られているか？
- エラーが頻繁に出ているページはないか？
- ユーザーはどの国から来ているか？

これらを知ることなく、何を改善すべきかを判断するのは難しい。

幸いなことに、Cloudflareはすでに2種類の無料アナリティクスを提供していて、何も設定しなくても使える。

-----

## 2種類のCloudflareアナリティクス

### Cloudflare Workers Analytics（バックエンド向け）

Cloudflare Workers Analyticsは、バックエンドAPIのリクエストを自動的に追跡する。

**表示される内容：**
- 総リクエスト数と成功率
- HTTPステータスコードの分布（200、404、500など）
- レスポンス時間（パーセンタイル）
- CPUタイムの使用状況

**確認場所：**
Cloudflare Dashboard → Workers & Pages → 対象のWorker → Metrics タブ

**この情報で何ができるか：**
- 500エラーが急増していれば、デプロイで何かが壊れた
- レスポンスタイムが長いエンドポイントがあれば、クエリ最適化が必要かもしれない
- 特定の時間帯にトラフィックが集中していれば、そのパターンを把握できる

### Cloudflare Web Analytics（フロントエンド向け）

Cloudflare Web Analyticsはページビュー（ユーザーのブラウザ側のアクセス）を追跡する。

**表示される内容：**
- ユニークビジター数とページビュー数
- よく見られているページのランキング
- ユーザーの国・地域の分布
- 主なリファラー（どこから来たか：Google、Twitter、Direct等）
- デバイスの種類（デスクトップ / モバイル）

**確認場所：**
Cloudflare Dashboard → Web Analytics → あなたのサイト

**この情報で何ができるか：**
- SEOが機能しているかをリファラー（検索エンジンからの流入）で確認できる
- モバイルユーザーが多いなら、モバイル体験の最適化を優先する
- 特定の商品ページが多く見られているなら、そのページのコンバージョン率を重点的に改善する

-----

## Cloudflare Web Analyticsの設定

Cloudflare Workersを使っていれば、Workers AnalyticsはゼロコンフィグでCloudflare DashboardのMetricsに自動表示される。

Web Analyticsだけは、フロントエンドのHTMLに1行追加する必要がある：

### ステップ1：Web Analyticsを有効化する

Cloudflare Dashboard → Web Analytics → Add a Site でサイトを追加する。

Cloudflareが1行のJavaScriptタグを提供する：

```html
<script defer src='https://static.cloudflareinsights.com/beacon.min.js' 
  data-cf-beacon='{"token": "あなたのトークン"}'></script>
```

### ステップ2：Remixにタグを追加する

vibefast.appのRemixアプリで、`app/root.tsx`の`<head>`に追加する：

```typescript
export function links() {
  return []
}

export default function App() {
  return (
    <html lang="ja">
      <head>
        <Meta />
        <Links />
        {/* Cloudflare Web Analytics */}
        <script
          defer
          src="https://static.cloudflareinsights.com/beacon.min.js"
          data-cf-beacon='{"token": "あなたのトークン"}'
        />
      </head>
      <body>
        <Outlet />
        <ScrollRestoration />
        <Scripts />
      </body>
    </html>
  )
}
```

これだけで、以後のすべてのページビューが自動的にトラッキングされる。

-----

## Cloudflare Web Analyticsのプライバシーへの配慮

Cloudflare Web Analyticsは**クッキーを使わず、個人を識別しない**設計だ。

多くの国ではCookieを使うアナリティクス（Google Analytics等）にはCookieバナーの設置が義務付けられている。Cloudflare Web AnalyticsはCookieレスなので、Cookieバナーが不要だ。

これはユーザー体験とコンプライアンスの両面で大きなメリットだ。

-----

## vibefast.appの管理画面：統合アナリティクスダッシュボード

vibefast.appにはアプリ内の管理画面（`/admin`）にトラフィック分析ダッシュボードが内蔵されている。

**表示されるもの：**
- 過去7日間・30日間のリクエスト数の推移グラフ
- HTTPステータスコードの分布（成功率）
- 平均レスポンスタイム
- 最もリクエストされているエンドポイントのトップ10

Cloudflare Dashboardとvibefast.appの管理画面を行き来する必要なく、アプリ内でトラフィック状況を一目で把握できる。

-----

## データの読み方：実践的なシナリオ

**シナリオ1：エラー率が急増した**

Workers Analyticsで500エラーの件数が通常の5倍になっていることを発見。

→ 最近のデプロイをロールバックして確認：
```bash
git log --oneline -5
# 直近のコミットを確認

git revert HEAD
npm run deploy
```

→ `wrangler tail` でリアルタイムエラーログを確認：
```bash
npx wrangler tail
```

**シナリオ2：特定のページだけ遅い**

Web Analyticsでは問題ないが、Workers Analyticsで`/api/products/featured`のレスポンスタイムだけが300msを超えている。

→ AIにD1クエリの最適化を依頼：
```
GET /api/products/featured の処理が遅いです。
現在のクエリはこれです：[クエリを貼り付け]
インデックスの追加や、クエリの最適化方法を提案してください。
```

**シナリオ3：モバイルユーザーが70%を占めることがわかった**

Web Analyticsでデバイス分布を確認したら、モバイルユーザーが70%だった。しかしモバイルでのUIは開発時にあまりテストしていなかった。

→ モバイルのUIをChromeDevToolsのDevice Modeで確認し、重要なページを修正する。

-----

## Google Analyticsと比較した場合

|               | Cloudflare Web Analytics | Google Analytics |
|---------------|--------------------------|-----------------|
| 料金          | **無料**（Cloudflareを使っていれば）| 無料（GA4）      |
| セットアップ  | 1行追加のみ              | スクリプト追加 + 設定 |
| Cookie        | **不使用**               | 使用（バナーが必要）|
| リアルタイム  | ✅                        | ✅               |
| ページビュー  | ✅                        | ✅               |
| イベントトラッキング| 基本機能              | 詳細な設定が可能  |
| ユーザー行動フロー| ❌                       | ✅               |
| eコマーストラッキング| ❌                    | ✅               |

**推奨：** Cloudflare Analytics（Workers + Web）を基本として使い、より詳細なユーザー行動分析が必要になったらGA4を追加する。多くのインディーアプリの段階では、Cloudflare Analyticsで十分だ。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
