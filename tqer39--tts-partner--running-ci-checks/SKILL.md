---
name: running-ci-checks
description: push前にCIチェック（lint、test）をローカルで実行します。CIチェック、lint確認、テスト実行を求められた場合に使用してください。 Use when this capability is needed.
metadata:
  author: tqer39
---

# CI チェック

push 前に GitHub Actions と同じチェックをローカルで実行します。

**注意**: pre-push フックが自動的にCIチェックを実行するため、通常は手動実行不要。

## prek（Git hooks チェック）

```bash
just prek           # 全ファイルに対してチェック
just prek-hook <名前>  # 特定のフックを実行
just prek-update    # フックを最新版に更新
just prek-clean     # キャッシュをクリア
```

## 実行手順

1. ユーザーの意図を確認（全チェック or 個別チェック）
2. 該当するチェックを実行
3. 結果をレポート
4. エラーがあれば修正方法を提示

## チェック内容

### Lint チェック

```bash
# JavaScript/TypeScript
pnpm biome check .

# Python
cd apps/backend && uv run ruff check . && uv run ruff format --check .

# Markdown
pnpm markdownlint-cli2 "**/*.md"

# YAML
pnpm prettier --check "**/*.{yml,yaml}"

# Spell check
pnpm cspell --no-progress "**/*.{md,ts,tsx,js,jsx,py}"
```

### テスト実行

```bash
# Backend
cd apps/backend && uv run pytest

# Frontend
cd apps/frontend && pnpm test
```

## 自動修正

```bash
# すべて自動修正
pnpm ci:lint --fix

# または個別に
pnpm biome check --write .
cd apps/backend && uv run ruff check --fix . && uv run ruff format .
```

## 結果レポート形式

```markdown
## CI チェック結果

### Lint
- Biome: 問題なし
- Ruff: 問題なし
- Markdownlint: 3 件のエラー

### Tests
- Backend: 15 passed
- Frontend: 2 failed

## 修正方法
[エラーごとの修正コマンド]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tqer39) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
