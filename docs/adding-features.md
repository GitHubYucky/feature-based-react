# 機能追加の流れ（TDD アプローチ）

新しい機能を追加する際の標準的な手順をまとめます。
`discussions` フィーチャーを参考例として、**テストを先に書きながら実装を進める**流れで説明します。

---

## はじめに：なぜこんなに手順が多いの？

「React なら `useState` と `fetch` だけでも動くのに…」と思うかもしれません。

小さなアプリならそれで十分です。でも複数人で開発したり、機能が増えてくると、こんな問題が起きます。

- API の呼び出しがコンポーネントの中に散らばって、どこで何をしているか分からなくなる
- ある画面のバグを直したら別の画面が壊れた
- 新しい人がジョインしたとき「どこに何を書けばいいか」が分からない

このアーキテクチャは「**決めごとを増やすことで、考えることを減らす**」設計です。
手順が多く見えますが、毎回同じパターンで書けるので、慣れると逆に速くなります。

---

## なぜ TDD（テスト駆動開発）なの？

TDD とは「実装する前にテストを書く」開発スタイルです。

```
① テストを書く（まだ実装がないので当然失敗する = RED）
② 実装する
③ テストが通る = GREEN
④ コードをきれいにする = REFACTOR
```

このプロジェクトでの TDD のメリットは特に大きいです。

- **MSW（モックサーバー）が使える** → 本物のバックエンドがなくても、フロントの実装を先に進められる
- **統合テストが主役** → 「ユーザーがボタンを押したら一覧に追加される」という動作全体をテストする
- **実装の迷いが減る** → テストを先に書くことで「何を作ればいいか」が明確になる

---

## 全体の流れ

新機能（例: 記事 = `articles`）を追加するとき、触るファイルは決まっています。

```
1. 型定義             src/types/api.ts                  ← データの「形」を定義する
2. MSW ハンドラー     src/testing/mocks/handlers/        ← モック API を用意する
3. DB モデル          src/testing/mocks/db.ts            ← テスト用のインメモリ DB に追加
4. データジェネレーター src/testing/data-generators.ts   ← テスト用のダミーデータ生成関数
5. テスト（先に書く）  src/app/routes/app/articles/__tests__/ ← RED の状態でOK
6. API 層             src/features/articles/api/         ← サーバーとのやり取りを書く
7. コンポーネント      src/features/articles/components/  ← UI を書く
8. ルートコンポーネント src/app/routes/app/articles/      ← ページを作る
9. パス定義           src/config/paths.ts                ← URL を登録する
10. ルーター登録       src/app/router.tsx                 ← URL とページを紐づける
11. ESLint 設定       .eslintrc.cjs                      ← ルール違反を自動検知できるようにする
```

---

## Step 1: 型定義

### なにをするの？

「このデータはどんなフィールドを持っているか」を TypeScript の型として定義します。
バックエンドの API が返すデータの形をここで明示することで、コード全体で型の恩恵を受けられます。

### どこに書く？

`src/types/api.ts`

```ts
// src/types/api.ts
export type Article = Entity<{
  title: string;
  body: string;
  teamId: string;
  author: User;
}>;
```

`Entity<T>` は `id` と `createdAt` を自動で付けてくれるラッパー型なので、独自フィールドだけ書けばOKです。

---

## Step 2: MSW ハンドラーを用意する

### なにをするの？

テスト（および開発中）に使う **モック API サーバー** を定義します。
実際のバックエンドがなくても、HTTP リクエストを横取りして偽のレスポンスを返してくれます。

### MSW って何？

> MSW（Mock Service Worker）は、ネットワーク層でリクエストをインターセプトするライブラリです。
> `fetch` や `axios` のコードを一切変えずに、テスト環境・開発環境でモックレスポンスを返せます。

### どこに書く？

`src/testing/mocks/handlers/articles.ts`（新規作成）

