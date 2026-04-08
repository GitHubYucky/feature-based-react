# Review Scope

優先して確認する対象:

- `src`
- `docs`
- `.claude`
- `package.json`
- `tsconfig*.json`
- `vite.config.ts`
- `eslint.config.js`
- `README.md`

深掘りしすぎない対象:

- `node_modules`
- `.git`

方針:

- 実装上の真実はまず `src` にあると考える
- docs が将来像を語っている場合は、その前提を明示する
- `.claude` はノイズではなく運用資産として扱う

