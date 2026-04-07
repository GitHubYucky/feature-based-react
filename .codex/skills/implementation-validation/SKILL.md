---
name: implementation-validation
description: 実装後の検証で使う。仕様との整合、コード品質、検証結果、主要なリスクを確認し、簡潔に報告する。
---

# Implementation Validation

## Use this skill when

- ユーザーが実装レビューや検証を依頼したとき
- 変更後に build、lint、構造整合性を確認したいとき

## Workflow

1. 対象コードと関連仕様を読む。
2. 変更点の意図を整理する。
3. 次を確認する。
- 仕様との整合
- コード品質
- テストや build の状態
- セキュリティ上の気になる点
- パフォーマンス上の明らかな問題
4. 可能ならコマンドを実行する。
5. 問題を優先度順に報告する。

## Read next when needed

- `references/validation-checklist.md`
- `references/report-format.md`

## Output

- Findings
- 検証結果
- 残るリスク

