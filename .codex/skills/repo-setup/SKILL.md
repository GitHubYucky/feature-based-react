---
name: repo-setup
description: 新規セットアップや初回整理時に使う。このリポジトリの docs、.claude、実装構造を読み、必要な設計文書と進め方を整理する。
---

# Repo Setup

## Use this skill when

- ユーザーが初回セットアップを依頼したとき
- リポジトリの全体像を把握して、作業の入口を作る必要があるとき
- docs や運用資産を含めて構造整理したいとき

## Workflow

1. まず `steering` を使い、`.steering/[YYYYMMDD]-[作業名]/` に依頼内容と整理方針を残す。
2. ルート構成、`src`、`docs`、`.claude`、`.codex` を確認する。
3. 現行実装と docs のずれを見つける。
4. 現状説明と目標設計を混同しないように整理する。
5. 必要なら成果物を作る。
6. 次の実装や文書化の入口を明示する。

## Read next when needed

- `../steering/SKILL.md`
- `references/review-scope.md`
- `references/output-files.md`

## Output

- `.steering` に残した依頼内容と整理結果
- リポジトリ全体の要約
- 主な不整合
- 優先アクション
