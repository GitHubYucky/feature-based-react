---
name: brainstorm
description: アイデアの壁打ちをして、`docs/ideas/initial-requirements.md` に書ける初期要件へ整理するためのスキル。要件がまだ曖昧なとき、作りたいものの方向性を一緒に詰めたいとき、最初の要求整理をしたいときに使う。
---

# Brainstorm

## Use this skill when

- ユーザーが作りたいものをまだ言語化し切れていないとき
- `docs/ideas/initial-requirements.md` を作る前に壁打ちしたいとき
- 機能、対象ユーザー、優先順位、制約を整理したいとき

## Goal

- 曖昧なアイデアを、実装や文書化に使える初期要件へ変換する
- ユーザーの考えを短い往復で整理する
- 最後に `docs/ideas/initial-requirements.md` に書ける形へ要約する

## Workflow

1. まずユーザーの作りたいものを 1-2 文で言い換える。
2. 足りない観点だけを順番に壁打ちする。
3. 特に次を明確にする。
   - 誰のためのものか
   - 最初に必要な機能は何か
   - 後回しでよい機能は何か
   - 制約や避けたいことは何か
4. 会話を広げすぎず、`initial-requirements.md` に入る粒度まで絞る。
5. 最後に Markdown でそのまま転記できる草案を返す。

## Rules

- 最初から設計や実装詳細に入りすぎない。
- 1 回の返答で論点を増やしすぎない。
- 壁打ちの目的は「初期要件の確定」であり、完全設計ではない。
- 不明点は列挙だけで終えず、優先度の高いものから詰める。
- まとまったら `docs/ideas/initial-requirements.md` の草案として返す。

## Read next when needed

- `references/question-areas.md`

## Output

- 壁打ちで整理した要点
- 未確定事項
- `docs/ideas/initial-requirements.md` に貼れる Markdown 草案

