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
# 全CI（推奨）
deno task ci
```

## テスト構造

### 単体テスト（lib/ 配下、実装と同一ディレクトリ）

`lib/factory/*.test.ts`, `lib/types/*_test.ts` など

### 統合・E2Eテスト（tests/ 配下、番号付きドメイン構造）

| Dir | Content |
|-----|---------|
| `tests/0_core_domain/` | 核心ドメイン統合テスト（プロンプトパス解決等） |
| `tests/4_cross_domain/` | ドメイン間統合・E2Eテスト |
| `tests/integration/` | JSR統合・設定プロファイルテスト |
| `tests/fixtures/` | テストフィクスチャ |

### 実行順序（CLAUDE.md 規約）

1. `deno task ci` で全体把握
2. `DEBUG=true deno task ci` で詳細確認
3. 個別テストファイルで特定エラー調査

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tettuan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
