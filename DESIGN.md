# デザインガイド

3pino のアプリ共通のデザインシステムの説明です。

- アイコンは [Lucide](https://lucide.dev/)（線幅 2、丸い端）。大きさは `--icon-sm` 14 / `--icon` 16 / `--icon-lg` 20 の 3 段階
- 文言は日本語、`<html lang="ja">`
- 見た目の確認用に `preview.html`（全部品の一覧。ライト／ダークの切り替え付き）がある

## 目次

1. [デザインの方針](#デザインの方針)
2. [使い方](#使い方)
3. [カラートークン](#カラートークン)
4. [テーマ切り替え](#テーマ切り替え)
5. [タイポグラフィ・形・影・余白](#タイポグラフィ形影余白)
6. [ベース設定（リセット）](#ベース設定リセット)
7. [部品カタログ](#部品カタログ)
8. [レイアウト](#レイアウト)
9. [モーション・アクセシビリティ・タッチ](#モーションアクセシビリティタッチ)
10. [使うときのチェックリスト](#使うときのチェックリスト)

---

## デザインの方針

- 色は、アクセント（青）・メンション（茶）・危険（赤）・成功（緑）・警告（黄）の 5 色のみ。
- ライト／ダーク両対応。
- 半透明の色は `color-mix(in oklch, …)` で派生させる。

## 使い方

1. 依存に入れる（導入方法は `README.md`）。
2. アプリの CSS の入り口で読み込む。まとめて読むか、個別に読む:
   ```css
   @import '@3pino/design-system';            /* = tokens → base → components */
   /* 個別に読むなら */
   @import '@3pino/design-system/tokens.css';
   @import '@3pino/design-system/base.css';
   @import '@3pino/design-system/components.css';
   ```
   | ファイル | 中身 |
   | --- | --- |
   | `tokens.css` | 色・文字サイズ・余白・角丸・影などのトークン（ライト／ダーク） |
   | `base.css` | リセット、`body`、スクロール・タッチの方針、フォーカスの輪郭、`prefers-reduced-motion` |
   | `components.css` | `.btn`・`.toggle`・`.menu`・`.modal` などの部品 |

   `components.css` は `tokens.css` のトークンだけに依存している（直書きの色はない）。
   **アプリごとの違いは、読み込んだあとにトークンを上書きして出す**（共有の CSS は編集しない）:
   ```css
   @import '@3pino/design-system';
   :root { --hue: 150; }   /* 色味を変える（ライト・ダーク両方に効く） */
   ```
   色トークン（`--accent` など）そのものを上書きするときは、ダーク用のセレクタ（`:root[data-theme='dark']` と `@media` 内の `:root:not([data-theme='light'])`）のほうが詳細度が高いので、ダーク側も同じセレクタで上書きする。色以外（文字サイズ・余白・角丸・フォント）は `:root` だけでよい。
3. `<meta name="color-scheme" content="light dark" />` と `viewport` を入れる:
   ```html
   <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
   <meta name="color-scheme" content="light dark" />
   ```
   （disnans ではモバイルのピンチ拡大を止めるため `maximum-scale=1, user-scalable=no, interactive-widget=resizes-content` も付けている。拡大を許すなら不要）
4. アイコンに Lucide を入れる。

## カラートークン

中立色は `oklch(L var(--chroma) var(--hue))`。**L（明度）だけを変える**。

```css
--hue: 248;      /* 中立色・アクセントの色相 */
--chroma: 0.015; /* 中立色の彩度 */
--hue-accent: var(--hue);  --hue-mention: 75;  --hue-danger: 25;  --hue-success: 155;  --hue-warning: 85;
--chroma-accent / -mention / -danger / -success / -warning   /* 意味色の彩度。ライトとダークで値が違う */
```

**アプリの色味を変えるときは、`:root` で `--hue`（必要なら `--chroma`・`--hue-*`）を上書きする**。ライト・ダークの両方に効く。

### 中立色（L の値）

| トークン | ライト | ダーク | 用途 |
| --- | --- | --- | --- |
| `--bg` | 0.97 | 0.17 | 画面の地。入力欄の背景にも使う |
| `--surface` | 0.94 | 0.21 | ナビ・メニュー・モーダルなど、一段浮いた面 |
| `--surface-2` | 0.9 | 0.26 | もう一段上。ボタンの地、ホバー・押下 |
| `--border` | 0.84 | 0.32 | 枠・区切り線・スライダーのトラック |
| `--border-hover` | 0.76 | 0.44 | ホバー時の枠 |
| `--border-focus` | 0.92 | 0.43 | 入力欄フォーカス時の枠 |
| `--field-focus-bg` | 0.97 | 0.245 | 入力欄フォーカス時の背景 |
| `--ring` | 0.45 | 0.84 | キーボードフォーカスの輪郭 |
| `--text` | 0.22 | 0.93 | 本文 |
| `--text-muted` | 0.48 | 0.7 | 補足・アイコン・非選択 |

### 意味色

L は **ライト 0.45 / ダーク 0.74**。彩度はライト / ダーク。

| トークン | 色相 | 彩度 | 用途 |
| --- | --- | --- | --- |
| `--accent` | `--hue-accent`（= `--hue`） | 0.11 / 0.13 | 主ボタン・選択中・リンク・トグル ON |
| `--mention` | 75 | 0.09 / 0.12 | 自分宛て・検索ヒットの強調 |
| `--danger` | 25 | 0.15 / 0.15 | 削除・エラー・未読の点 |
| `--success` | 155 | 0.1 / 0.12 | 成功 |
| `--warning` | 85 | 0.09 / 0.13 | 注意 |

### 派生・その他

```css
--on-accent:    var(--bg);                                   /* 意味色の地に載せる文字 */
--accent-soft:  color-mix(in oklch, var(--accent) 14%, transparent);   /* 選択・強調の薄い地。mention 16%・danger 14% も同様 */
--hover:        color-mix(in oklch, var(--surface-2) 60%, transparent);
```

| トークン | 値 |
| --- | --- |
| `--shadow` | ポップアップの影（ライト / ダークで別値） |
| `--shadow-sm` / `--shadow-media` | トグル・スライダーのつまみ / 画像ビューアの画像 |
| `--scrim` / `--scrim-strong` | モーダルの幕 / 画像ビューアの幕（ライト・ダーク共通） |
| `--on-scrim` / `--on-scrim-soft` | 幕の上の文字 / ボタンの背景 |
| `--mark` | 本文中のハイライト（黄色の半透明） |
| `--avatar-l` / `--avatar-c` | イニシャルアバターの明度（ライト 0.45 / ダーク 0.74）/ 彩度 0.08 |
| `--disabled-decoration` | 無効な項目の文字の装飾。既定 `none`。`line-through` にすると取り消し線（`.btn`・入力欄・`.check` に効く） |

### アバター（任意）

`src/avatar.css`（`index.css` には含まない）。`@import '@3pino/design-system/avatar.css'`。
色相 `--h`（0–360）は ID などから**各アプリが決める**（ハッシュは CSS で書けない）。例:

```ts
const hueOf = (id: string) => [...id].reduce((h, c) => (h * 31 + c.charCodeAt(0)) >>> 0, 0) % 360;
```

## テーマ切り替え

- 既定はシステム設定に追従（`@media (prefers-color-scheme: dark)` 内で `:root:not([data-theme='light'])` に値を入れる）。
- 明示的に選ばせるときは `<html data-theme="light|dark">` を付ける（`system` のときは属性を外す）。
- ダークの値は **2 か所に同じものを書く**（`@media` 内と `:root[data-theme='dark']`）。トークンを足したら両方に足すこと。
- `color-scheme: light | dark` も一緒に切り替える（スクロールバー・フォーム部品の色が追従する）。

利用者向けにトークンを上書きするカスタムテーマの仕組みは [`THEMES.md`](THEMES.md)。

## タイポグラフィ・形・影・余白

すべてトークン（`:root`、ライト・ダーク共通）。部品の CSS は直書きの px ではなく、これらを使う。
直書きは、部品の寸法から決まる値（`.select` の矢印の分の右 padding 32px、スライダーのつまみの margin -6px）と `letter-spacing` だけ。

```css
/* フォント。Web フォントは使わない（OS 標準） */
--font:  system-ui, -apple-system, 'Segoe UI', 'Hiragino Sans', 'Hiragino Kaku Gothic ProN',
         'Noto Sans JP', 'Yu Gothic UI', Meiryo, sans-serif;
--serif: ui-serif, 'Hiragino Mincho ProN', 'Yu Mincho', 'Noto Serif JP', 'Noto Serif', Georgia, serif;
--mono:  ui-monospace, 'SFMono-Regular', 'Cascadia Code', 'Noto Sans Mono', Menlo, Consolas, monospace;
```


| 種別 | トークン |
| --- | --- |
| 文字サイズ | `--fs-xs` 10（バッジ・ナビのラベル）/ `--fs-sm` 12（小見出し・補足・説明文）/ `--fs-base` 14（設定の行・トースト）/ `--fs-body` 15（本文）/ `--fs-lg` 16（モーダルのタイトルなど）/ `--fs-xl` 20（大見出し）/ `--fs-2xl` 24 |
| 太さ | `--fw-medium` 500 / `--fw-semibold` 600 / `--fw-bold` 700 |
| 行の高さ | `--lh-tight` 1.4 / `--lh-body` 1.55（本文） |
| 余白 | `--space-1` 2 / `-2` 4 / `-3` 6 / `-4` 8 / `-5` 10 / `-6` 12 / `-7` 16 / `-8` 24（px。gap・padding・margin 用） |
| 角丸 | `--radius-xs` 4 / `--radius-sm` 6（ボタン・入力欄）/ `--radius` 10（メニュー・カード）/ `--radius-lg` 14（モーダル・入力カード）/ `--radius-xl` 16（吹き出し）/ `--radius-pill` 999。丸（アバター・バッジ）は `50%` |

主な使い分け: 本文 `--fs-body` + `--lh-body`、設定の行 `--fs-base`（説明文 `--fs-sm`）、小見出し `--fs-sm` + `--fw-bold`（セクション見出しは字間 0.06em）、キー表示 `--mono` + `--fs-sm`。

高さの基準（直書き）: ボタン 36px / 入力欄 40px / アイコンボタン 34px / トグル 38×22 / セグメント内ボタン 32px。画面端の左右余白は `--space-7`（16px）。

**タイポグラフィを変えたいとき**は、トークンの値を変えるだけでよい（例: 本文を 16px にするなら `--fs-body: 16px`）。

## ベース設定（リセット）

`src/base.css` に入っているもの。

- `box-sizing: border-box`（全要素）
- `html, body`: `height: 100%; overflow: hidden; overscroll-behavior: none`（画面全体はスクロールさせず、`.scroll` の領域だけが動く）
- `#app`: `height: var(--app-height, 100dvh)`（モバイルでキーボードが出たときに JS で `--app-height` を `visualViewport.height` に合わせている。不要なら `100dvh` のみ）
- `button, input, textarea, select { font: inherit; color: inherit }`
- `a { color: var(--accent) }`
- `:focus-visible { outline: 2px solid var(--ring); outline-offset: 2px }`
- `::selection`: アクセント 35%
- ボタン・ナビ・見出し・ラベルなどは `user-select: none; -webkit-touch-callout: none`（本文だけ選択できる）
- `-webkit-tap-highlight-color: transparent`

## 部品カタログ

クラス名の規約: 部品 `.part`、中身 `.part-child`、状態 `.part-state`（例 `.toggle-on`）。位置や大きさの調整は使う側が別クラスで行う。

| 部品 | クラス | 見た目の要点 |
| --- | --- | --- |
| アイコン | `.icon`（`.icon-sm` 14 / `.icon-md` 16 / `.icon-lg` 20） | `flex: none`（縮まない）。Lucide、線幅 2 固定。大きさは 3 段階だけ使う |
| アイコンボタン | `.icon-btn`（`.active`） | 34px 角・`--radius-sm`・背景なし・`--text-muted`。ホバーで `--hover` 地 + `--text`。`.active` は `--accent` + `--accent-soft` 地。disabled は opacity .45 |
| ボタン | `.btn`（`.primary` `.danger` `.ghost` `.btn-icon-only`） | 高さ 36px・横 12px・枠 1px `--border`・地 `--surface-2`・font-weight 500。`.primary` は `--accent` 地 + `--on-accent` 文字、`.danger` は `--danger` 地。ホバーは `brightness(1.06)`（通常ボタンは枠が `--text-muted` に）。`.ghost` は背景も枠もなく、ホバー・フォーカスで `--hover`、押下で `--surface-2`。disabled は opacity .5 |
| 入力欄 | `.input` / `.textarea` / `.select` | 高さ 40px（textarea は最小 80px・縦にだけ伸びる）・`--bg` 地・枠 `--border`。フォーカスで枠 `--border-focus` + 地 `--field-focus-bg`（青くしない）。エラーは `aria-invalid="true"`（枠が `--danger`）、無効は `disabled`（opacity .5） |
| 補足・エラー文 | `.field-hint` / `.field-error` | 入力欄の下の 12px。`--text-muted` / `--danger` |
| チェック・ラジオ | `.checkbox` / `.radio` + `.check`（ラベル） | 18px。ON は `--accent` 地（チェックは `--on-accent`）。`indeterminate`・`aria-invalid`・`disabled` 対応。`<label class="check"><input type="checkbox" class="checkbox">名前</label>` |
| 小見出し | `.field-label` | 入力欄の上に置く 12px/700 の `--text-muted` |
| トグル | `.toggle`（`.toggle-on`） | 38×22・丸（`--radius-pill`）。OFF は `--border` 地、ON は `--accent` 地。つまみは 16px の `--bg` 色の丸で、ON で 16px 右へ（`--dur`） |
| スライダー | `.slider` / `-input` / `-value` | トラック 4px（`--border`）、つまみ 16px の `--accent` 丸。値は 12px・等幅数字・右寄せ |
| セグメント | `.segmented` / `-option`（`-selected`） | 地 `--surface-2` の枠、内側 4px。選択中の項目は `--bg` 地 + `--text`、他は `--text-muted`。高さ 32px・12px/600 |
| タブバー | `.nav-bar` / `-item`（`-selected`） | 下タブ: 上線 1px、地 `--surface`、アイコン + 10px ラベルを等分。選択は `--accent`。`.nav-bar-side` で幅 76px の左縦並び（右線 1px） |
| バッジ | `.badge` | 高さ 18px・min-width 18px・丸・`--accent` 地・10px/700 |
| 未読の点 | `.nav-bar-item-alert` | 9px の `--danger` 丸 + `0 0 0 2px var(--surface)` の縁取り |
| キー表示 | `.kbd` | 枠 1px（下だけ 2px）・`--surface-2` 地・等幅 12px |
| 区切り線 | `.divider` | `border-top: 1px solid var(--border)` |
| 設定セクション | `.setting-section` | 上下 18px。前にセクションがあるときだけ上端に区切り線。見出し 12px/700/`--text-muted`/字間 0.06em。中身は 10px 間隔 |
| 設定の行 | `.setting-row` | 左に名前 + 説明、右に操作。14px、間隔 12px。`.setting-row-button` で行全体が ghost ボタン |
| 状態表示 | `.status-line`（`-muted` `-ok` `-accent` `-warn` `-error`） | アイコン + 14px/600 の 1 行。色は意味色 |
| メニュー | `.menu` / `.menu-item`（`-danger`） | `--surface` 地・枠 1px・`--radius`・`--shadow`・padding 4px・min-width 200px。項目は padding 8×10・`--radius-sm`・間隔 10px、ホバー/押下で `--surface-2`。`--z-menu`（背後に透明な `.menu-backdrop`） |
| 補完候補 | `.suggest-list` / `-item`（`-selected`） | メニューと同じ面。選択中は `--accent-soft` 地。タイトル 600、詳細は 12px `--text-muted` で省略表示 |
| モーダル | `.modal-scrim` / `.modal` | 幕は `--scrim`。本体は幅 `min(420px, 100vw-32px)`・padding 16px・角丸 14px・`--surface`・枠 1px・`--shadow`・中央配置。出現はフェード + わずかなせり上がり（`--dur`）。ボタンは右寄せ・間隔 8px（`.modal-actions`） |
| トースト | `.toasts` > `.toast`（`-error` `-text` `-action`） | 画面上部中央（幅 min(440px, 100vw-24px)）、`--surface`・枠・`--shadow`・14px。エラーは左に 3px の `--danger` 帯だけ |
| ツールチップ | `[data-tooltip]`（`data-tooltip-pos="bottom"`） | アイコンだけのボタンの補足。ホバー（0.4s 後）かキーボードフォーカスで出る。読み上げは `aria-label`。親の `overflow` で切れるので端は下向きに |
| スクロール領域 | `.scroll` | `overflow-y: auto; scrollbar-width: thin; scrollbar-color: var(--border) transparent` |
| 回転 | `.spin` | 0.9s linear 無限 |

重なり順は `--z-dropdown` 20 / `--z-menu` 30 / `--z-modal` 80 / `--z-toast` 90 / `--z-tooltip` 100（`tokens.css`）。

## レイアウト

- 幅の境目は **767px**（`@media (max-width: 767px)` がモバイル）。CSS 変数は `@media` の条件に使えないので、各アプリで定数として揃える。
- **広い画面**: 左にナビ（76px、縦並び）| メイン（`minmax(0,1fr)`）| 右パネル（任意。`minmax(320px, 420px)`、左に 1px の枠）の 3 列グリッド。
- **モバイル**: 縦積み。ナビは下のタブバー、パネルは全画面（`position: fixed; inset: 0`、右から 0.18s でスライドイン）。
- 変数: `--nav-side-w: 76px`、`--panel-w: 420px`。
- セーフエリア: 上は `.top-bar`（`padding-top: calc(env(safe-area-inset-top) + 8px)`）、下は `max(14px, env(safe-area-inset-bottom))`。`viewport-fit=cover` が前提。
- 画面の区切りは**面の色差 + 1px の `--border`**。影は使わない。

## モーション・アクセシビリティ・タッチ

- 動きは `--dur`（0.15s）に統一。イージングは `--ease`（`ease-out`）。
- `prefers-reduced-motion: reduce` では全アニメーション・トランジションを 0.01ms にする（`base.css` に入っている）。
- ホバー表現は `@media (hover: hover)` の中だけ（タッチ操作のデバイスで押したあと明るいまま残るのを防ぐ）。`.menu-item`・`.icon-btn` が該当。
- アイコンだけのボタンには必ず `aria-label`（見える補足は `data-tooltip`）。飾りのアイコンは `aria-hidden`。
- キーボードフォーカスは `:focus-visible` の 2px `--ring` 輪郭。
- 色だけに頼らない（状態はアイコン・文言・形も併用）。
- タッチ: ピンチ拡大を止める `touch-action: pan-x pan-y`、ボタン類のテキスト選択・長押しコールアウトを止める。

## 使うときのチェックリスト

- [ ] `styles/` の 3 ファイルを取り込み、`color-scheme` メタを入れた
- [ ] ダークのトークンを `@media` 内と `[data-theme='dark']` の両方で更新した
- [ ] 色は必ずトークンから使っている（直書きの色がない）
- [ ] 新しい部品も `.part` / `.part-child` / `.part-state` の命名にした
- [ ] 角丸は `--radius-*` のどれかに合わせた
- [ ] ホバーは `@media (hover: hover)` に入れた
- [ ] アイコンだけのボタンに `aria-label` を付けた
