---
name: unit-test
description: ユニットテスト（Small Tests）を実行します。特定のファイルやパッケージを指定できます。 Use when this capability is needed.
metadata:
  author: hibi-com
---

# Unit Test Skill

ユニットテスト（Small Tests）を実行します。

## 使用方法

```text
/unit-test                        # 全テスト実行
/unit-test api                    # apps/api のテストのみ
/unit-test web                    # apps/web のテストのみ
/unit-test formatDate             # 特定のテストファイル
```

## 実行コマンド

```bash
bun run test

# 特定パッケージ
bun run test --filter={package}
```

## 参考ドキュメント

詳細なテスト戦略、TDD、カバレッジ目標については以下を参照：

- [テスト戦略](docs/testing/testing-strategy.md) - Google Test Sizes、Small Tests作成基準、カバレッジ目標
- [テストガイド](docs/testing/testing-guide.md) - TDD、テストハニカム、MC/DCカバレッジ

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hibi-com) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
