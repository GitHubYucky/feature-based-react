# Codex Skill Migration

最終更新: 2026-04-08

このリポジトリでは、`.claude` の command と reviewer の考え方を、Codex でも再現しやすいように skill ベースへ分解した。

## 追加した skill

- `.codex/skills/setup-repo`
- `C:\Users\regis\.codex\skills\setup-project`
- `.codex/skills/add-feature`
- `.codex/skills/review-doc`
- `.codex/skills/validate-implementation`

## 役割の対応

- `.claude/commands/setup-project.md`
  - `setup-project`
  - `setup-repo`
- `.claude/commands/add-feature.md`
  - `add-feature`
- `.claude/commands/review-docs.md`
  - `review-doc`
- `.claude/agents/doc-reviewer.md`
  - `review-doc`
- `.claude/agents/implementation-validator.md`
  - `validate-implementation`

## 使い分け

- 共通ルールは `AGENTS.md`
- 作業手順は `.codex/skills/*/SKILL.md`
- 詳細観点は各 `references/`

## 方針

- 1 skill 1 目的に分ける
- 長い説明を `SKILL.md` に詰め込みすぎない
- 実装より大きい理想論は `references/` 側に逃がす
- 現行実装の把握を優先する
