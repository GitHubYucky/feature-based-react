# Structure Pattern

このリポジトリの現行パターン:

- feature ごとに `routes.ts`, `views`, `components`, `hooks`, `types`
- 集約ルーターは `src/router.tsx`
- 共通レイアウトは `src/core/layouts`

新機能追加時の原則:

- 既存の `features/Post` と `features/Product` を基準にする
- 先に新しい抽象層を増やさない
- 要求がなければ `api/`, `store/`, `service/` は増やさない

