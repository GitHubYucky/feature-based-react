# Bulletproof React — リポジトリ概要 (日本語)

## プロジェクト概要

**Bulletproof React** は、スケーラブルな React アプリケーションを構築するための実践的なアーキテクチャガイドです。
テンプレートやボイラープレートではなく、実際のプロダクション環境で通用するベストプラクティスのリファレンス実装として位置づけられています。

- **ライセンス**: MIT
- **著者**: alan2207
- **目的**: チーム開発・エンタープライズ向けの React アーキテクチャパターンの提示

---

## モノレポ構成

```
bulletproof-react/
├── apps/
│   ├── react-vite/       # React + Vite（基本構成）
│   ├── nextjs-app/       # Next.js App Router
│   └── nextjs-pages/     # Next.js Pages Router
├── docs/                 # ドキュメント群
├── .devcontainer/        # 開発コンテナ設定
└── .github/workflows/    # CI/CD パイプライン
```

3 つのサンプルアプリはすべて同一のアーキテクチャパターンに従っており、フレームワークが変わっても設計思想を維持できることを示しています。

---

## 技術スタック

| カテゴリ | ライブラリ |
|---|---|
| UI フレームワーク | React 18 |
| ビルドツール | Vite 5 / Next.js 14 |
| ルーティング | React Router 7 |
| サーバーキャッシュ | TanStack Query 5 |
| クライアント状態 | Zustand 4 |
| フォーム | React Hook Form 7 + Zod |
| API クライアント | Axios |
| スタイリング | Tailwind CSS 3 + Radix UI |
| テスト | Vitest + Testing Library + Playwright |
| API モック | MSW 2 |
| コンポーネント開発 | Storybook 8 |
| コード品質 | ESLint + Prettier + TypeScript (strict) |
| Git フック | Husky 9 |
| コード生成 | Plop |

---

## ソースコード構造

各アプリケーションは以下の構成に従っています。

```
src/
├── app/              # アプリケーション層・ルーティング
│   ├── routes/       # ルート定義（遅延ロード）
│   ├── provider.tsx  # グローバルプロバイダー
│   └── router.tsx    # ルーター設定
├── components/       # 共有 UI コンポーネント
│   ├── ui/           # 汎用コンポーネント（Button, Form, Dialog 等）
│   ├── layouts/      # レイアウトコンポーネント
│   └── errors/       # エラー境界・エラー表示
├── features/         # 機能単位モジュール（ドメイン駆動）
│   ├── auth/         # 認証
│   ├── comments/     # コメント
│   ├── discussions/  # ディスカッション
│   ├── teams/        # チーム
│   └── users/        # ユーザー
├── hooks/            # 共有カスタムフック
├── lib/              # ライブラリ設定
│   ├── api-client.ts # Axios インスタンス
│   ├── auth.tsx      # 認証設定
│   └── authorization.tsx  # RBAC/PBAC 設定
├── config/           # グローバル設定・環境変数
├── types/            # 共有 TypeScript 型
└── testing/          # テストユーティリティ・MSW モック
```

### フィーチャーモジュールの構成

```
features/awesome-feature/
├── api/          # API リクエスト定義
├── components/   # フィーチャー固有コンポーネント
├── hooks/        # フィーチャー固有フック
├── stores/       # 状態管理
├── types/        # 型定義
└── utils/        # ユーティリティ
```

---

## アーキテクチャの重要な設計原則

### 単方向依存（Unidirectional Architecture）

```
shared/ → features/ → app/
```

- `shared` (components, hooks, utils) はどこからでも利用可能
- `features` は `shared` のみをインポート可能（他フィーチャーをインポート不可）
- `app` 層は `features` と `shared` をインポート可能

クロスフィーチャーのインポートは **ESLint ルールで強制禁止**されています。

### 状態管理の分類

| 種別 | 手段 |
|---|---|
| コンポーネントローカル状態 | `useState` / `useReducer` |
| アプリケーション状態 | Context + フック / Zustand |
| サーバーキャッシュ状態 | TanStack Query |
| フォーム状態 | React Hook Form + Zod |
| URL 状態 | React Router (params / query) |

---

## サンプルアプリの機能

チームコラボレーションプラットフォームとして実装されています。

**データモデル**:
- **Users** — ADMIN または USER ロール
- **Teams** — ユーザーの集合
- **Discussions** — チームメンバーが作成するトピック
- **Comments** — ディスカッション内のメッセージ

**実装済み機能**:
1. **認証** — ログイン・新規登録・JWT 管理・保護ルート
2. **ディスカッション** — 作成・編集・削除・一覧・閲覧
3. **コメント** — 作成・削除・一覧
4. **チーム** — 一覧・参加・管理
5. **ユーザー** — 一覧・削除（管理者）・プロフィール更新

---

## セキュリティ

- JWT 認証（HttpOnly Cookie を想定）
- ロールベースアクセス制御（RBAC）
- 権限ベースアクセス制御（PBAC）
- DOMPurify による XSS 対策（入力サニタイズ）
- 保護ルートコンポーネントによるアクセス制限

---

## テスト戦略

3 層のテストアプローチを採用しています。

| 層 | ツール | 目的 |
|---|---|---|
| ユニットテスト | Vitest + Testing Library | 個別コンポーネント・関数の検証 |
| 統合テスト | Vitest + Testing Library | フィーチャー全体のワークフロー検証（主軸） |
| E2E テスト | Playwright | ブラウザ上での完全なフロー検証 |

MSW によりネットワークレベルで API をモックし、実際の HTTP リクエストフローに近い形でテストを実現しています。

---

## CI/CD

GitHub Actions により各アプリケーションごとに以下を自動実行します。

- ESLint・TypeScript チェック
- Vitest ユニット/統合テスト
- Playwright E2E テスト
- ビルド検証

---

## ドキュメント一覧

| ファイル | 内容 |
|---|---|
| `project/application-overview.md` | データモデル・ユーザーロール・チーム構造 |
| `project/project-standards.md` | ESLint・Prettier・TypeScript・命名規則 |
| `project/project-structure.md` | フォルダ構成・フィーチャー分離・依存方向 |
| `project/state-management.md` | 状態管理の種類と使い分け |
| `project/api-layer.md` | API クライアント・リクエスト宣言・型安全性 |
| `project/components-and-styling.md` | コンポーネント設計・Storybook・スタイリング |
| `project/testing.md` | テスト手法・Vitest・Playwright・MSW |
| `project/error-handling.md` | API エラー・エラー境界・Sentry 連携 |
| `project/security.md` | 認証・認可・RBAC・PBAC・XSS 対策 |
| `project/performance.md` | コード分割・コンポーネント最適化 |
| `project/deployment.md` | Vercel・Netlify・AWS・CloudFlare へのデプロイ |
| `project/additional-resources.md` | 学習リソース集 |

---

## セットアップ

```bash
git clone https://github.com/alan2207/bulletproof-react.git
cd bulletproof-react
yarn prepare        # 全アプリの依存パッケージをインストール
cd apps/react-vite  # または nextjs-app / nextjs-pages
cp .env.example .env
yarn dev
```

**必要環境**: Node.js 20+、Yarn 1.22+
