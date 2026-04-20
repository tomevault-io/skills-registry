---
name: check
description: lint, typecheck, test を一括実行して結果をサマリーする Use when this capability is needed.
metadata:
  author: sei-yukinari
---

# /check - 品質チェック一括実行

lint、型チェック、テストを一括で実行し、結果をサマリーします。

## 実行手順

### 1. チェックコマンドの検出

`package.json` の `scripts` から以下を検出：

- **lint**: `lint`, `eslint`, `biome check`
- **typecheck**: `typecheck`, `type-check`, `tsc --noEmit`
- **test**: `test`, `jest`, `vitest`

他の言語の場合：

- Python: `ruff check`, `mypy`, `pytest`
- Go: `golangci-lint run`, `go vet`, `go test ./...`
- Rust: `cargo clippy`, `cargo test`

### 2. 順次実行

以下の順序で実行（前のステップが失敗しても次を実行）：

1. **Lint** → 結果を記録
2. **Type Check** → 結果を記録
3. **Test** → 結果を記録

### 3. サマリー出力

```
## チェック結果サマリー

| チェック | 結果 | 詳細 |
|---------|------|------|
| Lint | ✅ Pass / ❌ Fail | エラー数, 警告数 |
| Type Check | ✅ Pass / ❌ Fail | エラー数 |
| Test | ✅ Pass / ❌ Fail | テスト数, 失敗数 |

### 修正が必要な項目
- [ファイル:行番号] 問題の説明
```

## 注意事項

- `context: fork` により、メインコンテキストを消費しない
- チェックコマンドが見つからない場合はスキップしてその旨を報告

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sei-yukinari) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
