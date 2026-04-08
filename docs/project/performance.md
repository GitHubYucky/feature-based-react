# 🚄 パフォーマンス

### コード分割

コード分割とは、本番用 JavaScript を小さなファイルに分割してアプリの読み込み時間を最適化する技術です。必要なコードだけを必要なタイミングでフェッチすることで、アプリを部分的にダウンロードできるようになります。

理想的にはルートレベルでコード分割を実装し、初期ロード時に必要最低限のコードだけ読み込み、残りは遅延ロードします。ただし、過剰なコード分割はチャンクの取得リクエスト数が増えてパフォーマンス低下につながるため注意してください。

[コード分割のコード例](../apps/react-vite/src/app/router.tsx)

### コンポーネントと状態の最適化

- すべてを一つの状態にまとめないこと。不要な再レンダリングが発生します。代わりに、使用箇所に応じてグローバル状態を複数の状態に分割してください。

- 状態は使われる場所のできるだけ近くに置くこと。依存していないコンポーネントの再レンダリングを防げます。

- 重い計算で初期化する状態は、直接実行せず初期化関数を使うこと。関数は一度しか実行されません:

```javascript
// 毎回の再レンダリングで実行されてしまう（非推奨）
const [state, setState] = React.useState(myExpensiveFn());

// 一度だけ実行される（推奨）
const [state, setState] = React.useState(() => myExpensiveFn());
```

- 多くの要素を同時に追跡する状態が必要な場合は、[jotai](https://jotai.pmnd.rs/) などアトミック更新に対応した状態管理ライブラリを検討してください。

- React Context は慎重に使うこと。テーマ・ユーザーデータ・小さなローカル状態など、更新頻度の低いデータに向いています。更新頻度が高いデータには [use-context-selector](https://github.com/dai-shi/use-context-selector) の使用や、[状態を持ち上げる](https://react.dev/learn/sharing-state-between-components#lifting-state-up-by-example)・[適切なコンポジション](https://react.dev/learn/passing-data-deeply-with-context#before-you-use-context)を先に検討してください。

- 頻繁な更新が予想される場合は、ランタイムスタイリング（[emotion](https://emotion.sh/docs/introduction)、[styled-components](https://styled-components.com/)）からゼロランタイムスタイリング（[tailwind](https://tailwindcss.com/)、[vanilla-extract](https://github.com/seek-oss/vanilla-extract)、[CSS modules](https://github.com/css-modules/css-modules)）への移行を検討してください。

### children を使った最も基本的な最適化

`children` プロパティはコンポーネントを最適化する最も基本的な方法です。適切に使うと不要な再レンダリングを大幅に削減できます。`children` として渡された JSX は独立した VDOM 構造であり、親によって再レンダリングされません:

```javascript
// 最適化前
const App = () => <Counter />;

const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((count) => count + 1)}>
        count is {count}
      </button>
      <PureComponent /> // "count" が更新されるたびに再レンダリングされる
    </div>
  );
};

const PureComponent = () => <p>Pure Component</p>;

// 最適化後
const App = () => (
  <Counter>
    <PureComponent />
  </Counter>
);

const Counter = ({ children }) => {
  const [count, setCount] = useState(0);

  return (
    <div>
      <button onClick={() => setCount((count) => count + 1)}>
        count is {count}
      </button>
      {children} // "count" が更新されても再レンダリングされない
    </div>
  );
};

const PureComponent = () => <p>Pure Component</p>;
```

### 画像の最適化

ビューポート外の画像は遅延ロードを検討してください。

高速な読み込みのために WEBP などのモダンな画像フォーマットを使用してください。

クライアントの画面サイズに最適な画像を読み込むために `srcset` を使用してください。

### Web Vitals

Google が Web Vitals をインデックスの評価基準に加えて以来、[Lighthouse](https://web.dev/measure/) や [Pagespeed Insights](https://pagespeed.web.dev/) でのスコアを定期的に確認してください。

### データのプリフェッチ

ユーザーがページに遷移する前にデータをプリフェッチすることが可能です。`@tanstack/react-query` の `queryClient.prefetchQuery` メソッドを使うことで、特定のクエリのデータを事前に取得しておき、ページ遷移時の読み込み時間を短縮できます。

[データプリフェッチのコード例](../apps/react-vite/src/features/discussions/components/discussions-list.tsx)