```ts
// src/testing/mocks/handlers/articles.ts
import { HttpResponse, http } from 'msw';
import { env } from '@/config/env';
import { db, persistDb } from '../db';
import { requireAuth, requireAdmin, networkDelay } from '../utils';

type ArticleBody = {
  title: string;
  body: string;
};

export const articlesHandlers = [
  // GET /articles → 一覧取得
  http.get(`${env.API_URL}/articles`, async ({ cookies, request }) => {
    await networkDelay();
    try {
      const { user, error } = requireAuth(cookies);
      if (error) return HttpResponse.json({ message: error }, { status: 401 });

      const url = new URL(request.url);
      const page = Number(url.searchParams.get('page') || 1);
      const total = db.article.count({ where: { teamId: { equals: user?.teamId } } });

      const result = db.article.findMany({
        where: { teamId: { equals: user?.teamId } },
        take: 10,
        skip: 10 * (page - 1),
      });

      return HttpResponse.json({ data: result, meta: { page, total, totalPages: Math.ceil(total / 10) } });
    } catch (error: any) {
      return HttpResponse.json({ message: error?.message || 'Server Error' }, { status: 500 });
    }
  }),

  // POST /articles → 作成
  http.post(`${env.API_URL}/articles`, async ({ request, cookies }) => {
    await networkDelay();
    try {
      const { user, error } = requireAuth(cookies);
      if (error) return HttpResponse.json({ message: error }, { status: 401 });

      const data = (await request.json()) as ArticleBody;
      requireAdmin(user);
      const result = db.article.create({ teamId: user?.teamId, authorId: user?.id, ...data });
      await persistDb('article');
      return HttpResponse.json(result);
    } catch (error: any) {
      return HttpResponse.json({ message: error?.message || 'Server Error' }, { status: 500 });
    }
  }),

  // DELETE /articles/:articleId → 削除
  http.delete(`${env.API_URL}/articles/:articleId`, async ({ cookies, params }) => {
    await networkDelay();
    try {
      const { user, error } = requireAuth(cookies);
      if (error) return HttpResponse.json({ message: error }, { status: 401 });

      requireAdmin(user);
      const result = db.article.delete({ where: { id: { equals: params.articleId as string } } });
      await persistDb('article');
      return HttpResponse.json(result);
    } catch (error: any) {
      return HttpResponse.json({ message: error?.message || 'Server Error' }, { status: 500 });
    }
  }),
];
```

作成したら `src/testing/mocks/handlers/index.ts` にインポートを追加します。

```ts
// src/testing/mocks/handlers/index.ts
import { articlesHandlers } from './articles';
// ... 既存のハンドラー

export const handlers = [
  // ... 既存のハンドラー
  ...articlesHandlers,
];
```

---

## Step 3: DB モデルに追加する

### なにをするの？

MSW ハンドラーが使うインメモリ DB（`@mswjs/data`）に新しいモデルを追加します。
これが「テスト中の疑似データベース」になります。

### どこに書く？

`src/testing/mocks/db.ts`

```ts
// src/testing/mocks/db.ts
const models = {
  // ... 既存のモデル（user, team, discussion, comment）

  // 追加
  article: {
    id: primaryKey(nanoid),
    title: String,
    body: String,
    authorId: String,
    teamId: String,
    createdAt: Date.now,
  },
};
```

---

## Step 4: データジェネレーターを追加する

### なにをするの？

テストで使う**ダミーデータを生成する関数**を用意します。
毎回テストコード内にベタ書きするのではなく、一箇所で管理します。

### どこに書く？

`src/testing/data-generators.ts`

```ts
// src/testing/data-generators.ts（既存ファイルに追記）
import { randCatchPhrase, randParagraph, randUuid } from '@ngneat/falso';

const generateArticle = () => ({
  id: randUuid(),
  title: randCatchPhrase(),   // ランダムなキャッチコピー
  body: randParagraph(),      // ランダムな文章
  createdAt: Date.now(),
});

export const createArticle = <T extends Partial<ReturnType<typeof generateArticle>>>(
  overrides?: T & { authorId?: string; teamId?: string },
) => {
  return { ...generateArticle(), ...overrides };
};
```

**`overrides` って何？**

テストによっては「タイトルだけ固定したい」という場面があります。

```ts
const article = createArticle({ title: '固定タイトル' });
// → title は '固定タイトル'、他はランダム
```

---

## Step 5: テストを先に書く（RED）

### なにをするの？

**実装前にテストを書きます。** この時点でテストを実行すると当然失敗します（RED）。
それで OK です。「何を作れば完成か」の定義になります。

### どこに書く？

`src/app/routes/app/articles/__tests__/articles.test.tsx`（新規作成）

