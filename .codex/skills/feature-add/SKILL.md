---
name: feature-add
description: 新機能追加時に使う。既存 feature パターンを調べ、最小変更で実装し、必要な検証と docs 更新まで進める。
---

# Feature Add

## Use this skill when

- ユーザーが新機能追加を依頼したとき
- 既存 feature-based 構成に沿って画面や機能を増やすとき

## Workflow

1. 既存 feature、route、view、hook、type の構成を確認する。
2. 新機能の影響範囲を特定する。
3. 最小変更で実装する。
4. ルーティング、命名、一貫性を確認する。
5. build と lint を回す。
6. 挙動や構造に影響する場合は docs 更新要否を確認する。

## Read next when needed

- `references/checklist.md`
- `references/structure-pattern.md`

## Output

- 変更内容
- 実行した検証
- 残課題

