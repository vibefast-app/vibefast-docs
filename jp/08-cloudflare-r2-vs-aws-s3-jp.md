# Cloudflare R2 vs AWS S3：なぜR2を選ぶのか

[English](../en/08-cloudflare-r2-vs-aws-s3-en.md) · [繁中](../zh/08-cloudflare-r2-vs-aws-s3-zh.md) · [Español](../es/08-cloudflare-r2-vs-aws-s3-es.md) · [日本語](../jp/08-cloudflare-r2-vs-aws-s3-jp.md) · [Português (BR)](../pt-br/08-cloudflare-r2-vs-aws-s3-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約7分

-----

## ファイルストレージは必ずいつか必要になる

Webアプリを作っていると、必ずファイルを保存したい場面が来る。

- ユーザーのプロフィール画像
- 商品の写真
- ユーザーがアップロードするドキュメント
- アプリが生成するレポートやエクスポートファイル

これらはデータベース（D1）には入れない。データベースは構造化されたデータのためにあり、大きなバイナリファイルには向いていない。画像やファイルは**オブジェクトストレージ**に保存する。

-----

## AWS S3とは何か？

**AWS S3（Simple Storage Service）** は現在最も広く使われているオブジェクトストレージだ。業界標準として確立されており、ほぼすべてのクラウドサービスがS3との互換性を持つか、S3のAPIを参考にしている。

しかし、S3にはある有名な「落とし穴」がある。

-----

## S3の転送料金問題

S3はファイルのアップロードは安い（またはほぼ無料）が、**ダウンロード（転送）は有料だ**。

AWSの転送料金：
- 最初の10TB/月：$0.09/GB
- 次の40TB/月：$0.085/GB

計算してみよう。もしあなたのアプリが1日100万回の画像表示をするとして、各画像が100KBなら：

```
1,000,000 × 100KB = 100GB/日
100GB × $0.085 = $8.5/日
月額コスト ≈ $255
```

アプリが成功すれば成功するほど、転送コストが増えていく。これはインディー開発者にとって予想外のコストになりやすい。

-----

## Cloudflare R2とは何か？

**R2** はCloudflareが提供するオブジェクトストレージだ。AWS S3と完全に互換性のあるAPIを持ちながら、最大の違いは1つ：

**転送料金がゼロだ。**

R2のコスト構造：
- ストレージ：$0.015/GB/月（無料枠：10GB/月）
- クラス A オペレーション（書き込み）：$4.50/百万回（無料枠：1,000,000/月）
- クラス B オペレーション（読み取り）：$0.36/百万回（無料枠：10,000,000/月）
- **転送料金：$0.00**

アプリが成功して画像が何百万回ダウンロードされても、転送コストは一切かからない。

-----

## S3 API互換性の意味

R2はS3と互換性のあるAPIを持つ。これが意味することは：

S3用に書かれたコードを、エンドポイントのURLを変えるだけでR2で動かせる。

例えば `aws-sdk` を使ったコード：

```typescript
// S3用
const s3 = new S3Client({
  region: 'us-east-1',
  credentials: {
    accessKeyId: 'xxx',
    secretAccessKey: 'xxx'
  }
})

// R2用（エンドポイントを変えるだけ）
const s3 = new S3Client({
  region: 'auto',
  endpoint: `https://${ACCOUNT_ID}.r2.cloudflarestorage.com`,
  credentials: {
    accessKeyId: R2_ACCESS_KEY_ID,
    secretAccessKey: R2_SECRET_ACCESS_KEY
  }
})
```

既存のS3のコードをR2に移行するコストが非常に低い。

-----

## WorkersからR2を操作する（ネイティブバインディング）

vibefast.appのようにCloudflare Workers上でアプリを動かしている場合、S3 API互換の方法より**さらに簡単な方法**がある。WorkersのネイティブR2バインディングだ：

```typescript
// ファイルのアップロード
async function uploadFile(env: Env, key: string, file: ArrayBuffer, contentType: string) {
  await env.R2.put(key, file, {
    httpMetadata: { contentType }
  })
  return key
}

// ファイルの取得
async function getFile(env: Env, key: string) {
  const object = await env.R2.get(key)
  if (!object) return null
  return object.body
}

// ファイルの削除
async function deleteFile(env: Env, key: string) {
  await env.R2.delete(key)
}
```

HTTPリクエストもAPIキーも必要ない。R2はWorkersの環境変数として直接使える。

-----

## vibefast.appでのR2の使い方

vibefast.appには**メディアライブラリ**機能が内蔵されている。

管理画面からファイルをアップロードすると、自動的にR2に保存される。画像のURLを取得してデータベースに保存し、フロントエンドで表示する——このフローがすでに実装済みだ。

自分で実装する必要があるのは、そのURLを商品レコードや記事レコードに紐付ける部分だけだ：

```typescript
// 商品作成時にR2の画像URLを保存
await env.DB.prepare(`
  INSERT INTO products (name, price, image_url)
  VALUES (?, ?, ?)
`).bind(name, price, imageUrl).run()
```

-----

## R2の制限も知っておく

R2は万能ではない。知っておくべき制限：

**オブジェクトサイズの上限**  
1オブジェクトあたり最大5TB。通常のWebアプリでは問題にならない。

**一貫性モデル**  
R2はグローバルに整合性のある読み取りを提供するが、大量の同時書き込みがある場合の一貫性に注意が必要（通常は問題ない）。

**ディレクトリ概念なし**  
S3と同様、R2もフラットなキースペースだ。`images/products/item-1.jpg`のようにスラッシュを含むキーを使えばディレクトリのように見せることはできるが、実際にはフォルダ構造は存在しない。

-----

## どちらを選ぶか：シンプルな判断基準

**Cloudflare Workers上でアプリを動かしているなら、R2一択だ。**

理由：
1. 転送料金ゼロで長期的なコストが読みやすい
2. WorkersネイティブバインディングでSDKが不要
3. 同じCloudflareダッシュボードで管理できる
4. Workersとのデータやりとりに余計なネットワーク往復が不要

AWS上でアプリを動かしているなら、S3は自然な選択だ。しかしVibe CodingでCloudflareを選んだなら、R2で統一するのが最も効率的だ。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
