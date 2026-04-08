---
name: setup-project
description: 初回セットアップで設計文書群を整備するときに使う。`docs/ideas/` の初期要求や既存の `docs`、`.claude`、`src` を読み、`docs/continuous/` 配下に日本語ファイル名で PRD、機能設計、アーキテクチャ、リポジトリ構造、開発ガイドライン、用語集の作成または更新方針を決め、必要な下位 skill へ接続する。
---

# Setup Project

## Goal

- 初回開発に必要な設計文書の入口を揃える
- `docs/ideas/`、既存 docs、実装の関係を整理する
- 足りない設計文書を新規作成するか、既存文書を更新するか判断する
- 下位 skill を使う順序と前提を明確にする

## Workflow

1. ルート構成、`docs/ideas/`、`docs/`、`.claude/`、`src/` を確認する。
2. `docs/ideas/` の Markdown を読み、要求の有無を把握する。ない場合は、PRD 作成に必要な情報を対話で補う。
3. 既存 docs を読み、現状実装、目標設計、テンプレート由来の記述を分けて整理する。
4. `references/output-files.md` を基準に、`docs/continuous/` 配下の 6 つの設計文書それぞれについて「新規作成」「既存更新」「今回は保留」を決める。
5. PRD が未整備なら `prd-writing` を使って最初に整備する。PRD がある場合はそれを基準文書にする。
6. 必要に応じて `functional-design`、`architecture-design`、`repository-structure`、`development-guidelines`、`glossary-creation` を順に使う。
7. 技術要素が未確定で後続文書が止まる場合だけ `references/technology-decision-questions.md` を使って少数論点ずつ詰める。
8. 最後に、作成または更新した文書、残る未決定事項、次に着手すべきアクションを簡潔にまとめる。

## Rules

- 既存実装と既存 docs を先に確認し、重複文書を機械的に増やさない。
- テンプレートより `src` と実際に運用されている docs を優先する。
- PRD を基準文書として扱い、後続文書は PRD と矛盾しないように作る。
- 一度に全論点を聞かず、未決定事項は仮決定として明記して前に進める。
- 既存文書が別名や別配置で存在する場合は、`docs/continuous/` 配下の日本語名との対応関係を明示してから更新する。
- 目的が skill 移行や初回整備であっても、不要な大規模リファクタはしない。

## Read next when needed

- `references/review-scope.md`
- `references/output-files.md`
- `references/technology-decision-questions.md`

## Output

- 読み取った入力
- 作成または更新対象の設計文書
- 主な不整合や未決定事項
- 次に使うべき下位 skill または優先アクション