```tsx
// src/app/routes/app/articles/__tests__/articles.test.tsx
import { createArticle, createUser, renderApp, screen, userEvent, waitFor, within } from '@/testing/test-utils';
import { default as ArticlesRoute } from '../articles';

test('記事の作成・表示・削除ができる', { timeout: 10000 }, async () => {
  // 1. ページをレンダリング（ログイン済みユーザーとして）
  await renderApp(<ArticlesRoute />);

  // 2. 最初は何もない
  expect(await screen.findByText(/no entries/i)).toBeInTheDocument();

  // 3. 「Create Article」ボタンをクリック
  await userEvent.click(screen.getByRole('button', { name: /create article/i }));

  // 4. ドロワー（サイドパネル）が開く
  const drawer = await screen.findByRole('dialog', { name: /create article/i });

  // 5. フォームに入力
  const newArticle = createArticle();
  await userEvent.type(within(drawer).getByText(/title/i), newArticle.title);
  await userEvent.type(within(drawer).getByText(/body/i), newArticle.body);

  // 6. 送信
  await userEvent.click(within(drawer).getByRole('button', { name: /submit/i }));

  // 7. ドロワーが閉じる
  await waitFor(() => expect(drawer).not.toBeInTheDocument());

  // 8. 一覧に作成した記事が表示される
  expect(await screen.findByText(newArticle.title)).toBeInTheDocument();

  // 9. 削除ボタンをクリック
  await userEvent.click(screen.getByRole('button', { name: /delete article/i }));

  // 10. 確認ダイアログで削除を確定
  const confirmDialog = await screen.findByRole('dialog', { name: /delete article/i });
  await userEvent.click(within(confirmDialog).getByRole('button', { name: /delete/i }));

  // 11. 削除後は一覧から消えている
  await waitFor(() => {
    expect(screen.queryByText(newArticle.title)).not.toBeInTheDocument();
  });
});
```

### テストの読み方

このテストは「実際のユーザー操作」をそのままコードで表現しています。

```
ページを開く → ボタンをクリック → フォームに入力 → 送信 → 結果を確認
```

`screen.getByRole('button', { name: /create article/i })` のように、**見た目（テキスト・ロール）**でDOM要素を探します。
`id` や `class` 名ではなく、ユーザーが実際に見ているものを基準にするのがポイントです。

### テストを実行して RED を確認する

```bash
cd apps/react-vite
yarn test
# → テストが失敗する（まだ ArticlesRoute が存在しないため）= RED
```

---

## Step 6: API 層を実装する

`src/features/articles/api/` 配下に、操作ごとにファイルを分けて作成します。

### GET（一覧取得）

```ts
// src/features/articles/api/get-articles.ts
import { queryOptions, useQuery } from '@tanstack/react-query';
import { api } from '@/lib/api-client';
import { QueryConfig } from '@/lib/react-query';
import { Article, Meta } from '@/types/api';

// ① サーバーへのリクエスト本体
export const getArticles = (page = 1): Promise<{ data: Article[]; meta: Meta }> => {
  return api.get('/articles', { params: { page } });
};

// ② queryKey と queryFn をセットで定義（ローダーでも再利用できるようにする）
//    queryKey はキャッシュの「名前タグ」。同じキーなら同じキャッシュを使い回す
export const getArticlesQueryOptions = ({ page }: { page?: number } = {}) => {
  return queryOptions({
    queryKey: page ? ['articles', { page }] : ['articles'],
    queryFn: () => getArticles(page),
  });
};

// ③ コンポーネントから呼ぶためのカスタムフック
export const useArticles = ({ page }: { page?: number } = {}) => {
  return useQuery(getArticlesQueryOptions({ page }));
};
```

### POST（作成）

```ts
// src/features/articles/api/create-article.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { z } from 'zod';
import { api } from '@/lib/api-client';
import { MutationConfig } from '@/lib/react-query';
import { Article } from '@/types/api';
import { getArticlesQueryOptions } from './get-articles';

// Zod でバリデーションルールを定義（コンポーネント側のフォームとも共有する）
export const createArticleInputSchema = z.object({
  title: z.string().min(1, 'Required'),
  body: z.string().min(1, 'Required'),
});

export type CreateArticleInput = z.infer<typeof createArticleInputSchema>;

export const createArticle = ({ data }: { data: CreateArticleInput }): Promise<Article> => {
  return api.post('/articles', data);
};

export const useCreateArticle = ({ mutationConfig }: { mutationConfig?: MutationConfig<typeof createArticle> } = {}) => {
  const queryClient = useQueryClient();
  const { onSuccess, ...restConfig } = mutationConfig || {};

  return useMutation({
    onSuccess: (...args) => {
      // 作成後にキャッシュを無効化して最新データを再取得させる
      queryClient.invalidateQueries({ queryKey: getArticlesQueryOptions().queryKey });
      onSuccess?.(...args);
    },
    ...restConfig,
    mutationFn: createArticle,
  });
};
```

