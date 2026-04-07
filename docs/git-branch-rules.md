# Git ブランチ運用ルール

## ブランチの種類と役割

| ブランチ名 | 役割 | 派生元 | マージ先 |
|---|---|---|---|
| `main` | 本番公開中のコード | — | — |
| `develop` | 開発の起点となるブランチ | `main` | `main` |
| `release` | リリース前の最終確認用 | `develop` | `develop`, `main` |
| `feature-***` | 新機能の開発 | `develop` | `develop` |
| `hotfix-***` | 本番中のバグ修正 | `main` | `develop`, `main` |

---

## 各ブランチの説明

### `main`
本番環境に公開しているコードを管理するブランチ。
直接コミットは行わず、`release` または `hotfix-***` からのマージのみ受け付ける。

### `develop`
開発の中心となるブランチ。`feature-***` ブランチの派生元・マージ先になる。
`main` と開発作業の間の「バッファ」として機能する。

### `release`
リリース直前の最終確認・調整を行うブランチ。
確認が完了したら `main` と `develop` の両方にマージする。

### `feature-***`
新機能を開発するブランチ。`develop` から派生させ、完成したら `develop` にマージする。

**命名例**:
```
feature/36        # タスク番号が 36 の機能
feature/login     # ログイン機能
```

### `hotfix-***`
本番（`main`）で発生したバグを緊急修正するブランチ。
修正完了後は `main` と `develop` の両方にマージする。

**命名例**:
```
hotfix/42         # タスク番号が 42 のバグ修正
hotfix/fix-login  # ログイン不具合の修正
```

---

## ブランチの流れ図

```
main ──────────────────────────────────────────────► 本番
  │                          ▲            ▲
  │（派生）            （マージ）    （マージ）
  ▼                          │            │
develop ──────────────────── release ─────┘
  │  ▲        │  ▲
  │  │        │  │
  ▼  │        ▼  │
feature-A  feature-B   ...

main ──────────────────────────────────────────────►
  │                    ▲
  │（派生）       （マージ）
  ▼                    │
hotfix-*** ────────────┘
  │
  └──►（develop にもマージ）
```

---

## 命名規則まとめ

```
main
develop
release
feature/<タスク番号またはキーワード>
hotfix/<タスク番号またはキーワード>
```

**注意**: 命名規則はプロジェクトや職場によって異なります。よくある例としては以下があります。

- `develop` の代わりに `development`
- `feature-` の代わりに `issue-`

このリポジトリで採用するルールを事前にチームで確認・合意してから運用してください。

---

## 実務での進め方（Git + PR の一連の流れ）

### 全体像

```
main を最新化
  → 作業ブランチ作成
  → 実装
  → commit
  → push
  → PR 作成
  → レビュー対応
  → main へマージ
  → ローカル main 更新
  → 作業ブランチ削除
```

---

### 1. まず main を最新にする

作業を始める前に、基準になる `main` を最新化します。

```bash
git switch main
git pull origin main
```

**なぜやるか**: 古い `main` からブランチを切ると、あとで差分が汚くなりやすいためです。

---

### 2. 作業用ブランチを切る

やることごとにブランチを作ります。

```bash
git switch -c feature/login-page
```

**命名例**:
```
feature/login-page     # 新機能
fix/header-layout      # バグ修正
docs/api-overview      # ドキュメント
refactor/article-hooks # リファクタリング
```

基本は **1ブランチ = 1目的** です。

---

### 3. 作業する

実装・テスト・ドキュメント更新などをこのブランチで進めます。

```bash
# 変更ファイルの確認
git status

# 差分の確認
git diff
```

---

### 4. まとまった単位で commit する

```bash
git add .
git commit -m "feat: add login page"
```

**コミットメッセージの例**:

| プレフィックス | 用途 |
|---|---|
| `feat:` | 新機能の追加 |
| `fix:` | バグ修正 |
| `docs:` | ドキュメントの追加・更新 |
| `refactor:` | 動作を変えないコード整理 |
| `test:` | テストの追加・修正 |

