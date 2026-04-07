# 開発ワークフロー

このプロジェクトでの開発の進め方を、Codex でそのまま運用できる形で説明します。

## Codex への指示の出し方

`development-workflow` は単独のコマンド名ではなく、このドキュメントに書かれた進め方全体を指します。
Codex には長い指示ではなく、`使ってほしい skill` と `対象` を短く渡せば十分です。

### 基本形

```text
[skill名] で [対象] を進めて。
```

### 情報が足りない場合

必要な情報が足りなければ、Codex は作業を止めずに対話で確認しながら進めます。

### 最低限あるとよい情報

- 対象
- 使ってほしい phase または skill
- 避けたい変更

---

## 全体の流れ

```
フェーズ1: プロジェクト立ち上げ（初回のみ）
  └── repo-setup / setup-project
        ├── ステアリング作成（steering スキル）
        ├── PRD作成（prd-writing スキル）
        ├── 機能設計書（functional-design スキル）
        ├── アーキテクチャ設計書（architecture-design スキル）
        ├── リポジトリ構造定義（repository-structure スキル）
        ├── 開発ガイドライン（development-guidelines スキル）
        ├── 用語集（glossary-creation スキル）
        └── 振り返り記録（steering スキル）

フェーズ2: 機能追加（繰り返す）
  └── feature-add [機能名]
        ├── ステアリングファイル作成（steering スキル）
        ├── 実装ループ（steering スキル × development-guidelines スキル）
        ├── 実装検証（implementation-validation スキル）
        ├── テスト・lint・型チェック
        └── 振り返り記録（steering スキル）

フェーズ3: レビュー（任意）
  └── doc-review [ファイルパス]
        ├── レビュー方針の整理（steering スキル）
        ├── ドキュメント品質レビュー（doc-review スキル）
        └── 指摘整理・次アクション化（steering スキル）
```

---

## フェーズ1: プロジェクト立ち上げ

### Skill: `repo-setup` / `setup-project`

プロジェクト開始時に一度だけ実行します。
6つの永続ドキュメントを順番に作成します。

#### 準備

アイデアや要件のメモを事前に書いておくと、Codex が内容を引き継ぎやすくなります。

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
1. steering で対象と進め方を整理する
2. docs/ideas/initial-requirements.md を読み込む
3. PRD を作成 → ユーザーが承認するまで待機  ← ここだけ人間が確認
4. PRD を元に、残り5つを作成する
5. steering で振り返りと更新方針を記録する
```

> **ポイント**: PRD のドラフト確認後に残りへ進む運用にすると安全です。
> 最終的な判断は必ず人間が行ってください。

### Codex にはこう依頼する

```text
repo-setup で初期 docs を整備して。
```

```text
setup-project で立ち上げを進めて。
```

#### 追加であるとよい情報

- 要件メモの場所
- 既存 docs を更新するか新規作成か
- PRD で一度確認を挟むか
- steering を残す場所

---

## フェーズ2: 機能追加

### Skill: `feature-add [機能名]`

機能を追加するたびに実行します。開始から完了まで自動で進みます。

**依頼例**:
```
feature-add でユーザープロフィール編集を追加して。
feature-add で記事一覧ページを実装して。
feature-add でコメント機能を進めて。
```

#### 全ステップ

```
Step 1: 準備
  → .steering/[日付]-[機能名]/ ディレクトリを作成
  → requirements.md, design.md, tasklist.md（空ファイル）を作成

Step 2: プロジェクト理解
  → AGENTS.md・docs/ を読み込んでプロジェクトの方針を把握

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

Step 6: 実装検証 ← implementation-validation スキル
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

`.steering/` は Codex が1回の作業につき作る「作業ログ」です。

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

### Codex にはこう依頼する

```text
steering と feature-add でユーザープロフィール編集を追加して。
```

```text
feature-add でユーザープロフィール編集を追加して。
```

```text
feature-add で記事一覧ページを実装して。大きなリファクタはしないで。
```

```text
feature-add でコメント機能を進めて。必要なら docs も更新して。
```

#### 追加であるとよい情報

- 期待する挙動
- 関連 issue や参照 docs
- 触ってよい範囲
- 必須の検証内容

---

## フェーズ3: ドキュメントレビュー

### Skill: `doc-review [ファイルパス]`

ドキュメントの品質を確認したいときに実行します。

**依頼例**:
```
doc-review で docs/product-requirements.md を見て。
doc-review で docs/functional-design.md をレビューして。
```

#### 評価される観点（doc-review スキル）

| 観点 | 内容 |
|---|---|
| 完全性 | 必要なセクションが揃っているか |
| 明確性 | 誰が読んでも理解できるか |
| 一貫性 | 他のドキュメントと矛盾がないか |
| 実装可能性 | 開発者が実装に必要な情報が揃っているか |
| 測定可能性 | 成功基準が数値で確認できるか |

### Codex にはこう依頼する

```text
steering と doc-review で docs/product-requirements.md を見て。
```

```text
doc-review で docs/product-requirements.md を見て。
```

```text
doc-review で docs/functional-design.md をレビューして。修正案までほしい。
```

#### 追加であるとよい情報

- 特に見てほしい観点
- 指摘だけでよいか
- 比較対象の docs や実装ファイル

---

## フェーズとスキルの対応

### フェーズごとの入口

| 入口 | いつ使う |
|---|---|
| `repo-setup` / `setup-project` | プロジェクト開始時に1回 |
| `feature-add [機能名]` | 機能を追加するたびに |
| `doc-review [パス]` | ドキュメントをレビューしたいとき |

### 補助スキル

| スキル | 役割 | 使用タイミング |
|---|---|---|
| `prd-writing` | PRD 作成 | `repo-setup` / `setup-project` の序盤 |
| `functional-design` | 機能設計書作成 | 立ち上げ時 |
| `architecture-design` | アーキテクチャ設計書作成 | 立ち上げ時 |
| `repository-structure` | リポジトリ構造定義書作成 | 立ち上げ時 |
| `development-guidelines` | 開発ガイドライン作成・参照 | 立ち上げ時 / 実装時 |
| `glossary-creation` | 用語集作成 | 立ち上げ時 |
| `steering` | 作業計画・タスク管理・振り返り | 全フェーズ |
| `implementation-validation` | 実装コードの品質検証 | `feature-add` の検証時 |
| `doc-review` | ドキュメントの品質レビュー | レビュー時 |

---

## Git との連携

機能追加の流れは `docs/git-branch-rules.md` の「新機能の開発」パターンに従います。

```bash
# 1. develop を最新化してブランチを切る
git switch develop
git pull origin develop
git switch -c feature/my-feature

# 2. Codex に機能追加を依頼
feature-add で [機能名] を追加して。

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

**Q: `feature-add` の途中で作業が止まったら？**

`.steering/[日付]-[機能名]/tasklist.md` を確認してください。
`[ ]` が残っているタスクから再開できます。

**Q: 永続ドキュメント（`docs/`）はいつ更新する？**

`feature-add` の振り返りで、変更がアーキテクチャや設計に影響する場合に更新します。

**Q: スキルを単体で使いたい場合は？**

Codex に直接「`prd-writing` で PRD を更新して」のように依頼してください。
