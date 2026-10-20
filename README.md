# @3pino/design

3pino のアプリ共通のデザインシステム（純 CSS）。[disnans](https://github.com/3pino/disnans) の見た目をもとにしています。

| ファイル | 中身 |
| --- | --- |
| `src/tokens.css` | 色（ライト／ダーク）・文字サイズ・余白・角丸・影のトークン |
| `src/base.css` | リセット、`body`、スクロール・タッチの方針 |
| `src/components.css` | `.btn` `.toggle` `.menu` `.modal` などの部品 |
| `src/index.css` | 上の 3 つをこの順に読み込む |
| `preview.html` | 全部品の一覧（見た目の確認用） |
| `DESIGN.md` | 方針・トークン・部品の説明 |

## 使い方（アプリ側）

### 1. 入れる

```sh
# GitHub から（CI でもそのまま動く）
npm install github:3pino/design#v0.1.0

# ローカルで同時に開発するとき（symlink）
npm install ../design
```

### 2. 読み込む

```css
/* アプリの入り口の CSS */
@import '@3pino/design';
/* アプリ固有の上書きや追加は、このあとに書く */
:root { --font: 'Inter', system-ui, sans-serif; }
```

Vite・webpack など、`@import` で node_modules を解決できるバンドラーなら、そのまま動きます。
バンドラーを使わないときは `node_modules/@3pino/design/src/index.css` を `<link>` で読み込みます。

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
<meta name="color-scheme" content="light dark" />
```

詳しくは [`DESIGN.md`](DESIGN.md)。

## このリポジトリを変更するとき

- 色は直書きせず、必ず `tokens.css` のトークンにする。ライトとダークの両方に値を入れる（`@media` 内と `:root[data-theme='dark']` の 2 か所）。
- 部品の命名は `.part` / `.part-child` / `.part-state`。
- 変更したら `preview.html` をブラウザで開いて、ライト・ダークの両方を目で確認する（`npm run preview` → http://localhost:5180/preview.html）。
- 見た目が変わるときはバージョンを上げ、タグを打つ（`git tag v0.x.0`）。各アプリは、タグを指定して取り込む。
  - トークンの値の調整 → パッチ（`0.1.x`）
  - 部品やトークンの追加 → マイナー
  - クラス名・トークン名の変更や削除 → 破壊的変更。メジャーを上げ、各アプリを順に移行する

## 現状の範囲

- CSS のみ。Svelte などの部品（disnans の `components/ui/*`）は含みません。クラスを付けるだけの薄い包みなので、アプリ側で作ります。
- アイコンは Lucide を各アプリで入れます。
