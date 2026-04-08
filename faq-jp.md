# よくある質問

[English](./faq.md) · [繁中](./faq-zh.md) · [日本語](./faq-jp.md) · [Español](./faq-es.md) · [Português (BR)](./faq-pt-br.md)

-----

## 製品について

### vibefast.app はオープンソースですか？

いいえ。vibefast.app template は商用製品です。この公開リポジトリは紹介と学習リソースの入口としてのみ機能しています。

### 公開リポジトリとプライベートリポジトリの違いは？

公開リポジトリには製品紹介と Vibe Coding チュートリアルが含まれています。プライベートリポジトリには完全なソースコードとすべてのバイヤー向けドキュメントが含まれており、購入後に提供されます。

### vibefast.app template を自分の製品に使えますか？

はい — それが主な用途です。ほとんどのバイヤーは、自分の製品としてリブランドするか、カスタム SaaS や Web アプリの基盤として使用しています。

### デフォルトのモジュールをすべて残す必要がありますか？

いいえ。vibefast.app template は完全なリファレンス製品として提供されますが、必要に応じてモジュールを保持、調整、または削除できます。例えばブログが不要なら、他の機能に影響なく削除できます。

### ここですべての技術的詳細が公開されますか？

いいえ。このリポジトリは公開向けの紹介とチュートリアルのみです。完全なアーキテクチャドキュメント、デプロイガイド、APIリファレンスはすべてプライベートリポジトリ内にあります。

-----

## 購入について

### どうやって購入しますか？支払い後どうやってコードを取得しますか？

[vibefast.app](https://vibefast.app) で Stripe 決済を完了してください。支払い後、システムが自動的にあなたのメールに GitHub 招待を送信します。招待を受け入れるとプライベートリポジトリにアクセスできます。プロセス全体が自動化されており、通常数分で完了します。

10分以上経っても招待が届かない場合は、迷惑メールフォルダを確認するか、[hello@dankoai.com](mailto:hello@dankoai.com) にご連絡ください。

### アーリーバード価格は期間限定ですか？

はい。$99 の価格は 2026年6月1日まで有効で、その後 $199 に値上がりします。今買うか6月以降に買うかで $100 の差があります。

### 一回払いに何が含まれますか？

$99 に含まれるもの：プライベートリポジトリへのアクセス、完全なソースコード、すべてのバイヤー向けドキュメント、そしてすべての将来のテンプレートアップデート。月額料金なし。隠れたコストなし。

### 購入前に live demo で何が見られますか？

無料登録して live backend に入れます。中では Analytics、Blog、Media を直接体験できます。Business と User エリアは limited-access mode で sample data が表示され、保護された操作は引き続き制限されます。

### 返金ポリシーはありますか？

これは購入後すぐにソースコードにアクセスできるデジタル製品のため、原則として返金は行っておりません。購入後に技術的な問題が発生した場合は、まず [hello@dankoai.com](mailto:hello@dankoai.com) にご連絡ください — 問題解決に全力を尽くします。

-----

## 技術について

### vibefast.app template を使うにはどの程度の技術的背景が必要ですか？

このリポジトリのチュートリアルを読んで理解できる程度で十分です。vibefast.app template は Vibe Coder にとって使いやすいように設計されています — Cursor や Claude などの AI ツールと組み合わせれば、深いエンジニアリング経験がなくても動かせます。`npm install` を実行でき、AI が生成したコードの内容を理解できれば十分です。

### 有料の Cloudflare アカウントは必要ですか？

いいえ。Cloudflare の無料プランで実際のアプリを立ち上げるには十分です — Workers は1日10万リクエスト、D1 は1日10万読み取り、R2 は月100万オペレーション。トラフィックが実際に増えてからアップグレードすれば良いです。

### vibefast.app template は将来他のスタックをサポートしますか？

vibefast.app template は Cloudflare フルスタック（Remix + Workers + D1 + R2）に特化しています — これがコアポジショニングです。他のスタックやデプロイプラットフォームのサポートは現在のロードマップにはありません。

### 複数のプロジェクトで vibefast.app template を使えますか？

はい。現在のライセンスはマルチプロジェクト利用をサポートしています。個人プロジェクトでもクライアントワークでも、自由に使えます。

### vibefast.app template は ShipFast や Supastarter などの他のボイラープレートとどう違いますか？

vibefast.app template は Cloudflare のフルスタック上にネイティブに構築されています（Remix + Workers + D1 + R2）。主な違い：真のワンコマンドデプロイ（`npm run setup`）、エッジでのゼロコールドスタート、別途データベースホスティング不要、Service Binding によるフロントエンドと API 間のゼロ CORS 通信。汎用的な template ではなく、明確な主張を持つ、本番環境で検証済みのアーキテクチャです。

-----

## サポートについて

### 購入後に質問がある場合、誰に連絡すればいいですか？

以下の方法でご連絡いただけます：

- メール：[hello@dankoai.com](mailto:hello@dankoai.com)
- X：[@dankopeng](https://x.com/dankopeng)

### vibefast.app template はどのくらいの頻度で更新されますか？

vibefast.app template は継続的改善モデルを採用しています。すべてのアップデートはプライベートリポジトリにプッシュされ、追加料金なしで購入に含まれます。

### 購入前に Vibe Coding を理解したいです。どこから始めればいいですか？

ここから始めてください：

- [Vibe Coding とは？](./jp/01-what-is-vibecoding-jp.md) — コアコンセプトとキーワード
- [なぜ Cloudflare が Vibe Coding に最適なのか](./jp/05-the-best-way-to-vibecoding-on-cloudflare-jp.md) — 実践的な紹介

### 今購入しました。最初にすべきことは？

[クイックスタートガイド](./quickstart-jp.md) に従ってください — 10分以内にクローンからライブアプリまで到達できます。

-----

一般的な質問は [GitHub Issues](https://github.com/vibefast-app/vibefast-docs/issues) でお気軽にどうぞ。  
購入後の技術的な問題はプライベートリポジトリで issue を作成するか、[hello@dankoai.com](mailto:hello@dankoai.com) にメールしてください。  
または [@dankopeng](https://x.com/dankopeng) に直接ご連絡ください。
