# Cloudflare Workersと従来のサーバーの違い

[English](../en/06-cloudflare-workers-vs-traditional-server-en.md) · [繁中](../zh/06-cloudflare-workers-vs-traditional-server-zh.md) · [Español](../es/06-cloudflare-workers-vs-traditional-server-es.md) · [日本語](../jp/06-cloudflare-workers-vs-traditional-server-jp.md) · [Português (BR)](../pt-br/06-cloudflare-workers-vs-traditional-server-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約8分

-----

## 「サーバーレス」とは何か？

「サーバーレス」という言葉を聞いたことがあるかもしれない。これはサーバーが存在しないという意味ではなく、**あなたがサーバーを管理しなくていい**という意味だ。

従来の開発では：
- VPSやEC2インスタンスを借りる
- OSをインストールして設定する
- Nginxやアプリサーバーをセットアップする
- セキュリティパッチを適宜あてる
- スケールアップが必要になったら手動で対応する

これらがすべてなくなる。コードを書いてデプロイするだけだ。

-----

## Cloudflare WorkersとAWS Lambdaの違い

「サーバーレス」と聞いてAWS Lambdaを思い浮かべる人も多いだろう。WorkersとLambdaは同じカテゴリに見えるが、実は重要な違いがある。

### コールドスタート問題

AWS Lambdaの最大の問題の一つが**コールドスタート**だ。

Lambdaは一定時間リクエストがないと「スリープ」状態になる。次のリクエストが来たとき、コンテナを起動してコードをロードするために数百ミリ秒から数秒の遅延が発生する。ユーザーにとっては、最初のアクセスが遅いという不満につながる。

Cloudflare Workersは異なるアーキテクチャを使っている——V8 Isolatesと呼ばれる軽量な実行環境で、コールドスタートがほぼゼロだ。リクエストは常に即座に処理される。

### 実行場所

| | AWS Lambda | Cloudflare Workers |
|---|---|---|
| 実行場所 | 選択したリージョン（例：us-east-1）| 世界300以上のエッジノード |
| ユーザーとの距離 | リージョンによっては遠い | 常に最寄りのノード |
| 遅延 | リージョン依存 | グローバルに低遅延 |

### コスト構造

AWS Lambdaは実行時間（ミリ秒単位）と実行回数で課金される。加えてAPI Gateway、CloudWatch、データ転送など周辺サービスのコストも発生する。

Cloudflare Workersの無料枠は：
- 1日100,000リクエストまで無料
- 有料プランでも月額$5から

-----

## Workersの制限を正直に話す

WorkersはすべてのユースケースにFit するわけではない。正直に制限を伝えておく。

### CPU時間の制限

- 無料プラン：リクエストごとに最大10ms CPU時間
- 有料プラン（Workers Paid）：リクエストごとに最大30秒 CPU時間

**向いていない処理：**
- 動画のエンコード・デコード
- 大量データの集計処理
- AIモデルの推論（重いもの）
- 長時間実行が必要なバッチ処理

これらはWorkersではなく、専用のサービス（Cloudflare AI、バックグラウンドキューなど）を使う。

### Node.js APIの制限

WorkersはNode.jsではなく、V8 Isolatesで動く。そのため一部のNode.js APIは使えない。

よく使われるもので使えないもの：
- `fs`（ファイルシステム）→ R2を使う
- `child_process` → Workers上では不可
- 一部のnpmパッケージ（Node.js専用のもの）

ほとんどのWebアプリでは問題にならないが、Node.js固有の機能に依存したパッケージを使いたい場合は注意が必要だ。

### メモリ制限

Workers Freeでは128MB、Workers Paidでは128MBまで。大きなオブジェクトをメモリ上に保持する処理には向かない。

-----

## vibefast.appのアーキテクチャでWorkersをどう使うか

vibefast.appでは2つのWorkerが動いている：

**フロントエンドWorker（Remix）**
- ユーザーがアクセスするWebページをSSRで生成
- ルーティング、フォーム処理
- Service Bindingを通じてバックエンドAPIを呼ぶ

**バックエンドWorker（API）**
- REST APIエンドポイントを提供
- D1データベースへの読み書き
- R2へのファイル操作
- Stripe、Resendなどの外部APIとの連携

この分離設計のメリット：
- バックエンドAPIのURLが外部に公開されない（Service Bindingのため）
- フロントエンドとバックエンドを独立してデプロイできる
- 将来的にどちらかだけを変更しやすい

-----

## 「サーバー管理ゼロ」の本当の意味

Workersを使うと何が「なくなる」のかを具体的に挙げる：

**なくなるもの：**
- サーバーのOSアップデート
- セキュリティパッチの適用
- ロードバランサーの設定
- オートスケーリングの設定
- サーバーの死活監視
- ディスク容量の管理
- サーバーの料金（使った分だけ払う）

**残るもの：**
- コードを書くこと
- バグを直すこと
- 機能を追加すること

Vibe Coderにとって、これが最大のメリットだ。**限られた時間をすべてプロダクトに集中できる。**

-----

## デプロイが本当に簡単

従来のサーバーへのデプロイ：

```
1. コードをビルドする
2. SSHでサーバーにログインする
3. ファイルを転送する
4. サービスを再起動する
5. 動いているか確認する
6. 問題があれば同じことをまたやる
```

Workersのデプロイ：

```bash
npm run deploy
```

これだけだ。数十秒でグローバルに展開される。

-----

**Danko Peng**  
[X](https://x.com/dankopeng) · [YouTube](https://www.youtube.com/@DankoPeng) · [Threads](https://www.threads.com/@dankopeng)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app/pricing)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
