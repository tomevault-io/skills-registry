---
name: reviewing-type-safety
description: > Use when this capability is needed.
metadata:
  author: thkt
---

# 型安全レビュー

## 検出

| ID  | パターン                      | 修正                          |
| --- | ----------------------------- | ----------------------------- |
| TS1 | `any`                         | `unknown` + 型ガード          |
| TS1 | 暗黙的any                     | 明示的な型アノテーション      |
| TS2 | `value as Type`               | 型ガード関数                  |
| TS2 | `value!` (非nullアサーション) | 明示的なnullチェック          |
| TS3 | `function fn(data)` (型なし)  | `function fn(data: Type)`     |
| TS3 | 戻り値型の欠如                | 明示的な `: ReturnType`       |
| TS4 | 網羅的でない`default:`        | `default: assertNever(value)` |
| TS5 | `strictNullChecks: false`     | tsconfigで有効化              |
| TS5 | `noImplicitAny: false`        | tsconfigで有効化              |

## 基準

型カバレッジ >= 95%。Any使用 = 0。Strictモード全て有効。

## 参照

| トピック   | ファイル                                          |
| ---------- | ------------------------------------------------- |
| カバレッジ | `${CLAUDE_SKILL_DIR}/references/type-coverage.md` |
| ガード     | `${CLAUDE_SKILL_DIR}/references/type-guards.md`   |
| Strict     | `${CLAUDE_SKILL_DIR}/references/strict-mode.md`   |
| Result     | `${CLAUDE_SKILL_DIR}/references/result-type.md`   |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thkt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
