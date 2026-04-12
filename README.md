# TS Web Bank — Design Tokens & SCSS

Figma Design Tokens 對應的 SCSS、CSS Custom Properties 供元件開發使用。

## 目錄結構

```
scss/
  main.scss                    # 進入點，生成所有 CSS Custom Properties
  abstracts/
    _variables.scss            # Design Tokens（Primitive + Semantic）
    _typography.scss           # Typography Mixins

tokens/
  tokens.json                  # DTCG 格式的 Token 定義檔（對照 Figma Variables）

css/
  main.css                     # 編譯產出（不需手動編輯）
```

## 編譯

```bash
npx sass scss/main.scss css/main.css
```

加上 `--watch` 可在開發時自動編譯：

```bash
npx sass --watch scss/main.scss css/main.css
```

## 架構說明

### Token 層級

| 層級 | 說明 | 範例 |
|------|------|------|
| **Primitive** | 原始值，不直接用於元件 | `$color-ts-red-500`、`$spacing-16`、`$radius-md` |
| **Semantic** | 語意別名，指向 Primitive，元件僅使用此層 | `$text-nav-default`、`$border-input-focus` |
| **Responsive** | Desktop / Mobile 雙值，透過 `@media` 切換 | `--font-size-xl`、`--letter-spacing-wide` |
| **Language** | tw / en 雙語系，透過 `html[lang]` 切換 | `--font-family` |

### CSS Custom Properties 生成規則

`main.scss` 將所有 Token 輸出為 `:root` 上的 CSS Custom Properties：

- **Primitive**（靜態值）：顏色、間距、字重、圓角、行高直接寫入 `:root`
- **Semantic**（靜態值）：surface、bg、text、icon、border、radius、shadow 寫入 `:root`
- **Responsive**（雙值）：由 `$responsive` map 數出後，Desktop 值寫入 `:root`，Mobile 值寫入 `@media (max-width: 1199px)`
- **Language**：預設中文字型，`html[lang='en']` 覆寫為英文字型

## 使用方式

元件使用 CSS Custom Properties（`var(--...)`），不需額外引入即可使用：

```scss
.input-field {
  background: var(--bg-input-default);
  border: 1px solid var(--border-input-default);
  border-radius: var(--radius-input);
  color: var(--text-input-value);

  &::placeholder {
    color: var(--text-input-placeholder);
  }

  &:focus {
    border-color: var(--border-input-focus);
  }
}
```

### 使用 Typography Mixins

`_typography.scss` 提供預組合的文字樣式 mixin，已包含 font-family、font-size、font-weight、line-height（使用 CSS Custom Properties 處理 Responsive 和 Language 切換）。

```scss
@use 'abstracts/typography' as *;

.page-title {
  @include heading-title;   // 3xl / medium
}

.nav-link {
  @include text-nav;        // xl / regular / letter-spacing-wide
  color: var(--text-nav-default);

  &:hover { color: var(--text-nav-hover); }
  &:active { color: var(--text-nav-pressed); }
}

.paragraph {
  @include text-body;       // lg / regular
}

.description {
  @include text-base;       // md / regular
}

.footnote {
  @include text-sm;         // sm / regular
}

.label {
  @include text-xs;         // xs / regular
}
```

> Typography mixin 只負責排版屬性，不包含 `color`。顏色由元件依語意指定。

### 可用的 Mixin 一覽

| Mixin | font-size | font-weight | 說明 |
|-------|-----------|-------------|------|
| `heading-title` | `--font-size-3xl` | medium | 頁面主標題 |
| `text-nav` | `--font-size-xl` | regular | 導航連結（含 letter-spacing） |
| `text-body` | `--font-size-lg` | regular | 內文段落 |
| `text-base` | `--font-size-md` | regular | 基礎文字 |
| `text-sm` | `--font-size-sm` | regular | 小字 |
| `text-xs` | `--font-size-xs` | regular | 最小字 |
| `text-en` | — | regular | 英文字型（不指定 size） |

## CSS Custom Properties 總覽

### Semantic

| 類別 | Custom Property | 用途 |
|------|----------------|------|
| **Surface** | `--surface-base` | 頁面主背景 |
| | `--surface-header` | Header 區塊背景 |
| | `--surface-footer` | Footer 區塊背景 |
| | `--surface-overlay` | 遮罩層（rgba 半透明） |
| **Background** | `--bg-input-default` | Input 欄位背景 |
| | `--bg-icon-btn-hover` | Icon Button hover 背景 |
| | `--bg-icon-btn-pressed` | Icon Button pressed 背景 |
| **Text** | `--text-nav-default` | 導航連結 — default |
| | `--text-nav-hover` | 導航連結 — hover |
| | `--text-nav-pressed` | 導航連結 — pressed |
| | `--text-on-brand` | 品牌色背景上的白字 |
| | `--text-caption` | 輔助說明文字 |
| | `--text-input-placeholder` | Input placeholder 文字 |
| | `--text-input-value` | Input 已填入文字 |
| **Icon** | `--icon-default` | 靜態 UI 圖示 |
| **Icon Button** | `--icon-btn-default` | 可點擊圖示按鈕 |
| **Border** | `--border-divider` | 分隔線 |
| | `--border-accent` | 品牌紅裝飾線 |
| | `--border-input-default` | Input 預設邊線 |
| | `--border-input-focus` | Input 聚焦邊線 |
| **Radius** | `--radius-input` | Input 欄位圓角 |
| **Shadow** | `--shadow-nav` | 導航陰影（box-shadow 用） |

### Responsive — 依斷點切換

| Custom Property | Desktop | Mobile |
|-----------------|---------|--------|
| `--font-size-3xl` | 2rem | 1.5rem |
| `--font-size-2xl` | 1.5rem | 1.25rem |
| `--font-size-xl` | 1.25rem | 1.125rem |
| `--font-size-lg` | 1.125rem | 1rem |
| `--font-size-md` | 1rem | 0.875rem |
| `--font-size-sm` | 0.875rem | 0.75rem |
| `--font-size-xs` | 0.75rem | 0.625rem |
| `--letter-spacing-wide` | 1px | 0 |
| `--letter-spacing-normal` | 0 | 0 |

斷點：Desktop `>= 1200px`、Mobile `< 1200px`

## 注意事項

- 元件使用 Semantic 層的 CSS Custom Properties，盡量不直接引用 Primitive 變數
- 新增 Token 時需同步更新：Figma Variables → `tokens.json` → `_variables.scss` → `main.scss`
- `$responsive` map 僅包含 Desktop / Mobile 有差異的值（font-size、letter-spacing）
- 字重、圓角、行高為 Primitive 靜態值，不隨斷點變化
