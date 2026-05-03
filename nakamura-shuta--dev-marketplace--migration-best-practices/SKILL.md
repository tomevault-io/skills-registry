---
name: migration-best-practices
description: | Use when this capability is needed.
metadata:
  author: nakamura-shuta
---

# Database Migration Best Practices

## 命名規則

- ファイル名: `YYYYMMDDHHMMSS_description.sql`
- 例: `20250121120000_add_user_email.sql`

## マイグレーションルール

### UP Migration（適用）
- 必ずトランザクション内で実行
- カラム追加は`NOT NULL`制約に注意（デフォルト値を設定）
- インデックス作成は大規模テーブルでは段階的に

### DOWN Migration（ロールバック）
- すべてのUPに対応するDOWNを必ず用意
- データ損失の可能性がある変更は警告コメント必須

## 禁止事項

- 既存マイグレーションファイルの変更禁止
- 本番環境で直接DDL実行禁止
- カラム削除前にデータ確認必須

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nakamura-shuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
