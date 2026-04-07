# ⚙️ プロジェクト標準

プロジェクト標準を徹底することは、React アプリケーションのコード品質・一貫性・スケーラビリティを維持するうえで重要です。ベストプラクティスを定義して遵守することで、コードベースを清潔・整理された・メンテナブルな状態に保てます。

#### ESLint

ESLint は JavaScript のリントツールとして、コード品質の維持とコーディング標準への準拠を支援します。`.eslintrc.js` にルールを設定することで、よくあるエラーの検出と防止、コードの正確性の確保、一貫したコーディング習慣の促進が可能です。

[ESLint 設定のコード例](../apps/react-vite/.eslintrc.cjs)

#### Prettier

Prettier はプロジェクト全体のコードフォーマットを統一するツールです。IDE の「保存時にフォーマット」機能を有効にすると、`.prettierrc` の設定に従ってコードが自動整形されます。自動整形が失敗する場合は構文エラーのサインです。Prettier は ESLint と組み合わせて、コーディング標準の適用とコードフォーマットを同時に管理できます。

[Prettier 設定のコード例](../apps/react-vite/.prettierrc)

#### TypeScript

ESLint は JavaScript の言語関連のバグを検出するのに有効ですが、JavaScript の動的な性質上、複雑なプロジェクトではすべてのランタイムデータの問題を検出できない場合があります。これを補うために TypeScript を推奨します。TypeScript は大規模なリファクタリング時に気づきにくい問題を見つけるのに役立ちます。リファクタリングの際はまず型宣言を更新し、その後プロジェクト全体の TypeScript エラーを解消してください。なお TypeScript はビルド時に型チェックを行い開発の信頼性を高めますが、ランタイムエラーの防止は保証しません。

#### Husky

Husky は git フックを実装・実行するためのツールです。コミット前にコードバリデーションを実行することで、品質の低いコミットがリポジトリにプッシュされるのを防げます。リント・コードフォーマット・型チェックなどのタスクをコードプッシュ前に実行できます。設定方法は[こちら](https://typicode.github.io/husky/#/?id=usage)を参照してください。

#### 絶対パスインポート

絶対パスインポートは常に設定して使用してください。ファイルの移動が楽になり、`../../../component` のような複雑な相対パスを避けられます。ファイルをどこに移動しても、すべてのインポートはそのまま動作します。設定方法:

TypeScript プロジェクト (`tsconfig.json`):

```json
"compilerOptions": {
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"]
    }
  }
```

`@/*` を使う利点は、短くて他の node_modules と区別しやすく、複数のパスを設定する必要がない点です。`src/components/my-component` のファイルは `../../../components/my-component` の代わりに `@/components/my-component` でアクセスできます。

#### ファイル命名規則

プロジェクト内のファイル名とフォルダ名の命名規則を ESLint で強制できます。例えば、すべてのファイルを `kebab-case` で命名することを強制できます。

```js
'check-file/filename-naming-convention': [
  'error',
  {
      '**/*.{ts,tsx}': 'KEBAB_CASE',
  },
  {
      // babel.config.js や smoke.spec.ts のような中間拡張子のファイル名は無視する
      ignoreMiddleExtensions: true,
  },
],
'check-file/folder-naming-convention': [
  'error',
  {
    // src 配下のすべてのフォルダ（__tests__ を除く）は kebab-case
    'src/**/!(__tests__)': 'KEBAB_CASE',
  },
],
```
