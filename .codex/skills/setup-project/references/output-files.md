# Output Files

`setup-project` で基準にする設計文書一覧。

配置先は原則として `docs/continuous/` 配下に統一し、ファイル名は日本語にする。

## 6 つの基準文書

1. `docs/continuous/プロダクト要求定義書.md`
- プロダクト要求定義。
- `docs/ideas/` や初期ヒアリングを具体化した基準文書。

2. `docs/continuous/機能設計書.md`
- ユーザー体験、機能仕様、主要フローの整理。
- PRD の内容を実装可能な機能設計へ落とす。

3. `docs/continuous/アーキテクチャ設計書.md`
- 技術構成、責務分割、主要な設計判断。
- 未決定がある場合は仮決定を明記する。

4. `docs/continuous/リポジトリ構造定義書.md`
- リポジトリの責務分割、主要ディレクトリ、配置ルール。
- `src`、`docs`、`.claude` など管理対象の関係を明文化する。

5. `docs/continuous/開発ガイドライン.md`
- 開発フロー、レビュー観点、実装ルール、検証方針。
- 既存の運用ルールが散在しているならここへ寄せる。

6. `docs/continuous/用語集.md`
- 用語、略語、ロール名、ドメイン概念の統一定義。

## 判断ルール

- `docs/continuous/` 配下の対象ファイルが未存在なら新規作成候補として扱う。
- 既存の英語名ファイルや `docs/project/` 配下文書が実質同等なら、対応関係を整理してから `docs/continuous/` 側へ寄せる。
- 既存文書が詳細で、同名文書を新設すると重複が増える場合は、今回は新設せず対応表を残してもよい。
- 6 文書すべてをその場で完成させる必要はないが、少なくともどれを作るか、どれを更新するか、どれを保留にするかは明確にする。

## 下位 skill の対応

- PRD: `prd-writing`
- 機能設計: `functional-design`
- アーキテクチャ: `architecture-design`
- リポジトリ構造: `repository-structure`
- 開発ガイドライン: `development-guidelines`
- 用語集: `glossary-creation`
