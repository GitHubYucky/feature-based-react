---
name: steering
description: ユーザーから AI に出されたあらゆる作業指示について、依頼内容、計画、進捗、振り返りを `.steering/` に記録しながら進めるためのスキル。実装、調査、文書化、レビュー、整理を含む全作業で使う。
---

# Steering

## Use this skill when

- ユーザーから AI への作業指示を受けたとき
- 実装、調査、文書化、レビュー、整理など、作業内容を記録しながら進めたいとき
- 作業内容を `.steering/` に残しながら進めたいとき
- 進捗や判断の履歴を後から追えるようにしたいとき

## Goal

- `.steering/[YYYYMMDD]-[作業名]/` に要求整理と進行記録を残す
- `requirements.md` にユーザー要求の整理と前提条件を書く
- `design.md` に設計判断や方針を書く
- `tasklist.md` に作業タスクと進捗を残す
- tasklist の更新を通じて作業の現在地を明確にする

## Workflow

1. 当日の日付を確認し、`.steering/[YYYYMMDD]-[作業名]/` を作る。
2. `docs/continuous/` の既存ドキュメントと `docs/project/` の関連ガイドを読む。
3. ユーザーの要求整理を `requirements.md` に書く。
4. `design.md` に実装方針を書く。設計不要な軽作業でも、必要最小限の方針や変更対象ファイルを 1 つにまとめて残す。
5. `tasklist.md` を作り、その時点の作業に必要なタスクだけを簡潔に列挙する。
6. 作業中は tasklist を進捗記録として更新する。
7. 全タスク完了後に振り返りや結果を必要に応じて追記する。

## Rules

- `.steering/` は AI との全作業指示で運用する。
- `.steering/` には必ず、今回の要求整理、設計、進捗を残す。
- `.steering/` 配下に作成・更新するファイルの内容は日本語で記述する。
- 実装作業では tasklist による進捗管理を基本にする。
- 軽作業でも、変更対象ファイルや意図が追える最小限の記録は残す。
- tasklist を更新せずに次のタスクへ進まない。
- 「あとで書く」前提で未記録のまま進めない。

## Read next when needed

- `templates/requirements.md`
- `templates/design.md`
- `templates/tasklist.md`

## Output

- 作成・更新した `.steering` ディレクトリ
- 更新した tasklist の進捗
- 必要に応じた振り返りメモ