あとから見て意味が分かる粒度でコミットを切ることが大事です。

---

### 5. リモートに push する

```bash
# 初回（upstream を設定しながら push）
git push -u origin feature/login-page

# 2回目以降
git push
```

---

### 6. GitHub で PR を作る

GitHub 上で Pull Request を作成します。

```
base:    main（またはマージ先のブランチ）
compare: feature/login-page（自分の作業ブランチ）
```

**PR の書き方の例**:
```
タイトル: feat: ログインページを追加

## 概要
ログインページの実装を行いました。

## 変更内容
- ログインフォームのコンポーネント追加
- バリデーション（Zod）の設定
- 認証 API との接続

## 確認方法
1. /login にアクセス
2. 正しい認証情報でログインできることを確認
3. 誤った情報でエラーが表示されることを確認
```

---

### 7. レビュー対応

レビューコメントをもとに修正します。

```bash
# 修正して追加コミット
git add .
git commit -m "fix: apply review comments"
git push
```

push するだけで PR に自動的に反映されます。

---

### 8. main へマージ

レビューが通ったら GitHub 上でマージします。
マージ方法はプロジェクトのルールに従ってください。

| マージ方法 | 特徴 |
|---|---|
| Merge commit | コミット履歴がそのまま残る |
| Squash and merge | ブランチのコミットを1つにまとめてマージ |
| Rebase and merge | 履歴を直線的に保ったままマージ |

---

### 9. ローカルを更新してブランチを削除する

マージ完了後はローカルを最新化し、作業ブランチを削除します。

```bash
git switch main
git pull origin main
git branch -d feature/login-page
```

**リモートのブランチも削除する場合**:
```bash
git push origin --delete feature/login-page
```

GitHub の PR マージ画面にある「Delete branch」ボタンを押せばリモートブランチは自動削除されます。

---

## このリポジトリでの運用例

### 新機能の開発

```bash
# 1. develop を最新化
git switch develop
git pull origin develop

# 2. 作業ブランチを切る
git switch -c feature/my-feature

# 3. 実装・コミットを繰り返す
git add .
git commit -m "feat: add my feature"

# 4. リモートに push
git push -u origin feature/my-feature
```

↓ **GitHub で PR を作成**
```
base:    develop
compare: feature/my-feature
```

↓ **レビュー → マージ（GitHub 上で実行）**

```bash
# 5. ローカルの develop を更新してブランチ削除
git switch develop
git pull origin develop
git branch -d feature/my-feature
```

---

### リリース準備

```bash
# 1. develop を最新化してリリースブランチを切る
git switch develop
git pull origin develop
git switch -c release

# 2. 最終確認・微調整があればコミット
git add .
git commit -m "chore: prepare release"
git push -u origin release
```

↓ **GitHub で PR を 2 本作成**
```
① base: main    compare: release   （本番反映用）
② base: develop compare: release   （develop への反映用）
```

↓ **両方マージ（GitHub 上で実行）**

```bash
# 3. ローカルを更新してブランチ削除
git switch main && git pull origin main
git switch develop && git pull origin develop
git branch -d release
```

---

### 本番バグの緊急対応（hotfix）

```bash
# 1. main を最新化して hotfix ブランチを切る
git switch main
git pull origin main
git switch -c hotfix/critical-bug

# 2. 修正してコミット
git add .
git commit -m "fix: fix critical bug"
git push -u origin hotfix/critical-bug
```

↓ **GitHub で PR を 2 本作成**
```
① base: main    compare: hotfix/critical-bug   （本番反映用）
② base: develop compare: hotfix/critical-bug   （develop への反映用）
```

↓ **両方マージ（GitHub 上で実行）**

```bash
# 3. ローカルを更新してブランチ削除
git switch main && git pull origin main
git switch develop && git pull origin develop
git branch -d hotfix/critical-bug
```
