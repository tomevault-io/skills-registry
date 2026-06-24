---
name: run-tests
description: Run a specific test file with debug logging Use when this capability is needed.
metadata:
  author: tettuan
---

指定テストをデバッグ出力で実行する。引数なしなら全テスト実行。

```bash
LOG_LEVEL=debug deno test $ARGUMENTS --allow-env --allow-write --allow-read
```

テスト・フィクスチャは `tests/` に配置する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tettuan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
