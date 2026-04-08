---
name: feature-add
description: 新機能追加時に使う。既存 feature パターンを調べ、最小変更で実装し、必要な検証と docs 更新まで進める。
---

# Feature Add

## Use this skill when

- ユーザーが新機能追加を依頼したとき
- 既存 feature-based 構成に沿って画面や機能を増やすとき

## Workflow

1. まず `steering` を使い、`.steering/[YYYYMMDD]-[機能名]/` に依頼内容、設計、tasklist を作る。
2. 既存 feature、route、view、hook、type の構成を確認する。
3. 新機能の影響範囲を特定する。
4. テスト先行で最小変更を実装する。
5. 実装ファイル 1 つに対して、原則として対応するテストファイル 1 つを作る。
6. ルーティング、命名、一貫性を確認する。
7. build と lint を回す。
8. 挙動や構造に影響する場合は docs 更新要否を確認する。

## Read next when needed

- `../steering/SKILL.md`
- `references/checklist.md`
- `references/structure-pattern.md`

## Output

- `.steering` に残した依頼内容と進捗
- 変更内容
- 実行した検証
- 残課題
