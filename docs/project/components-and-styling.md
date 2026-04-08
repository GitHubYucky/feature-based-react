# 🧱 コンポーネントとスタイリング

## コンポーネントのベストプラクティス

#### 使う場所のできるだけ近くに置く

コンポーネント・関数・スタイル・状態などは、使われる場所のできるだけ近くに置いてください。これによってコードの可読性と理解しやすさが向上するだけでなく、状態更新時の不要な再レンダリングを防ぎパフォーマンスも向上します。

#### レンダリング関数を内部にネストした巨大コンポーネントを避ける

コンポーネント内に複数のレンダリング関数を定義すると、コンポーネントが成長するにつれて制御不能になります。UI として独立した単位として認識できる部分は、別コンポーネントとして切り出してください。

```javascript
// コンポーネントが大きくなるにつれてメンテナンスが非常に困難になる例
function Component() {
  function renderItems() {
    return <ul>...</ul>;
  }
  return <div>{renderItems()}</div>;
}

// 別コンポーネントとして切り出す
function Items() {
  return <ul>...</ul>;
}

function Component() {
  return (
    <div>
      <Items />
    </div>
  );
}
```

#### 一貫性を保つ

コードスタイルを統一してください。例えばコンポーネント名をパスカルケースで命名するなら、すべての箇所で統一します。コードの一貫性の多くはリンターとフォーマッターで担保できるので、プロジェクトに必ず設定してください。

#### コンポーネントの props 数を制限する

props が多すぎる場合は、コンポーネントを複数に分割するか、children やスロットを使ったコンポジション技法を検討してください。

[コンポジションのコード例](../apps/react-vite/src/components/ui/dialog/confirmation-dialog/confirmation-dialog.tsx)

#### 共有コンポーネントをコンポーネントライブラリとして抽象化する

大規模プロジェクトでは、共有コンポーネントをまとめた抽象レイヤーを作ることが有効です。アプリの一貫性が高まりメンテナンスも容易になります。誤った抽象化を避けるため、コンポーネント作成前に重複を見極めてください。

[コンポーネントライブラリのコード例](../apps/react-vite/src/components/ui/button/button.tsx)

サードパーティのコンポーネントもラップしておくことを推奨します。将来の変更時にアプリの機能に影響を与えずに修正しやすくなります。

[サードパーティコンポーネントのコード例](../apps/react-vite/src/components/ui/link/link.tsx)

## コンポーネントライブラリ

プロジェクトにはモーダル・タブ・サイドバー・メニューなどの UI コンポーネントが必要です。ゼロから作る代わりに、実績のあるコンポーネントライブラリの活用を検討してください。

#### フル機能コンポーネントライブラリ（スタイル済み）

- [Chakra UI](https://chakra-ui.com/) — 開発体験が良く、合理的なデフォルトデザインで高速なプロトタイピングが可能。カスタマイズ性が高くアクセシビリティも標準搭載。

- [AntD](https://ant.design/) — 豊富なコンポーネントを持つライブラリ。管理ダッシュボードに最適。カスタムデザインへのスタイル変更はやや難しい。

- [MUI](https://mui.com/material-ui/) — React で最も人気のあるコンポーネントライブラリ。Material Design のスタイル付きソリューションとして使うことも、スタイルなしのヘッドレスコンポーネントとして使うことも可能。

- [Mantine](https://mantine.dev/) — 豊富なコンポーネントとフックを持つモダンなライブラリ。カスタマイズ性が高く多機能。

#### ヘッドレスコンポーネントライブラリ（スタイルなし）

独自のデザインシステムを実装する場合、フル機能ライブラリのスタイルを上書きするよりも、スタイルなしのヘッドレスコンポーネントを使う方が簡単な場合があります。

- [Radix UI](https://www.radix-ui.com/)
- [Headless UI](https://headlessui.dev/)
- [react-aria](https://react-spectrum.adobe.com/react-aria/)
- [Ark UI](https://ark-ui.com/)
- [Reakit](https://reakit.io/)

## スタイリング

React アプリのスタイリング方法はいくつかあります:

- [tailwind](https://tailwindcss.com/)
- [vanilla-extract](https://github.com/seek-oss/vanilla-extract)
- [Panda CSS](https://panda-css.com/)
- [CSS modules](https://github.com/css-modules/css-modules)
- [styled-components](https://styled-components.com/)
- [emotion](https://emotion.sh/docs/introduction)

※ React Server Components はランタイムスタイリング不要のソリューションが必要な点に注意。

ヘッドレスコンポーネントライブラリの普及に伴い、定義済みコンポーネントとスタイリングがセットで提供されるが、パッケージではなくコードとして提供されカスタマイズ可能な新しい形のライブラリも登場しています。

- [ShadCN UI](https://ui.shadcn.com/)
- [Park UI](https://park-ui.com/)

## Storybook

[Storybook](https://storybook.js.org/) はコンポーネントを単独で開発・テストする優れたツールです。アプリが使用するすべてのコンポーネントのカタログとして機能し、開発や発見性の向上に非常に役立ちます。

[Storybook の Story コード例](../apps/react-vite/src/components/ui/button/button.stories.tsx)
