# Vibe Coderのセキュリティ入門：公開前に確認すべきこと

**著者：** Danko Peng（[@dankopeng](https://x.com/dankopeng)）  
**更新：** 2026年3月  
**読了時間：** 約9分

-----

## セキュリティはAIに任せられない

Vibe Codingの強力なところは、AIが多くのことを代わりにやってくれることだ。しかしセキュリティだけは例外だ。

**AIはセキュリティの脆弱性を見逃すことがある。** AIが生成したコードは動くが、攻撃に対して安全かどうかは別の問題だ。

この記事は完全なセキュリティガイドではない。Vibe Coderが最低限知っておくべき、公開前に確認すべき基本事項をまとめたものだ。

-----

## 1. シークレットキーをコードに直接書かない

これは繰り返すほど重要なことだ。

```typescript
// ❌ 絶対にダメ
const stripe = new Stripe('sk_live_xxxxxxxxxxxxxxxxxx')
const jwtSecret = 'my_secret_password'
```

```typescript
// ✅ 正しい方法
const stripe = new Stripe(env.STRIPE_SECRET_KEY)
const jwtSecret = env.JWT_SECRET
```

シークレットは必ず環境変数から読み込む。ローカルは`.env`ファイル、本番はCloudflare Workersのシークレット設定を使う。

詳細は[第09回：環境変数とシークレット管理](./09-environment-variables-and-secrets-jp.md)を参照。

-----

## 2. ユーザーの入力は必ず検証する

ユーザーから受け取るすべてのデータは「信頼できないデータ」として扱う。

### SQLインジェクション

```typescript
// ❌ 危険：ユーザー入力を直接SQLに埋め込む
const email = req.body.email
const user = await db.execute(`SELECT * FROM users WHERE email = '${email}'`)

// ✅ 安全：パラメータ化クエリを使う
const user = await env.DB.prepare(
  'SELECT * FROM users WHERE email = ?'
).bind(email).first()
```

攻撃者が`email`に`'; DROP TABLE users; --`を入力した場合、直接埋め込みでは全テーブルが削除される可能性がある。D1の`.bind()`を使えばこのリスクを自動的に防げる。

### XSS（クロスサイトスクリプティング）

ユーザーが入力したHTMLやJavaScriptをそのまま画面に表示してはいけない：

```typescript
// ❌ 危険：ユーザー入力をそのままHTMLに出力
const comment = userInput
return `<div>${comment}</div>`

// ✅ 安全：ReactはデフォルトでXSSをエスケープする
return <div>{comment}</div>
```

RemixとReactを使っている場合、`{}`でJSXに展開する限り、Reactが自動的にエスケープしてくれる。`dangerouslySetInnerHTML`は絶対に使わない（名前通り危険だ）。

-----

## 3. APIエンドポイントの認証と認可

認証（Authentication）と認可（Authorization）は別物だ：

- **認証**：あなたは誰ですか？（ログインしているか）
- **認可**：あなたはこれをしていいですか？（権限があるか）

よくある間違い：

```typescript
// ❌ ログインしているかだけ確認して、権限を確認しない
export async function deleteOrder(request: Request, env: Env) {
  const user = await authenticate(request, env)
  if (!user) return unauthorized()
  
  const orderId = await request.json()
  // 危険：このユーザーがこの注文を削除する権限があるか確認していない
  await env.DB.prepare('DELETE FROM orders WHERE id = ?').bind(orderId).run()
}
```

```typescript
// ✅ 認証と認可を両方確認する
export async function deleteOrder(request: Request, env: Env) {
  const user = await authenticate(request, env)
  if (!user) return unauthorized()
  
  const { orderId } = await request.json()
  
  // 認可：この注文が本当にこのユーザーのものか確認する
  const order = await env.DB.prepare(
    'SELECT * FROM orders WHERE id = ? AND user_id = ?'
  ).bind(orderId, user.id).first()
  
  if (!order) return forbidden()
  
  await env.DB.prepare('DELETE FROM orders WHERE id = ?').bind(orderId).run()
}
```

admin機能には必ずroleの確認を追加する：

```typescript
if (user.role !== 'admin') return forbidden()
```

-----

## 4. レート制限

レート制限なしのAPIエンドポイントは悪用される可能性がある：

- ログインエンドポイントへのブルートフォース攻撃
- フォーム送信の大量スパム
- APIの過負荷攻撃

Cloudflare Workers自体にレート制限機能があり、Cloudflare Dashboardから設定できる。または簡単な実装をAIに頼もう：

```
ログインAPIにレート制限を追加してください。
同一IPアドレスから1分間に最大10回のリクエストに制限してください。
制限を超えた場合は429ステータスを返してください。
Cloudflare Workers上で動作する実装にしてください。
```

-----

## 5. HTTPSは必須

HTTP（暗号化なし）でデータをやりとりすると、通信途中で盗み見られる可能性がある。

Cloudflareを使えばHTTPSは自動で設定される。ただし、確認しておくこと：

Cloudflare Dashboard → SSL/TLS → モードが「Full (strict)」になっているか確認する。

また、HTTPでアクセスしてきたユーザーを自動的にHTTPSにリダイレクトする設定も推奨：

Cloudflare Dashboard → SSL/TLS → Edge Certificates → 「Always Use HTTPS」をオン。

-----

## 6. エラーメッセージに内部情報を含めない

```typescript
// ❌ 危険：詳細なエラー情報を外部に返す
try {
  await processPayment(data)
} catch (err) {
  return Response.json({ 
    error: err.message,  // スタックトレースや内部情報が漏れる可能性
    stack: err.stack
  })
}
```

```typescript
// ✅ 安全：ユーザーには汎用メッセージ、詳細はサーバーログへ
try {
  await processPayment(data)
} catch (err) {
  console.error('Payment processing error:', err)  // サーバーログに記録
  return Response.json({ 
    error: '決済処理に失敗しました。しばらく後でお試しください。'
  }, { status: 500 })
}
```

エラーメッセージに含まれるスタックトレースやSQLエラーの内容は、攻撃者にとって攻撃の手がかりになる。

-----

## 7. 依存パッケージを定期的に更新する

npm パッケージには既知の脆弱性が発見されることがある。定期的に確認しよう：

```bash
# 既知の脆弱性を確認
npm audit

# 自動修正できる脆弱性を修正
npm audit fix
```

重大な脆弱性が出た場合は早めに対応する。AIに「このパッケージをアップデートする安全な方法は？」と聞けば手順を教えてくれる。

-----

## 公開前セキュリティチェックリスト

```
□ コードにAPIキーや秘密情報が直接書かれていない
□ すべてのシークレットが環境変数で管理されている
□ .envが.gitignoreに含まれている
□ D1クエリにすべてパラメータ化（.bind()）を使用している
□ ReactのJSXでdangerouslySetInnerHTMLを使っていない
□ すべてのAPIエンドポイントで認証を確認している
□ admin機能でroleの確認をしている
□ ログインAPIにレート制限がある
□ CloudflareのSSLが「Full (strict)」に設定されている
□ 「Always Use HTTPS」がオンになっている
□ エラーメッセージに内部情報が含まれていない
□ npm auditで重大な脆弱性がないことを確認した
```

-----

## AIにセキュリティレビューを依頼する

コードが完成したら、デプロイ前にAIにセキュリティレビューを依頼しよう：

```
以下のAPIエンドポイントのセキュリティを確認してください。

（コードを貼り付け）

確認してほしい点：
1. SQLインジェクションの可能性はあるか
2. 認証・認可は適切か
3. エラーハンドリングで機密情報が漏れていないか
4. その他のセキュリティリスクはあるか
```

AIは完全ではないが、明らかな問題を指摘してくれることが多い。AIのレビューを受けた後、自分でもチェックリストを確認する。

-----

## まとめ

Vibe Coderが最初に習慣化すべきセキュリティ対策：

1. **シークレットは環境変数**——コードに直接書かない
2. **ユーザー入力は検証**——`.bind()`でSQLインジェクションを防ぐ
3. **認証と認可を両方確認**——ログインしているだけでなく、権限も確認する
4. **HTTPSを強制**——CloudflareのAlways Use HTTPSをオンに
5. **エラー情報は隠す**——詳細はサーバーログ、ユーザーには汎用メッセージ

完璧なセキュリティは存在しないが、基本を押さえるだけで大半の一般的な攻撃を防げる。

-----

質問はX [@dankopeng](https://x.com/dankopeng) まで。  
👉 **[vibefast.app](https://vibefast.app)** — アーリーバード $99、2026年6月1日より $199 に値上がり。
