# Codex Skill Migration

最終更新: 2026-04-08

このリポジトリでは、`.claude` の command と reviewer の考え方を、Codex でも再現しやすいように skill ベースへ分解した。

## 追加した skill

- `.codex/skills/repo-setup`
- `C:\Users\regis\.codex\skills\setup-project`
- `.codex/skills/feature-add`
- `.codex/skills/doc-review`
- `.codex/skills/implementation-validation`

## 役割の対応

- `.claude/commands/setup-project.md`
  - `setup-project`
  - `repo-setup`
- `.claude/commands/add-feature.md`
  - `feature-add`
- `.claude/commands/review-docs.md`
  - `doc-review`
- `.claude/agents/doc-reviewer.md`
  - `doc-review`
- `.claude/agents/implementation-validator.md`
  - `implementation-validation`

## 使い分け

- 共通ルールは `AGENTS.md`
- 作業手順は `.codex/skills/*/SKILL.md`
- 詳細観点は各 `references/`

## 方針

- 1 skill 1 目的に分ける
- 長い説明を `SKILL.md` に詰め込みすぎない
- 実装より大きい理想論は `references/` 側に逃がす
- 現行実装の把握を優先する
