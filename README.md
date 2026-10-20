# @3pino/design-system

3pino のアプリ共通のデザインシステム（純 CSS）。[disnans](https://github.com/3pino/disnans) の見た目をもとにしています。

| ファイル | 中身 |
| --- | --- |
| `src/tokens.css` | 色（ライト／ダーク）・文字サイズ・余白・角丸・影のトークン |
| `src/base.css` | リセット、`body`、スクロール・タッチの方針 |
| `src/components.css` | `.btn` `.toggle` `.menu` `.modal` などの部品 |
| `src/avatar.css` | アバター（optional） |
| `src/index.css` | 上の 3 つをこの順に読み込む |
| `preview.html` | 全部品の一覧（見た目の確認用） |
| `DESIGN.md` | 方針・トークン・部品の説明 |

## 使い方（アプリ側）

### 1. 入れる

```sh
# GitHub から。tag指定すべき。
npm install github:3pino/design-system#v0.4.1
```

### 2. 読み込む

```css
/* アプリの入り口の CSS */
@import '@3pino/design-system';
/* アプリ固有の上書きや追加は、このあとに書く */
:root { --hue: 150; --font: 'Inter', system-ui, sans-serif; }
```

Vite・webpack など、`@import` で node_modules を解決できるバンドラーなら、そのまま動きます。
バンドラーを使わないときは `node_modules/@3pino/design-system/src/index.css` を `<link>` で読み込みます。

```html
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
<meta name="color-scheme" content="light dark" />
```

詳しくは [`DESIGN.md`](DESIGN.md)。

## 決まりごと

- ブレークポイントは **767px**（`@media (max-width: 767px)` がモバイル）。CSS 変数は `@media` の条件に使えないので、各アプリで定数として揃える。
- アイコンは Lucide、線幅 2、大きさは 14 / 16 / 20（`.icon-sm` / `.icon-md` / `.icon-lg`）。
- アイコンだけのボタンには `aria-label`。見える補足は `data-tooltip`。

## このリポジトリを変更するとき

- 色は直書きせず、必ず `tokens.css` のトークンにする。ライトとダークの両方に値を入れる（`@media` 内と `:root[data-theme='dark']` の 2 か所）。
- 部品の命名は `.part` / `.part-child` / `.part-state`。
- 変更したら `preview.html` をブラウザで開いて、ライト・ダークの両方を目で確認する（`npm run preview` → http://localhost:5180/preview.html）。
- 見た目が変わるときはバージョンを上げ、`git tag vx.x.x`。各アプリは、タグを指定して取り込む。破壊的変更ではメジャーを上げ、各アプリを順に移行する

## 現状の範囲

- CSS のみ。Svelte などの部品は含みません。
- アイコンは Lucide を各アプリで入れます。
