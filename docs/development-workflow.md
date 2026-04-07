# 開発ワークフロー

このプロジェクトでの開発の進め方を、Claude のエージェント・コマンド・スキルと合わせて説明します。

---

## 全体の流れ

```
フェーズ1: プロジェクト立ち上げ（初回のみ）
  └── /setup-project
        ├── PRD作成（prd-writing スキル）
        ├── 機能設計書（functional-design スキル）
        ├── アーキテクチャ設計書（architecture-design スキル）
        ├── リポジトリ構造定義（repository-structure スキル）
        ├── 開発ガイドライン（development-guidelines スキル）
        └── 用語集（glossary-creation スキル）

フェーズ2: 機能追加（繰り返す）
  └── /add-feature [機能名]
        ├── ステアリングファイル作成（steering スキル）
        ├── 実装ループ（steering スキル × development-guidelines スキル）
        ├── 実装検証（implementation-validator エージェント）
        ├── テスト・lint・型チェック
        └── 振り返り記録（steering スキル）

フェーズ3: レビュー（任意）
  └── /review-docs [ファイルパス]
        └── ドキュメント品質レビュー（doc-reviewer エージェント）
```

---

## フェーズ1: プロジェクト立ち上げ

### コマンド: `/setup-project`

プロジェクト開始時に一度だけ実行します。
6つの永続ドキュメントを順番に作成します。

#### 準備

アイデアや要件のメモを事前に書いておくと、Claude が内容を引き継いでくれます。

```
docs/ideas/initial-requirements.md ← ここにアイデアを書いておく
```

#### 作成されるドキュメントと対応するスキル

| 順番 | ドキュメント | スキル | 内容 |
|---|---|---|---|
| 1 | `docs/product-requirements.md` | `prd-writing` | 誰のために何を作るか。KPI・ユーザーストーリー・優先度（P0/P1/P2） |
| 2 | `docs/functional-design.md` | `functional-design` | 機能の詳細設計。データモデル・ユースケース・API設計 |
| 3 | `docs/architecture.md` | `architecture-design` | 技術スタック・レイヤー構造・パフォーマンス・セキュリティ要件 |
| 4 | `docs/repository-structure.md` | `repository-structure` | ディレクトリ構成・命名規則・依存ルール |
| 5 | `docs/development-guidelines.md` | `development-guidelines` | コーディング規約・ブランチ戦略・テスト方針・レビュープロセス |
| 6 | `docs/glossary.md` | `glossary-creation` | プロジェクト固有の用語定義 |

#### 流れ

```
1. docs/ideas/initial-requirements.md を読み込む
2. PRD を作成 → ユーザーが承認するまで待機  ← ここだけ人間が確認
3. PRD を元に、残り5つを自動作成
```

> **ポイント**: PRD の承認後は自動で進みます。
> 最終的な判断は必ず人間が行ってください（Claude のレビューを鵜呑みにしない）。

---

## フェーズ2: 機能追加

### コマンド: `/add-feature [機能名]`

機能を追加するたびに実行します。開始から完了まで自動で進みます。

**使い方の例**:
```
/add-feature ユーザープロフィール編集
/add-feature 記事一覧ページ
/add-feature コメント機能
```

#### 全ステップ

```
Step 1: 準備
  → .steering/[日付]-[機能名]/ ディレクトリを作成
  → requirements.md, design.md, tasklist.md（空ファイル）を作成

Step 2: プロジェクト理解
  → CLAUDE.md・docs/ を読み込んでプロジェクトの方針を把握

Step 3: 既存パターンの調査
  → src/ を grep して命名規則・実装パターンを確認

Step 4: 計画（ステアリングファイル生成）← steering スキル
  → requirements.md: 今回の機能要件
  → design.md: 設計方針・データ構造・コンポーネント設計
  → tasklist.md: 実装タスクをフェーズ別にリスト化

Step 5: 実装ループ ← steering スキル + development-guidelines スキル
  → tasklist.md の [ ] タスクをひとつずつ実装・完了させる
  → 完了したら [x] に更新（リアルタイムに記録）
  → 全タスクが [x] になるまで繰り返す

Step 6: 実装検証 ← implementation-validator エージェント
  → スペック準拠・コード品質・テストカバレッジ・セキュリティ・パフォーマンスを検証

Step 7: 自動テスト
  → npm test（Vitest）
  → npm run lint（ESLint）
  → npm run typecheck（TypeScript）
  → エラーがあれば修正して再実行

Step 8: 振り返り ← steering スキル（振り返りモード）
  → tasklist.md に記録（完了日・計画との差分・学び・改善提案）
  → docs/ の永続ドキュメントへの影響があれば更新
```

