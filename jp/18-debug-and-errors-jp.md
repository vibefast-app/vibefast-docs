# デバッグとエラー対処：AIと一緒に問題を解決する

[English](../en/18-debug-and-errors-en.md) · [繁中](../zh/18-debug-and-errors-zh.md) · [Español](../es/18-debug-and-errors-es.md) · [日本語](../jp/18-debug-and-errors-jp.md) · [Português (BR)](../pt-br/18-debug-and-errors-pt-br.md)

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約9分

-----

## エラーは敵ではない

Vibe Codingをしていると、必ずエラーに遭遇する。

「なぜ動かないのか」「このエラーは何なのか」——初心者のころは赤いエラーメッセージを見るたびに焦る。しかし慣れてくると、エラーは「何が起きているかのヒント」だとわかる。

**エラーメッセージはAIへの最良のインプットだ。**

エラーメッセージをそのままAIに渡すだけで、大半の問題は解決できる。

-----

## エラーの3種類

### 1. シンタックスエラー（文法エラー）

コードの文法が間違っている。

```
SyntaxError: Unexpected token '}'
Expected ',' but got ':'
```

原因：括弧の閉じ忘れ、コンマの漏れ、スペルミスなど。

**対処：** エラーメッセージとコードをそのままAIに渡す。AIがどの行に問題があるかをほぼ確実に特定できる。

### 2. ランタイムエラー（実行時エラー）

コードは文法的に正しいが、実行中に問題が発生する。

```
TypeError: Cannot read properties of undefined (reading 'id')
ReferenceError: env is not defined
```

原因：`null`や`undefined`の値に対してプロパティアクセスしようとした、変数が定義されていない、など。

**対処：** エラーメッセージ + エラーが発生した箇所のコードをAIに見せる。

### 3. ロジックエラー

エラーは出ないが、期待通りに動かない。

例：ショッピングカートに追加したが数量が増えない、ログインしたのにダッシュボードにリダイレクトされない。

**対処：** 「期待した動作」と「実際の動作」を具体的にAIに伝える。

-----

## npm run build：デプロイ前の最初の確認

Cloudflare Workersにデプロイする前に、必ずビルドを通す：

```bash
npm run build
```

このコマンドはTypeScriptの型チェックとバンドルを行い、問題があれば報告する。**ビルドが通らないものはデプロイしても動かない。**

ビルドエラーが出たら：

```
以下のビルドエラーが出ました。原因と修正方法を教えてください：

[エラーメッセージを全文コピー]
```

-----

## エラーへの対処フロー

### ステップ1：エラーメッセージを全文コピーする

一部だけ見て判断しない。エラーメッセージには重要なコンテキストが含まれているので、全文をAIに渡す。

```
# 悪い依頼
「エラーが出ました。直してください」

# 良い依頼
以下のエラーが出ました：

TypeError: Cannot read properties of null (reading 'email')
    at getUserEmail (api/src/routes/auth.ts:45:20)
    at handleLogin (api/src/routes/auth.ts:23:14)

このコードです：
[関連するコードを貼り付け]

原因と修正方法を教えてください。
```

### ステップ2：何をしようとしたかを伝える

```
[エラーメッセージ]

このエラーは「ユーザーがログインしようとしたとき」に発生します。
ログインフォームの送信ボタンを押すと表示されます。
```

### ステップ3：修正前に理由を理解する

AIが修正コードを提案したとき、「なぜこの修正が必要なのか」を確認する：

```
提案された修正を適用する前に、なぜこのエラーが起きたのか
わかりやすく説明してください。
```

原因を理解すると、同じようなエラーを次から自分で対処できるようになる。

-----

## console.logを使ったデバッグ

何が起きているかわからないとき、`console.log`で状態を確認する：

```typescript
export async function handleLogin(request: Request, env: Env) {
  const body = await request.json()
  console.log('Login attempt:', { email: body.email })  // ← 確認ポイント
  
  const user = await getUserByEmail(env, body.email)
  console.log('User found:', user ? 'yes' : 'no')  // ← 確認ポイント
  
  if (!user) {
    return Response.json({ error: 'User not found' }, { status: 404 })
  }
  
  // ...
}
```

### Cloudflare Workersのログを見る

本番環境でのログを見るには：

```bash
npx wrangler tail
```

このコマンドを実行しながら本番環境でリクエストを送ると、リアルタイムでログが表示される。`console.log`で出力した内容もここに表示される。

または、Cloudflare Dashboard → Workers → 対象のWorker → Logs タブからも確認できる。

-----

## よくあるCloudflare Workersのエラー

**`Error: Script startup exceeded CPU time limit`**  
Workers起動時のCPU時間が制限を超えた。無限ループや重い処理が起動時に実行されていないか確認する。

**`Error: D1_ERROR: no such table: xxx`**  
テーブルが存在しない。マイグレーションを実行したか確認する：
```bash
npx wrangler d1 migrations apply DB
```

**`500 Internal Server Error`（詳細なし）**  
本番環境では詳細なエラーは表示されない。`wrangler tail`でログを確認する。

**`CORS error`**  
フロントエンドからバックエンドAPIへの呼び出しでCORSエラーが出る場合、Service Bindingを使っているかどうかを確認する。Service Bindingを使えばCORSは不要だ。

**`TypeError: Cannot read properties of undefined`**  
Cloudflare WorkersのlocalオブジェクトでAPIキーや環境変数が設定されていない可能性。`wrangler.toml`または`.dev.vars`（ローカル開発用）の設定を確認する。

-----

## AIへの高度なデバッグ依頼

単純なエラーではなく「なぜか動かない」場合の聞き方：

```
デバッグを手伝ってください。

問題：ユーザーがカートに商品を追加しても、カートページに商品が表示されない。

動作確認：
- 追加ボタンを押すとAPIにリクエストが送られている（Network タブで確認）
- APIは200を返している
- しかしカートページをリロードしても商品が表示されない

関連するコード：
[カート追加のAPIコード]
[カートページのloaderコード]

何が原因として考えられるか、確認すべき順番で教えてください。
コードは書かないでください。まず診断を教えてください。
```

「まず診断を教えてください」と伝えることで、AIが見当違いのコードを書き始める前に原因の仮説を立てさせることができる。

-----

## デバッグを早く終わらせるための習慣

**1. 変更は1つずつ**  
複数の変更を同時に試すと、どの変更が効いたかわからなくなる。

**2. ブラウザのDevToolsを使う**  
`F12`（またはMac: `Cmd+Opt+I`）でDevToolsを開く。NetworkタブでAPIのリクエスト・レスポンスを確認できる。Consoleタブでエラーを確認できる。

**3. 本番デバッグより先にローカルデバッグ**  
本番環境でデバッグするよりローカルの方が情報が多い。ローカルで再現できるなら、先にローカルで解決する。

**4. 「最近何を変えたか」を思い出す**  
「直前のコミットから何が変わったか」を確認することで、原因を絞れることが多い：
```bash
git diff HEAD~1
```

-----

**Danko Peng**  
[@dankopeng](https://x.com/dankopeng) · [vibefast.app](https://vibefast.app)

-----

👉 [チュートリアルトップに戻る](../README-jp.md)

完全なCloudflareフルスタックテンプレートを今すぐ使い始めたい方へ：  
👉 **[vibefast.app](https://vibefast.app)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
