---
name: run-tests
description: Run and validate tests for the Yui Protocol. Use after code changes, bug fixes, or when verifying functionality. Use when this capability is needed.
metadata:
  author: yui-synth-lab
---

# Run Tests Skill

テストを実行して結果を検証するスキル。

## コマンド

```bash
# 単体テスト（推奨）
npm run test

# カバレッジ付き
npm run test:coverage

# UIモード
npm run test:ui
```

## E2Eテスト

```bash
npm run test:console
npm run test:system
npm run test:progression
```

## 失敗時の対応

1. エラーメッセージを確認
2. 関連するソースファイルを読む
3. テストファイル（`*.test.ts`）を確認
4. 修正後、再度テスト実行

## テストファイルの場所

- `src/**/*.test.ts` - 単体テスト
- `selenium-tests/*.cjs` - E2Eテスト

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yui-synth-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