#### ステアリングファイルとは？

`.steering/` はClaude が1回の作業につき作る「作業ログ」です。

```
.steering/
└── 20260405-記事一覧ページ/
    ├── requirements.md  ← この機能で何を作るか
    ├── design.md        ← どう作るか（設計）
    └── tasklist.md      ← タスクの進捗（リアルタイム更新）
```

tasklist.md はリアルタイムに更新されるので、途中で作業が止まっても再開できます。

#### 実装中のルール（tasklist.md）

```
✅ 良い完了の形
- [x] 型定義を追加
- [x] API 層を実装
- [x] コンポーネントを作成

❌ やってはいけないこと
- [ ] テストを書く（時間の都合により後回し）← 絶対禁止
```

技術的な理由でスキップが必要な場合のみ、理由を明記してスキップできます：
```
- [x] ~~キャッシュ層を追加~~ （アーキテクチャ変更により不要: TanStack Query が同等の役割を担うため）
```

---

## フェーズ3: ドキュメントレビュー

### コマンド: `/review-docs [ファイルパス]`

ドキュメントの品質を確認したいときに実行します。

**使い方の例**:
```
/review-docs docs/product-requirements.md
/review-docs docs/functional-design.md
```

#### 評価される観点（doc-reviewer エージェント）

| 観点 | 内容 |
|---|---|
| 完全性 | 必要なセクションが揃っているか |
| 明確性 | 誰が読んでも理解できるか |
| 一貫性 | 他のドキュメントと矛盾がないか |
| 実装可能性 | 開発者が実装に必要な情報が揃っているか |
| 測定可能性 | 成功基準が数値で確認できるか |

---

## エージェント・コマンド・スキルの役割まとめ

### コマンド（`/` で呼ぶ）

| コマンド | いつ使う |
|---|---|
| `/setup-project` | プロジェクト開始時に1回 |
| `/add-feature [機能名]` | 機能を追加するたびに |
| `/review-docs [パス]` | ドキュメントをレビューしたいとき |

### エージェント（コマンドから自動起動）

| エージェント | 役割 | 起動タイミング |
|---|---|---|
| `doc-reviewer` | ドキュメントの品質レビュー | `/review-docs` 実行時 |
| `implementation-validator` | 実装コードの品質検証 | `/add-feature` の Step 6 |

### スキル（コマンド・エージェントの内部で使用）

| スキル | 役割 | 使用タイミング |
|---|---|---|
| `prd-writing` | PRD 作成 | `/setup-project` Step 1 |
| `functional-design` | 機能設計書作成 | `/setup-project` Step 2 |
| `architecture-design` | アーキテクチャ設計書作成 | `/setup-project` Step 3 |
| `repository-structure` | リポジトリ構造定義書作成 | `/setup-project` Step 4 |
| `development-guidelines` | 開発ガイドライン作成・参照 | `/setup-project` Step 5 / 実装時 |
| `glossary-creation` | 用語集作成 | `/setup-project` Step 6 |
| `steering` | 作業計画・タスク管理・振り返り | `/add-feature` 全体 |

---

## Git との連携

機能追加の流れは `docs/git-branch-rules.md` の「新機能の開発」パターンに従います。

```bash
# 1. develop を最新化してブランチを切る
git switch develop
git pull origin develop
git switch -c feature/my-feature

# 2. /add-feature を実行して実装
/add-feature [機能名]

# 3. コミット・プッシュ
git add .
git commit -m "feat: add my feature"
git push -u origin feature/my-feature

# 4. GitHub で PR を作成（base: develop）
# 5. レビュー → マージ
# 6. ローカルを更新してブランチ削除
git switch develop && git pull origin develop
git branch -d feature/my-feature
```

---

## よくある質問

**Q: `/add-feature` の途中で作業が止まったら？**

`.steering/[日付]-[機能名]/tasklist.md` を確認してください。
`[ ]` が残っているタスクから再開できます。

**Q: 永続ドキュメント（`docs/`）はいつ更新する？**

`/add-feature` の Step 8（振り返り）で、変更がアーキテクチャや設計に影響する場合に自動更新されます。

**Q: スキルを単体で使いたい場合は？**

Claude に直接「`prd-writing` スキルを使って PRD を更新して」のように依頼してください。
