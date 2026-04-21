---
name: integration-test
description: 統合テスト（Medium Tests）を実行します。シーケンス図と1:1で対応するテストです。 Use when this capability is needed.
metadata:
  author: hibi-com
---

# Integration Test Skill

統合テスト（Medium Tests）を実行します。

## 使用方法

```text
/integration-test           # 全統合テスト実行
/integration-test post      # Post ドメインのみ
/integration-test crm       # CRM ドメインのみ
```

## 実行コマンド

```bash
bun run integration

# 特定ドメイン
bun run integration --filter={domain}
```

## 参考ドキュメント

詳細なテスト戦略、シーケンス図との対応については以下を参照：

- [テスト戦略](docs/testing/testing-strategy.md) - Google Test Sizes、Medium Testsとシーケンス図の対応
- [テストガイド](docs/testing/testing-guide.md) - Medium Testsの書き方、テストハニカム

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hibi-com) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
