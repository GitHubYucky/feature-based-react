---
name: steering
description: ユーザーから AI に出されたあらゆる作業指示について、依頼内容、計画、進捗、振り返りを `.steering/` に記録しながら進めるためのスキル。実装、調査、文書化、レビュー、整理を含む全作業で使う。
---

# Steering

## Use this skill when

- ユーザーから AI への作業指示を受けたとき
- 実装、調査、文書化、レビュー、整理など、何らかの成果物や判断を伴う作業を始めるとき
- 作業内容を `.steering/` に残しながら進めたいとき
- 進捗や判断理由を後から追えるようにしたいとき

## Goal

- `.steering/[YYYYMMDD]-[作業名]/` に依頼内容と計画を残す
- `requirements.md` にユーザー指示の要旨と受け入れ条件を書く
- `design.md` に設計方針や進め方を書く
- `tasklist.md` に作業順序と進捗を残す
- tasklist の全タスク完了まで作業を継続する

## Workflow

1. 今日の日付を確認し、`.steering/[YYYYMMDD]-[作業名]/` を作る。
2. `docs/continuous/` の永続ドキュメントと `docs/project/` の関連ガイドを読む。
3. ユーザーの依頼内容を `requirements.md` の冒頭に明記する。
4. `design.md` に作業方針を書く。実装作業なら設計、テスト駆動方針、実装ファイル 1 つに対してテスト 1 つの原則を書く。
5. `tasklist.md` を今回の作業に必要なタスクだけで具体化する。
6. 実装中は tasklist を正式記録として逐次更新する。
7. 全タスク完了後に振り返りを追記する。

## Rules

- `.steering/` は AI への全作業指示で作成する。
- `.steering/` には必ず「今回の依頼内容」を残す。
- 実装作業では tasklist でテスト先行を基本にする。
- 実装作業では、実装ファイル 1 つに対して、原則として対応するテストファイル 1 つを作る。
- tasklist を更新せずに次のタスクへ進まない。
- 「時間の都合」で未完了タスクを残さない。

## Read next when needed

- `templates/requirements.md`
- `templates/design.md`
- `templates/tasklist.md`

## Output

- 作成した `.steering` ディレクトリ
- 更新した tasklist の進捗
- 実行した検証
