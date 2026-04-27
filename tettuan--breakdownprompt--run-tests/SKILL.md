---
name: run-tests
description: Run tests with debug logging. Use when user says 'test', 'テスト', or asks to verify changes. Use when this capability is needed.
metadata:
  author: tettuan
---

変更を検証するため、テストをデバッグ出力付きで実行する。

```bash
# 指定ファイル
LOG_LEVEL=debug deno test $ARGUMENTS --allow-env --allow-write --allow-read
# 全テスト（引数なし時）
LOG_LEVEL=debug deno test --allow-env --allow-write --allow-read
```

テスト階層: `tests/` — `00_fixtures/` (fixture) → `01_unit/` → `02_integration/` → `03_system/`。ファイル命名: `*_test.ts`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tettuan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
