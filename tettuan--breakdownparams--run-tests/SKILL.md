---
name: run-tests
description: Run tests with debug logging. Use when user says 'test', 'テスト', or asks to verify changes. Use when this capability is needed.
metadata:
  author: tettuan
---

デバッグ出力付きでテストを実行する。引数なしの場合は全テスト実行。

```bash
LOG_LEVEL=debug deno test $ARGUMENTS --allow-env --allow-write --allow-read
```

テスト階層: `tests/`下に `00_fixtures/` `01_unit/` `02_integration/` `03_system/`。ファイル命名: `*_test.ts`。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tettuan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