### DELETE（削除）

```ts
// src/features/articles/api/delete-article.ts
import { useMutation, useQueryClient } from '@tanstack/react-query';
import { api } from '@/lib/api-client';
import { MutationConfig } from '@/lib/react-query';
import { getArticlesQueryOptions } from './get-articles';

export const deleteArticle = ({ articleId }: { articleId: string }) => {
  return api.delete(`/articles/${articleId}`);
};

export const useDeleteArticle = ({ mutationConfig }: { mutationConfig?: MutationConfig<typeof deleteArticle> } = {}) => {
  const queryClient = useQueryClient();
  const { onSuccess, ...restConfig } = mutationConfig || {};

  return useMutation({
    onSuccess: (...args) => {
      queryClient.invalidateQueries({ queryKey: getArticlesQueryOptions().queryKey });
      onSuccess?.(...args);
    },
    ...restConfig,
    mutationFn: deleteArticle,
  });
};
```

---

## Step 7: コンポーネントを実装する

`src/features/articles/components/` 配下に UI コンポーネントを作成します。

### 作成フォーム

```tsx
// src/features/articles/components/create-article.tsx
import { Plus } from 'lucide-react';
import { Button } from '@/components/ui/button';
import { Form, FormDrawer, Input, Textarea } from '@/components/ui/form';
import { useNotifications } from '@/components/ui/notifications';
import { Authorization, ROLES } from '@/lib/authorization';
import { createArticleInputSchema, useCreateArticle } from '../api/create-article';

export const CreateArticle = () => {
  const { addNotification } = useNotifications();
  const createMutation = useCreateArticle({
    mutationConfig: {
      onSuccess: () => {
        addNotification({ type: 'success', title: 'Article Created' });
      },
    },
  });

  return (
    <Authorization allowedRoles={[ROLES.ADMIN]}>
      <FormDrawer
        isDone={createMutation.isSuccess}
        triggerButton={
          <Button size="sm" icon={<Plus className="size-4" />}>
            Create Article
          </Button>
        }
        title="Create Article"
        submitButton={
          <Button form="create-article" type="submit" size="sm" isLoading={createMutation.isPending}>
            Submit
          </Button>
        }
      >
        <Form
          id="create-article"
          onSubmit={(values) => createMutation.mutate({ data: values })}
          schema={createArticleInputSchema}
        >
          {({ register, formState }) => (
            <>
              <Input label="Title" error={formState.errors['title']} registration={register('title')} />
              <Textarea label="Body" error={formState.errors['body']} registration={register('body')} />
            </>
          )}
        </Form>
      </FormDrawer>
    </Authorization>
  );
};
```

**コンポーネントのルール**:
- `../api/` からのみインポートする（他フィーチャーは絶対にインポートしない）
- バリデーションスキーマを API 層から再利用して `Form` に渡す
- 権限制御が必要なら `<Authorization>` で囲む

---

## Step 8: ルートコンポーネント（ページ）を作る

```tsx
// src/app/routes/app/articles/articles.tsx
import { QueryClient } from '@tanstack/react-query';
import { LoaderFunctionArgs } from 'react-router';
import { ContentLayout } from '@/components/layouts';
import { getArticlesQueryOptions } from '@/features/articles/api/get-articles';
import { CreateArticle } from '@/features/articles/components/create-article';
import { ArticlesList } from '@/features/articles/components/articles-list';

// ページ表示前にデータをプリフェッチする
// → ページを開いた瞬間にデータが表示される（ローディング状態を減らせる）
export const clientLoader =
  (queryClient: QueryClient) =>
  async ({ request }: LoaderFunctionArgs) => {
    const url = new URL(request.url);
    const page = Number(url.searchParams.get('page') || 1);
    const query = getArticlesQueryOptions({ page });
    return queryClient.getQueryData(query.queryKey) ?? (await queryClient.fetchQuery(query));
  };

const ArticlesRoute = () => (
  <ContentLayout title="Articles">
    <div className="flex justify-end">
      <CreateArticle />
    </div>
    <div className="mt-4">
      <ArticlesList />
    </div>
  </ContentLayout>
);

export default ArticlesRoute;
```

---

## Step 9: パス定義

`src/config/paths.ts` に新しいパスを追加します。

```ts
export const paths = {
  app: {
    // ... 既存のパス
    articles: {
      path: 'articles',
      getHref: () => '/app/articles',
    },
    article: {
      path: 'articles/:articleId',
      getHref: (id: string) => `/app/articles/${id}`,
    },
  },
} as const;
```

