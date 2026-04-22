---
name: corder-test-generation
description: | Use when this capability is needed.
metadata:
  author: takemi-ohama
---

# Corder Test Generation Skill

## 概要

corderエージェントが既存のコードに対してテストを自動生成する際に使用します。Jest、Mocha、pytestなどの主要なテストフレームワークに対応。

## クイックリファレンス

### AAA パターン

```javascript
test('should do something', () => {
  // Arrange: テストデータを準備
  const input = { ... };
  const expected = { ... };

  // Act: テスト対象を実行
  const result = functionUnderTest(input);

  // Assert: 結果を検証
  expect(result).toEqual(expected);
});
```

### 基本コマンド

```bash
node scripts/generate-tests.js src/utils/calculator.js
```

### カバレッジ目標

- **ユニットテスト**: 80%以上
- **クリティカルパス**: 100%
- **エッジケース**: 主要な境界値をカバー

## ベストプラクティス

| DO | DON'T |
|----|-------|
| AAAパターンに従う | テストの重複 |
| 1テスト = 1アサーション | 実装の詳細に依存 |
| エッジケースをテスト | 過剰なモック |
| 独立したテスト（実行順序に依存しない） | 外部依存（ネットワーク等） |
| わかりやすいテスト名 | テストのテスト |

## 詳細ガイド

| ファイル | 内容 |
|---------|------|
| `01-patterns.md` | AAAパターン、エッジケース、モック・スパイの詳細 |
| `02-examples.md` | ユニットテスト、API統合テストの実装例 |

## 関連Skill

- **corder-code-templates**: テスト対象のコード生成

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/takemi-ohama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
