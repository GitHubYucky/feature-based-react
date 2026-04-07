# Full Repository Analysis

最終確認日: 2026-04-08

## 対象範囲

この文書は `src/` だけではなく、リポジトリ全体を対象にした分析です。

確認対象:

- `/.claude`
- `/docs`
- `/public`
- `/src`
- ルート直下の設定ファイルとメタデータ
- 補助的な管理対象としての `/node_modules` と `/.git`

詳細な中身までは対象外:

- `node_modules` 内のサードパーティ実装
- `.git` 内の Git オブジェクト内部

## 全体要約

このリポジトリは、`src` に feature-based 構成の React アプリを持つ一方で、リポジトリ全体としてはそれ以上の意味を持っています。

構造としては大きく 3 層あります。

1. `src` の実行対象アプリケーションコード
2. `docs` の設計・運用ドキュメント
3. `.claude` の AI 支援ワークフロー資産

アプリ本体は小規模で理解しやすいですが、`docs` と `.claude` はそれよりも大きな設計思想を前提にしています。そのため、現在は次の 3 つにずれがあります。

- 実際に実装されているもの
- docs が説明しているもの
- `.claude` が前提としている運用レベル

最重要の技術的問題は、現時点でもアプリが正常に build できないことです。

## トップレベル構成

```text
/
  .claude/
  .git/
  docs/
  node_modules/
  public/
  src/
  .gitignore
  components.json
  eslint.config.js
  index.html
  package.json
  README.md
  tsconfig.app.json
  tsconfig.json
  tsconfig.node.json
  vite.config.ts
  yarn.lock
```

## フォルダ別分析

### `src`

ここが実行対象のアプリケーション本体です。

現在の構成:

```text
src/
  main.tsx
  router.tsx
  vite-env.d.ts
  core/
    assets/css/App.css
    components/shadcn/
    layouts/
  features/
    Welcome/
    Post/
    Product/
```

確認できた設計:

- `main.tsx` がエントリポイント
- `router.tsx` が feature ごとの route を集約
- `core` が共通レイアウトとスタイルを担当
- `features` が機能単位の分割を担当

良い点:

- feature 単位で追いやすい
- route 分割が単純で分かりやすい
- データ取得の流れが読みやすい

弱い点:

- build が壊れている
- loading、error、empty の状態管理がない
- 命名の一貫性が弱い
- shadcn 由来のファイルが中途半端に入っている

機能の概要:

- `Welcome`: 画面遷移用のランディングページ
- `Post`: JSONPlaceholder から投稿一覧を取得
- `Product`: Fake Store API から商品一覧を取得

### `docs`

`docs` は、現在のコード規模に対してかなり大きいです。

含まれている主な内容:

- アプリ概要
- プロジェクト構造
- プロジェクト標準
- 状態管理
- API レイヤ
- コンポーネントとスタイリング
- テスト
- セキュリティ
- パフォーマンス
- デプロイ
- 開発フローとブランチ運用
- 機能追加手順
- 日本語概要
- リポジトリ分析

評価:

- `docs` は単なるアプリ説明ではなく、より広い設計方針と開発プロセスを含んでいます。
- 多くの文書は、現在のコードベースより大きいアーキテクチャを前提にしています。
- Bulletproof React 系のテンプレートをベースにした記述が混ざっている可能性が高いです。
- そのため、現状では「現行システムの正確な説明」より「理想構成の案内」に近いです。

主な問題:

- docs は将来像やテンプレート構成を説明している一方で、`src` はその一部しか実装していません。

運用上のリスク:

- 初見の開発者が docs を読むと、実装済み範囲を誤解しやすいです。

### `.claude`

このフォルダは重要です。リポジトリ専用の AI 運用設定が入っています。

構成:

```text
.claude/
  agents/
  commands/
  skills/
  settings.json
  settings.local.json
```

#### `.claude/settings.json`

許可されている skill:

- `prd-writing`
- `functional-design`
- `architecture-design`
- `repository-structure`
- `development-guidelines`
- `glossary-creation`

解釈:

- このリポジトリは、文書主導の設計フローを AI で補助する前提を持っています。

#### `.claude/settings.local.json`

ローカル実行が許可されているもの:

- `npm run:*`
- `npm test:*`
- `npx vitest:*`
- `yarn`
- 一部の Git 操作

解釈:

- Claude 主導のローカル自動化を想定しています。
- ただし、一部の設定はこのリポジトリと完全には一致しない別環境由来の痕跡があります。

#### `.claude/commands`

確認できた command:

- `setup-project.md`
- `add-feature.md`
- `review-docs.md`

役割:

- 初期セットアップ
- 機能追加の手順化
- 専用エージェントによる docs レビュー

評価:

- これは単なる補助メモではなく、AI 支援開発の実行手順そのものです。
- ただし、そこで想定されている設計フローは、現在の `src` よりも成熟した開発体制を前提にしています。

#### `.claude/agents`

確認できた agent:

- `doc-reviewer.md`
- `implementation-validator.md`

役割:

- ドキュメントレビュー
- 実装の妥当性検証

評価:

- このリポジトリには、実装だけでなくレビュー観点や品質統制の考え方も含まれています。
- `.claude` が想定している成熟度は、現行アプリ本体より高いです。

