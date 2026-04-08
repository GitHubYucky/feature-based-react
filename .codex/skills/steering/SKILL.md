---
name: steering
description: ユーザー指示ごとの作業計画、依頼内容、タスクリストを `.steering/` に記録しながら進めるためのスキル。計画時、実装時、検証時に使う。
---

# Steering

## Use this skill when

- ユーザーから機能追加、改修、整理の依頼を受けたとき
- 作業内容を `.steering/` に残しながら進めたいとき
- 実装の進捗を tasklist に追記しながら進める必要があるとき

## Goal

- `.steering/[YYYYMMDD]-[作業名]/` に依頼内容と計画を残す
- `requirements.md` にユーザー指示の要旨と受け入れ条件を書く
- `design.md` に設計方針とテスト方針を書く
- `tasklist.md` に実装順序と進捗を残す
- tasklist の全タスク完了まで作業を継続する

## Workflow

1. 今日の日付を確認し、`.steering/[YYYYMMDD]-[作業名]/` を作る。
2. `docs/continuous/` の永続ドキュメントと `docs/project/` の関連ガイドを読む。
3. ユーザーの依頼内容を `requirements.md` の冒頭に明記する。
4. `design.md` に設計、テスト駆動方針、実装ファイル 1 つに対してテスト 1 つの原則を書く。
5. `tasklist.md` を実装可能なタスクだけで具体化する。
6. 実装中は tasklist を正式記録として逐次更新する。
7. 全タスク完了後に振り返りを追記する。

## Rules

- `.steering/` には必ず「今回の依頼内容」を残す。
- tasklist ではテスト先行を基本にする。
- 実装ファイル 1 つに対して、原則として対応するテストファイル 1 つを作る。
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