URL を直書きせずここから参照することで、URL が変わっても一箇所の修正で済みます。

```tsx
// NG
<Link to="/app/articles">記事一覧</Link>

// OK
<Link to={paths.app.articles.getHref()}>記事一覧</Link>
```

---

## Step 10: ルーター登録

`src/app/router.tsx` に新しいルートを追加します。

```ts
{
  path: paths.app.articles.path,
  // lazy: そのページの JS は最初にアクセスしたときだけ読み込まれる（コード分割）
  lazy: () =>
    import('./routes/app/articles/articles').then(convert(queryClient)),
},
```

---

## Step 11: ESLint 設定

`.eslintrc.cjs` にクロスフィーチャーインポートの禁止ルールを追加します。

```js
{
  target: './src/features/articles',
  from: './src/features',
  except: ['./articles'],  // 自フィーチャー内のみ許可
},
```

フィーチャー間で自由にインポートし合うと循環依存が生まれます。このルールで ESLint が自動検知してくれます。

---

## テストを実行して GREEN にする

すべての実装が終わったらテストを実行します。

```bash
cd apps/react-vite
yarn test
# → テストが通る = GREEN
```

GREEN になれば機能の追加は完了です。

---

## テストの種類と使い分け

このプロジェクトでは3種類のテストを使います。どこに何を書くかは決まっています。

| 種類 | 何をテストするか | 置き場所 |
|---|---|---|
| **統合テスト**（メイン） | ユーザー操作の一連の流れ（クリック → API → 表示まで） | `src/app/routes/app/<feature>/__tests__/` |
| **ユニットテスト** | 単一のコンポーネントや関数の動作 | `src/components/.../__tests__/` または `src/features/.../__tests__/` |
| **E2E テスト** | ブラウザ上での完全なフロー | `e2e/tests/` |

**統合テストを主軸にする理由**:

「コンポーネントが正しく描画される」をユニットテストで確認しても、
「ボタンを押したらAPIが叩かれて一覧が更新される」という動作は確認できません。
統合テストはユーザー目線で動作全体を確認するため、最も信頼性が高いテストです。

---

## テストインフラの全体像

```
テストコード
    ↓ renderApp() で描画
コンポーネント
    ↓ useQuery / useMutation で API 呼び出し
Axios（api-client）
    ↓ MSW がインターセプト（実際のネットワーク通信は発生しない）
MSW ハンドラー
    ↓ @mswjs/data の db からデータを取得・操作
インメモリ DB（db.ts）
```

`renderApp()` を呼ぶだけで、ログイン状態・ルーティング・プロバイダーがすべてセットアップされます。
テストコードでは「ユーザー操作と画面の確認」だけに集中できます。

---

## フィーチャーのディレクトリ構成まとめ

```
src/features/articles/
├── api/
│   ├── get-articles.ts      # 一覧取得
│   ├── get-article.ts       # 詳細取得
│   ├── create-article.ts    # 作成（Zod スキーマ含む）
│   ├── update-article.ts    # 更新（Zod スキーマ含む）
│   └── delete-article.ts    # 削除
└── components/
    ├── articles-list.tsx    # 一覧表示
    ├── article-view.tsx     # 詳細表示
    ├── create-article.tsx   # 作成フォーム
    ├── update-article.tsx   # 更新フォーム
    └── delete-article.tsx   # 削除ボタン

src/testing/
├── mocks/
│   ├── db.ts                      # インメモリ DB（article モデルを追加）
│   └── handlers/
│       └── articles.ts            # MSW ハンドラー（新規作成）
├── data-generators.ts             # createArticle() を追加
└── test-utils.tsx                 # そのまま使える（変更不要）

src/app/routes/app/articles/
├── articles.tsx                   # 一覧ページ
├── article.tsx                    # 詳細ページ
└── __tests__/
    └── articles.test.tsx          # 統合テスト（先に書く）
```

---

## 依存方向の原則

```
src/types/         ← どこからでも使える（共通の型）
src/lib/           ← どこからでも使える（設定済みライブラリ）
src/components/    ← どこからでも使える（共通 UI）
        ↑
src/features/      ← app 層からのみ使える（フィーチャー間の参照は禁止）
        ↑
src/app/           ← 最上位（他から参照されない）
```

フィーチャー間でロジックを共有したい場合は `src/hooks/` や `src/utils/` に切り出します。
