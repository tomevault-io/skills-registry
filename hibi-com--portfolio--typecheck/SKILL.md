---
name: typecheck
description: TypeScript型チェックを実行します。全体または特定パッケージを指定可能。 Use when this capability is needed.
metadata:
  author: hibi-com
---

# TypeCheck Skill

TypeScript型チェックを実行します。

## 使用方法

```text
/typecheck                   # 全体
/typecheck api               # apps/api のみ
/typecheck web               # apps/web のみ
/typecheck db                # packages/db のみ
/typecheck validation        # packages/validation のみ
```

## 実行コマンド

```bash
# 全体
bun run typecheck

# 特定アプリ/パッケージ
bun run typecheck --filter=@portfolio/api
bun run typecheck --filter=@portfolio/web
bun run typecheck --filter=@portfolio/db

# 依存パッケージ含む
bun run typecheck --filter=@portfolio/api...
```

## 参考ドキュメント

TypeScript設定、型定義のベストプラクティスについては以下を参照：

- [コーディング規約](docs/development/coding-standards.md) - 型定義、インポート順序、Props型定義

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hibi-com) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