#### `.claude/skills`

確認できた skill 群:

- architecture design
- development guidelines
- functional design
- glossary creation
- PRD writing
- repository structure
- steering

評価:

- `.claude` は、このリポジトリを単なるアプリではなく、AI 支援で整備していくための運用基盤として扱っています。
- 実態としてこのリポジトリは次の混成状態です。
- 小規模な React サンプルアプリ
- 大きめの設計ドキュメントテンプレート
- AI ワークフローツール群

### `public`

このフォルダは最小構成です。

- `vite.svg`

評価:

- 標準的な Vite の静的アセット置き場
- 現時点では独自の公開アセットや配信用資産はほぼありません

### `node_modules`

依存関係はすでにインストール済みです。

評価:

- 標準的な依存ツリー
- リポジトリ分析の対象は基本的に実装ではなく、導入済みパッケージの把握までで十分です

`package.json` から分かる依存の方向性:

- React 19
- React Router 7
- Tailwind CSS 4
- Radix UI
- shadcn 関連 utility
- TypeScript
- Vite
- ESLint

### `.git`

通常の Git 管理情報です。

評価:

- バージョン管理には必要
- アプリケーション設計そのものではない
- 主にブランチや履歴の文脈確認に意味がある

## ルート直下ファイル分析

### `package.json`

標準的な Vite 系の manifest です。

script:

- `dev`
- `build`
- `lint`
- `preview`

評価:

- script は最小限
- test script がない
- build 以外の typecheck script がない
- formatter script もない

### `vite.config.ts`

内容:

- React plugin
- Tailwind plugin
- port `3000`
- `@` と `@features` の alias

評価:

- 実行時設定としては妥当
- ただし TypeScript 側の alias 設定と整合していません

### `tsconfig.json` と `tsconfig.app.json`

主な問題:

- `baseUrl` が `src`
- それなのに `paths` が `./src/*` を向いている

これにより `src/src/...` のような誤解決になっている可能性があります。

影響:

- TypeScript の path alias import が失敗
- build が失敗

### `eslint.config.js`

評価:

- modern flat config
- TypeScript と React Hooks は見ている
- ただし repository architecture を強制するほどの厳しさはない

### `components.json`

評価:

- shadcn/ui を入れようとしていることは分かる
- ただし必要な utility 層が揃っていません

### `README.md`

評価:

- feature-based React boilerplate として説明している
- テンプレート由来の説明を流用した部分がある可能性が高い
- 現在の実装状態を完全には反映していません

補足:

- `README.md` や一部 docs は、端末上で文字化けして見えました

## 検証結果

実行したもの:

- `npm run build`
- `npm run lint`

### Build

`npm run build` は失敗しました。

確認できた原因:

- TypeScript の alias 設定不整合
- shadcn component から参照される `core/lib/utils` が存在しない

代表的な失敗:

- `@/core/...` の import
- `@features/...` の import
- `core/lib/utils` の import

### Lint

`npm run lint` は warning のみで完了しました。

確認できた warning:

- `src/core/components/shadcn/button.tsx` の `react-refresh/only-export-components`
- `src/router.tsx` の `react-refresh/only-export-components`

## 横断的な所見

### 1. リポジトリの性格が混在している

このリポジトリは現在、次の 3 つを同時に持っています。

- サンプルアプリ
- 設計/運用テンプレート
- Claude 運用ワークスペース

それぞれ単体では成立しますが、互いの前提がまだ揃っていません。

### 2. `.claude` の方がアプリ本体より成熟している

`.claude` 側は次を前提にしています。

- 仕様書ベースの設計
- 手順化された実装フロー
- 検証とレビューのゲート

一方、アプリ本体はまだサンプルプロジェクト段階です。

### 3. `docs` がコードと同期していない

docs は充実していますが、その多くが現行実装の説明ではなく、目標構成やテンプレート構成の説明になっています。

### 4. アプリ本体の問題は深い設計問題というより基礎整備の不足

大きな問題は高度な設計崩壊ではなく、次のような基礎項目です。

- alias 設定
- 不足 utility
- UX 状態不足
- 小さな命名や描画上の粗さ

## 推奨アクション

優先順:

1. TypeScript alias 設定を修正する
2. 不足している `core/lib/utils` を追加するか、未使用の shadcn component を整理する
3. まず app を build 可能な状態に戻す
4. `docs` を「現状説明」と「目標設計」に分けるか、どちらを主にするか決める
5. `.claude` を現役の運用基盤として使うのか、テンプレート資産として残すのか整理する
6. `README.md` と主要 docs を現行の実態に合わせる
7. アプリ本体に最低限の改善を入れる
- loading state
- error state
- responsive 対応
- list の key
- debug code や typo の除去

## 結論

このリポジトリは単なる React アプリではなく、コード、ドキュメント、AI ワークフロー資産を合わせ持つ複合的な管理対象です。

軽量な feature-based React starter を目指すなら、全体を簡素化して実装と docs を揃えるべきです。

一方で、AI 支援込みの整ったプロダクトテンプレートを目指すなら、`src` の実装を `.claude` と `docs` が前提としている水準まで引き上げる必要があります。
