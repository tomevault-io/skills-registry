---
name: component-skill
description: 「コンポーネント」「component」というワードが出てきた時にここを参照します。 Use when this capability is needed.
metadata:
  author: shionterunaga
---

# Components skill

これはコンポーネントの実装・修正を行うものです。

## 場所

`src/components`

## 新規作成

- `src/components`に新しいコンポーネント名のフォルダをキャメルケースで作成してください。(e.g. `src/components/box`)
- 中に`コンポーネント名.astro`と`コンポーネント名.css.ts`を作成してください
- コンポーネントのスタイルを反映させる時は以下のようにしてください
  ```astro
  ---
  import * as styles from './box.css'
  ---
  <div class={styles.box}>サンプル</div>
  ```

## 関連

- [スタイルについて](../style/SKILL.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shionterunaga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
