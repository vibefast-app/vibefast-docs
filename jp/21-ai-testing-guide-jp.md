# アプリのテスト方法：AIにcurlコマンドで全機能を検証させる

[English](../en/21-ai-testing-guide-en.md) · [繁中](../zh/21-ai-testing-guide-zh.md) · [Español](../es/21-ai-testing-guide-es.md) · [日本語](../jp/21-ai-testing-guide-jp.md) · [Português (BR)](../pt-br/21-ai-testing-guide-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約7分

-----

## デプロイした後、本当に動いているか確認できているか？

デプロイした後にブラウザを開いて数回クリックして、壊れていなければ大丈夫だと思っている人が多い。

しかしブラウザは見えている部分しかテストできない。APIは正しいデータを返しているか？フロントエンドのページは本当に200を返しているか？ログイン失敗時に正しいエラーコードを返しているか？これらはクリックするだけでは確認できない。

私の習慣は：**機能を1つ作り終えたら、すぐにcurlでテストする。** ローカル開発でも本番環境でも、curlはどこでも使えて結果が一目瞭然だ。

-----

## curlとは何か？

curlはターミナルでHTTPリクエストを送るツールで、MacとLinuxにはプリインストールされており、Windowsにもある。

npmパッケージのインストールも不要、コードを書く必要もなく、1行のコマンドでどんなURL——フロントエンドのページもバックエンドのAPIも——をテストできる。

**そしてcurlの構文を覚える必要はない。AIに生成させるだけでいい。**

-----

## ステップ1：AIにcurlコマンドを生成させる

機能を作り終えたら、AIに直接伝えるだけだ：

```
手工芸品ECサイトを https://soapco.com にデプロイしました。

以下の機能をcurlでテストするコマンドを生成してください：
1. トップページが正常に返るか
2. 商品一覧API（GET /api/products）
3. 単一商品詳細（GET /api/products/lavender-soap）
4. ログインAPI（POST /api/auth/login）、メール: test@example.com, パスワード: test123
5. ログインで取得したtokenを使って、要ログインの注文一覧API（GET /api/orders）をテスト
6. tokenなしで注文一覧を叩いて弾かれるか確認（401が返るべき）
```

AIが完全なcurlコマンドのセットを生成するので、ターミナルにコピペして実行するだけだ。

**これがVibe Coderのテストの核心的なマインドセット：何をテストするかはあなたが決め、どうテストするかはAIが書く。**

-----

## curlはAPIだけでなくフロントエンドページもテストできる

curlはバックエンドAPIだけのものだと思っている人が多いが、フロントエンドのページもテストできる：

```
以下のフロントエンドページをcurlでテストしてください：
1. トップページ https://soapco.com/ が200を返すか
2. 商品一覧ページ /shop が正常に返るか
3. 存在しないページ /xyz が404を返すか（200ではない）
4. /admin 管理ページが未ログイン状態でリダイレクトされるか（302または401が返るべき、200ではない）
```

AIが生成するコマンドの例：

```bash
# トップページのテスト
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/
# 期待値：200

# 存在しないページのテスト
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/xyz
# 期待値：404

# 管理ページが未ログインでアクセスできないことを確認
curl -s -o /dev/null -w "%{http_code}" https://soapco.com/admin
# 期待値：302または401、200ではない
```

`-o /dev/null` でHTMLコンテンツを捨て、`-w "%{http_code}"` でステータスコードだけを表示する——フロントエンドのテストは通常ステータスコードが合っているかだけを確認すればいい。

-----

## レスポンス全体を確認したいとき

APIが返す内容を確認したいときは：

```
GET https://soapco.com/api/products のレスポンスJSONを
読みやすく整形して表示するcurlコマンドを生成してください。
```

AIが生成する：

```bash
curl -s https://soapco.com/api/products | jq
```

`jq` はJSONを整形するツール。Macでは `brew install jq` でインストール、またはAIに「jqをインストールする方法を教えてください」と聞けばいい。

-----

## ステータスコードと本文を同時に確認したいとき

APIが200を返しているのか401なのか不明なとき：

```
以下のエンドポイントを叩いて、HTTPステータスコードとレスポンス本文を
同時に表示するcurlコマンドを生成してください：
POST https://soapco.com/api/auth/login
body: {"email": "test@example.com", "password": "wrongpassword"}
```

AIが生成する：

```bash
curl -i -X POST https://soapco.com/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email": "test@example.com", "password": "wrongpassword"}'
```

`-i` でHTTPステータスコードとヘッダーを本文と一緒に表示する。

-----

## ローカル開発でも同じ、URLを変えるだけ

デプロイを待たずに、ローカルで動いていればすぐにテストできる：

```
ローカルの商品APIをcurlでテストするコマンドを生成してください。
ローカルは http://localhost:8787 で動いています。
```

AIがURLをlocalhostに変えて、他はすべて同じコマンドを生成する。

**ローカルでテスト → 問題なければデプロイ → 本番のURLで再テスト。**

この2ステップが両方通ってはじめて本当に問題なしと言える。

-----

## 良い習慣：テストコマンドをスクリプトにまとめる

テストする機能が増えてきたら、AIにスクリプトにまとめさせよう：

```
以下のテストをtest.shにまとめてください。
各テストに「合格」または「不合格」を表示し、
最後に何個合格・不合格かを表示してください：

1. トップページが200を返す
2. 商品一覧APIが200を返す
3. 存在しない商品が404を返す
4. tokenなしで注文APIを叩くと401を返す

URLは https://soapco.com
```

デプロイのたびに実行する：

```bash
bash test.sh
```

数秒で主要機能がすべて正常かを確認できる。

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
