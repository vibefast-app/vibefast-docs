# APIとは何か？フロントエンドとバックエンドの通信をわかりやすく解説

[English](../en/03-what-is-api-frontend-backend-en.md) · [繁中](../zh/03-what-is-api-frontend-backend-zh.md) · [Español](../es/03-what-is-api-frontend-backend-es.md) · [日本語](../jp/03-what-is-api-frontend-backend-jp.md) · [Português (BR)](../pt-br/03-what-is-api-frontend-backend-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約7分

-----

## 毎日使っている身近な例え

あなたがレストランで食事をする場面を想像してほしい。

自分でキッチンに入って料理するのではなく、ウェイターに何が食べたいかを伝える。ウェイターはあなたの注文をキッチンに届け、料理ができたらウェイターが料理を運んでくる。

**ウェイターがAPIだ。**

APIはフロントエンド（あなた）とバックエンド（キッチン）の間をつなぐ橋だ。フロントエンドはバックエンドがどう動いているかを知る必要はなく、APIとのやりとり方さえわかればいい。

-----

## APIとは何か？

**API（Application Programming Interface）** は2つのシステム間の通信方法を定義した規則の集まりだ。

Webアプリの文脈では通常：

- **フロントエンド**（ブラウザのページ）がリクエストを送る
- **バックエンドAPI**（サーバー）がリクエストを受け取り、処理して、結果を返す

典型的なAPIリクエストはこんな形だ：

```
フロントエンドが聞く：「userId = 123 のユーザーデータをください」
→ GET /api/users/123

バックエンドが答える：「了解、これがそのデータです」
→ { "id": 123, "name": "Danko", "email": "danko@example.com" }
```

-----

## HTTPメソッド：APIに何を伝えているのか？

APIリクエストには4種類の一般的な操作があり、それぞれ異なるHTTPメソッドに対応している：

| メソッド           | 用途         | 例え             |
|------------------|------------|-----------------|
| `GET`            | データを読む  | 「メニューを見せて」  |
| `POST`           | データを追加  | 「これを注文したい」  |
| `PUT` / `PATCH`  | データを更新  | 「別の料理に変えたい」 |
| `DELETE`         | データを削除  | 「この注文をキャンセル」|

-----

## APIレスポンス：バックエンドは何を返すのか？

バックエンドのレスポンスは通常2つの部分を含む：

**HTTPステータスコード**——数字で結果を伝える：

| ステータスコード | 意味                        |
|--------------|---------------------------|
| `200`        | 成功                        |
| `201`        | 作成成功                    |
| `400`        | リクエストに問題がある（Bad Request）|
| `401`        | ログインしていない（Unauthorized） |
| `403`        | 権限がない（Forbidden）        |
| `404`        | 見つからない（Not Found）      |
| `500`        | サーバーエラー（Server Error）  |

**レスポンスの内容**——通常はJSON形式：

```json
{
  "success": true,
  "data": {
    "id": 123,
    "name": "Danko"
  }
}
```

-----

## VibeFastのAPIアーキテクチャ

VibeFastは **Service Binding** を使ってフロントエンドとバックエンドをCloudflare内部で直接通信させ、公開ネットワークを経由しない。

```
ブラウザ
  ↓ HTTPリクエスト
Remix フロントエンドWorker
  ↓ Service Binding（内部、CORSゼロ）
Workers API
  ↓
D1 データベース
```

これが意味すること：

- バックエンドAPIに公開URLがなく、外部から直接アクセスできない
- フロントエンドとバックエンド間でCORSの設定が不要
- APIの呼び出しごとにネットワークの往復が1回減り、速くなる

-----

## サードパーティAPI：あなたのアプリも他のAPIを呼んでいる

あなたのアプリには独自のAPIだけでなく、他のAPIへの呼び出しも含まれる：

- **Stripe API**：「この支払いを処理して」
- **Resend API**：「このユーザーにメールを送って」
- **Cloudflare API**：`npm run deploy` の裏ではCloudflare APIが呼ばれている

これらのサードパーティAPIを使うにはAPIキーが必要だ——だから前の記事でAPIキーは環境変数に安全に保管すると説明した。

-----

## AIでAPIエンドポイントを設計する

これがVibe Codingの実際の応用シーンだ。AIに直接伝えるだけでいい：

> 「Cloudflare Workersに GET /api/posts エンドポイントを作ってください。公開済みの記事をすべて返し、作成日時の降順で並べ替え、1ページ10件にしてください。」

AIはこのようなコードを生成する：

```typescript
// apps/api/src/routes/posts.ts
app.get("/api/posts", async (c) => {
  const page = Number(c.req.query("page") || 1)
  const limit = 10
  const offset = (page - 1) * limit

  const posts = await c.env.DB.prepare(`
    SELECT id, title, slug, created_at 
    FROM posts 
    WHERE status = 'published'
    ORDER BY created_at DESC
    LIMIT ? OFFSET ?
  `).bind(limit, offset).all()

  return c.json({ success: true, data: posts.results })
})
```

自分でこれを書く必要はないが、何をしているかを読めることで、AIの出力が正しいかどうかをより良く判断できる。

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
