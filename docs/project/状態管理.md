# 🗃️ 状態管理

状態を効果的に管理することはアプリのパフォーマンス最適化に不可欠です。すべての状態を単一の中央リポジトリに集約するのではなく、用途に応じてカテゴリに分けて管理することを検討してください。

## コンポーネント状態

コンポーネント状態は個々のコンポーネントに固有のもので、グローバルに共有すべきではありません。必要であれば子コンポーネントに props として渡せます。まずコンポーネント自体の中で状態を定義し、他の箇所でも必要になった場合に上位に持ち上げることを検討してください。コンポーネント状態の管理には以下の React フックを使用します:

- [useState](https://react.dev/reference/react/useState) — 独立した単純な状態に
- [useReducer](https://react.dev/reference/react/useReducer) — 1つのアクションで複数の状態を更新する複雑な状態に

[コンポーネント状態のコード例](../apps/react-vite/src/components/layouts/dashboard-layout.tsx)

## アプリケーション状態

アプリケーション状態はグローバルモーダルの制御・通知・カラーモードの切り替えなど、アプリ全体に関わる部分を管理します。最適なパフォーマンスとメンテナンス性のために、状態はそれを必要とするコンポーネントのできるだけ近くに置いてください。最初からすべての状態をグローバル化することは避けてください。

おすすめのアプリケーション状態ソリューション:

- [context](https://react.dev/learn/passing-data-deeply-with-context) + [hooks](https://react.dev/reference/react-dom/hooks)
- [redux](https://redux.js.org/) + [redux toolkit](https://redux-toolkit.js.org/)
- [mobx](https://mobx.js.org)
- [zustand](https://github.com/pmndrs/zustand)
- [jotai](https://github.com/pmndrs/jotai)
- [xstate](https://xstate.js.org/)

[グローバル状態のコード例](../apps/react-vite/src/components/ui/notifications/notifications-store.ts)

## サーバーキャッシュ状態

サーバーキャッシュ状態とは、サーバーから取得してクライアント側にローカルで保存するデータのことです。Redux などの状態管理ストアでリモートデータをキャッシュすることも可能ですが、より効率的なキャッシュ機構を使う方がパフォーマンス向上につながります。

おすすめのサーバーキャッシュライブラリ:

- [react-query](https://tanstack.com/query) — REST + GraphQL
- [swr](https://swr.vercel.app/) — REST + GraphQL
- [apollo client](https://www.apollographql.com/) — GraphQL
- [urql](https://formidable.com/open-source/urql/) — GraphQL
- [RTK](https://redux-toolkit.js.org/rtk-query)

[サーバーキャッシュ状態のコード例](../apps/react-vite/src/features/discussions/api/get-discussions.ts)

## フォーム状態

フォームはあらゆるアプリの重要な構成要素です。フォーム状態の管理には、Formik・React Hook Form・Final Form などのライブラリを使うと、バリデーション・エラーハンドリング・フォーム送信の機能が組み込まれており効率的です。

React のフォームには[制御コンポーネントと非制御コンポーネント](https://react.dev/learn/sharing-state-between-components#controlled-and-uncontrolled-components)があります。

おすすめのフォームライブラリ:

- [React Hook Form](https://react-hook-form.com/)
- [Formik](https://formik.org/)
- [React Final Form](https://github.com/final-form/react-final-form)

ライブラリの機能をラップしてアプリのニーズに合わせた抽象 `Form` コンポーネントと入力フィールドコンポーネントを作成してください。

[フォームのコード例](../apps/react-vite/src/components/ui/form/form.tsx)

[入力フィールドのコード例](../apps/react-vite/src/components/ui/form/input.tsx)

クライアント側のバリデーションには以下のライブラリと組み合わせて使用できます:

- [zod](https://github.com/colinhacks/zod)
- [yup](https://github.com/jquense/yup)

[バリデーションのコード例](../apps/react-vite/src/features/auth/components/register-form.tsx)

## URL 状態

URL 状態とは、ブラウザのアドレスバーに保存・操作されるデータです。URLパラメーター（例: `/app/${dynamicParam}`）またはクエリパラメーター（例: `/app?dynamicParam=1`）として管理されます。react-router などのルーティングソリューションを使うことで、URL 状態に効果的にアクセスし制御できます。

[URL 状態のコード例](../apps/react-vite/src/features/discussions/components/discussion-view.tsx)
