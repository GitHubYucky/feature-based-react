---
name: setup-project
description: `.claude/commands/setup-project.md` 相当。初回セットアップ時に、アイデア確認から 6 つの永続ドキュメント作成までを順番に進める。
---

# Setup Project

## Use this skill when

- ユーザーが `.claude` の `/setup-project` 相当を Codex で実行したいとき
- 初回セットアップとして設計文書一式をまとめて整備したいとき
- `docs/ideas/` の初期要件から、開発開始に必要な文書群を順番に作りたいとき

## Goal

- `docs/ideas/` の有無を確認し、入力ソースを明確にする
- 必要なら `brainstorm` で `docs/ideas/initial-requirements.md` の草案を固める
- 設計文書に入る前に、技術要素を対話形式で決める
- 次の 6 文書を順番に作成または更新する
  - `docs/product-requirements.md`
  - `docs/functional-design.md`
  - `docs/architecture.md`
  - `docs/repository-structure.md`
  - `docs/development-guidelines.md`
  - `docs/glossary.md`

## Workflow

1. まず `docs/ideas/` 配下の Markdown を確認する。
2. `docs/ideas/initial-requirements.md` がなければ `brainstorm` を使い、初期要件の草案を固める。
3. `prd-writing` を使い、`docs/product-requirements.md` のドラフトを作る。
4. PRD は後続文書の基準になるため、ユーザーに確認を求め、承認後に先へ進む。
5. 技術要素の対話に入る。次の観点を 1 回で詰め込みすぎず、2-4 個ずつ質問して決める。
   - フロントエンド構成
   - バックエンド有無と API 方式
   - 認証方式
   - データ保存先と DB
   - デプロイ先
   - 監視、分析、外部サービス連携
   - 制約: 予算、運用体制、既存資産、避けたい技術
   - 問い方は `references/technology-decision-questions.md` のテンプレートを使う
6. 対話で決まった内容を短く整理し、未決定事項と合わせてユーザー確認を取る。
7. 決定済みの技術要素を前提に `functional-design` を使い、`docs/functional-design.md` を作る。
8. 同じ決定事項を前提に `architecture-design` を使い、`docs/architecture.md` を作る。
9. `repository-structure` を使い、`docs/repository-structure.md` を作る。
10. `development-guidelines` を使い、`docs/development-guidelines.md` を作る。
11. `glossary-creation` を使い、`docs/glossary.md` を作る。
12. 6 文書が揃ったら、作成結果と次の入口を短く整理して返す。

## Rules

- 既存ドキュメントがある場合は、その構造と内容を優先し、上書きではなく更新として扱う。
- 現在の実装、目標設計、テンプレート由来の記述を混同しない。
- 1 文書ごとに前提ドキュメントを読んでから進む。
- PRD 承認前に後続文書を確定させない。
- 技術要素は一気に決め切ろうとせず、対話で候補、理由、制約を短い往復で詰める。
- 技術選定では、各候補に対して「なぜそれを採るか」「何を採らないか」を最低 1 行ずつ残す。
- 未決定事項が残る場合は、仮決定と保留理由を明示してから後続文書へ進む。
- 実装変更が発生しない文書作業でも、現行 `src` と docs のずれは明示する。

## Read next when needed

- `../brainstorm/SKILL.md`
- `references/technology-decision-questions.md`
- `../prd-writing/SKILL.md`
- `../functional-design/SKILL.md`
- `../architecture-design/SKILL.md`
- `../repository-structure/SKILL.md`
- `../development-guidelines/SKILL.md`
- `../glossary-creation/SKILL.md`

## Output

- 6 つの永続ドキュメントの作成または更新結果
- PRD 承認の有無
- 技術要素の決定一覧と未決定事項
- 現行実装との主要なずれ
- 次に進むべき実装またはレビュー入口
