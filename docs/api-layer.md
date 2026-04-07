# 📡 API 層

### API クライアントのインスタンスを一つに統一する

RESTful または GraphQL API と通信する際は、事前に設定済みのAPIクライアントインスタンスを一つだけ作成し、アプリケーション全体で再利用することが望ましいです。たとえば、ネイティブの fetch API や [axios](https://github.com/axios/axios)、[graphql-request](https://github.com/prisma-labs/graphql-request)、[apollo-client](https://www.apollographql.com/docs/react/) などのライブラリを使って、共通設定済みのシングルインスタンスを作成できます。

[API クライアントのコード例](../apps/react-vite/src/lib/api-client.ts)

### API リクエストを定義してエクスポートする

APIリクエストをその場で書くのではなく、個別に定義してエクスポートする形を推奨します。

構造化された形でAPIリクエストを宣言することで、コードが整理されてコロケーション（関連コードをまとめる）が実現します。
各APIリクエスト定義には以下が含まれます:

- リクエスト・レスポンスデータの型定義とバリデーションスキーマ
- APIクライアントインスタンスを使ってエンドポイントを呼び出すフェッチャー関数
- [react-query](https://tanstack.com/query)、[swr](https://swr.vercel.app/)、[apollo-client](https://www.apollographql.com/docs/react/)、[urql](https://formidable.com/open-source/urql/) などのライブラリをベースにフェッチャー関数を呼び出すフック（データ取得・キャッシュを管理）

このアプローチにより、アプリケーション内で定義されているエンドポイントの把握が容易になります。また、レスポンスに型をつけてアプリ全体で型推論することでタイプセーフティが向上します。

[API リクエスト定義 - Query のコード例](../apps/react-vite/src/features/discussions/api/get-discussions.ts)
[API リクエスト定義 - Mutation のコード例](../apps/react-vite/src/features/discussions/api/create-discussion.ts)
