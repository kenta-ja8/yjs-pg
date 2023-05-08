# Yjs 検証リポジトリ

## 起動
```
pnpm install
pnpm -r run --parallel dev
```

## pnpm でパッチをあてる
```
pnpm patch yjs
cd {patch-yjs-repository}
git apply {this-repository}/vite-project/patches/yjs@13.5.52.patch
-- edit mjs source -- 
cd {this-repository}/vite-project
pnpm run dev
```

