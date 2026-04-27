---
name: review
description: Review code changes for quality, correctness, and adherence to project conventions. Use when user says 'review', 'レビュー', or asks to check code quality. Use when this capability is needed.
metadata:
  author: tettuan
---

# Code Review

コード品質と規約準拠を担保するため、`$ARGUMENTS` 指定ファイル（未指定時は `git diff`）を以下の観点でレビューする。

| 観点 | 基準 |
|------|------|
| 型安全性 | strict mode 準拠、`any` 禁止（正当理由なし）、`PromptResult`/`PromptParams`/`Variables` 型の適切な使用 |
| 命名 | テンプレート変数: `{snake_case}`、TS: camelCase (変数/関数), PascalCase (型/クラス)、テスト: `*_test.ts` |
| エラー処理 | `ValidationError`/`TemplateError`/`FileSystemError` を使用、`PromptResult.success` で伝搬 |
| パス検証 | `src/validation/path_validator.ts` 経由、絶対パス禁止 (`/absolute-path-checker`) |
| テスト | 新規・変更に対応するテストが `01_unit/` → `02_integration/` → `03_system/` に存在 |
| import | `deno.json` の import map エイリアス必須、インライン `jsr:`/`npm:`/`https:` 禁止 |
| フォーマット | 2スペース、100文字幅、ダブルクォート、セミコロン |

ファイルパスと行番号付きで報告する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tettuan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
