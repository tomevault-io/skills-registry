---
name: verification-loop-typescript
description: TypeScript プロジェクトの品質検証ループ。ESLint、tsc、Jest/Vitest を実行して問題を自動修正。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# TypeScript 検証ループスキル

TypeScript プロジェクトのための包括的な検証システム。

## いつ使用するか

このスキルを呼び出す:
- 機能または重要なコード変更を完了した後
- PRを作成する前
- 品質ゲートがパスすることを確認したいとき
- リファクタリング後

## 検証フェーズ

### フェーズ1: ビルド検証

```bash
# プロジェクトがビルドされるかチェック
npm run build 2>&1 | tail -20
# または
pnpm build 2>&1 | tail -20
```

ビルドが失敗したら、続行前に停止して修正します。

### フェーズ2: 型チェック (tsc)

```bash
# TypeScript 型チェック
npx tsc --noEmit 2>&1 | head -30
```

すべての型エラーを報告します。続行前に重要なものを修正します。

### フェーズ3: ESLint チェック

```bash
# Lint チェック
npm run lint 2>&1 | head -30
# または
npx eslint . --ext .ts,.tsx 2>&1 | head -30

# 自動修正を適用
npx eslint . --ext .ts,.tsx --fix 2>&1
```

### フェーズ4: テストスイート (Jest/Vitest)

```bash
# カバレッジ付きでテストを実行
npm run test -- --coverage 2>&1 | tail -50

# または Vitest
npx vitest run --coverage 2>&1 | tail -50

# カバレッジしきい値をチェック
# 目標: 最低80%
```

レポート:
- 総テスト数: X
- 成功: X
- 失敗: X
- カバレッジ: X%

### フェーズ5: セキュリティスキャン

```bash
# シークレットをチェック
grep -rn "sk-" --include="*.ts" --include="*.tsx" . 2>/dev/null | head -10
grep -rn "api_key" --include="*.ts" --include="*.tsx" . 2>/dev/null | head -10
grep -rn "password" --include="*.ts" --include="*.tsx" . 2>/dev/null | head -10

# console.log をチェック（本番コードには使用しない）
grep -rn "console.log" --include="*.ts" --include="*.tsx" src/ 2>/dev/null | head -10

# デバッグコードをチェック
grep -rn "debugger" --include="*.ts" --include="*.tsx" . 2>/dev/null | head -10
```

### フェーズ6: Diff レビュー

```bash
# 変更内容を表示
git diff --stat
git diff HEAD~1 --name-only
```

各変更ファイルをレビューする対象:
- 意図しない変更
- 欠けているエラー処理
- 潜在的なエッジケース
- 型定義の欠落

## 自動修正フロー

```bash
# 1. ESLint を実行して自動修正
npx eslint . --ext .ts,.tsx --fix

# 2. Prettier を実行（使用している場合）
npx prettier --write .

# 3. 型チェック
npx tsc --noEmit

# 4. テストを実行
npm run test -- --coverage

# 5. ビルドを確認
npm run build

# 6. すべてパスしたら完了
```

## 出力フォーマット

すべてのフェーズを実行した後、検証レポートを作成:

```
検証レポート (TypeScript)
==================

ビルド:        [PASS/FAIL]
型 (tsc):      [PASS/FAIL] (Xエラー)
Lint (ESLint): [PASS/FAIL] (X警告)
テスト:        [PASS/FAIL] (X/Y成功、Z%カバレッジ)
セキュリティ:  [PASS/FAIL] (X問題)
Diff:          [Xファイル変更]

全体:          [準備完了/未完了] PR用

修正すべき問題:
1. ...
2. ...
```

## CI/CD 統合

```yaml
# .github/workflows/verify.yml
name: Verify

on: [push, pull_request]

jobs:
  verify:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'

      - name: Install dependencies
        run: npm ci

      - name: Type check
        run: npx tsc --noEmit

      - name: ESLint check
        run: npm run lint

      - name: Run tests
        run: npm run test -- --coverage

      - name: Build
        run: npm run build
```

## Jest カバレッジ設定

```json
// jest.config.js または package.json
{
  "jest": {
    "collectCoverage": true,
    "coverageThreshold": {
      "global": {
        "branches": 80,
        "functions": 80,
        "lines": 80,
        "statements": 80
      }
    },
    "coverageReporters": ["text", "lcov", "html"]
  }
}
```

## Vitest カバレッジ設定

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config'

export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80
      }
    }
  }
})
```

## pre-commit フック

```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged"
    }
  },
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```

## 連続モード

長いセッションの場合、15分ごとまたは大きな変更後に検証を実行:

```markdown
メンタルチェックポイントを設定:
- 各関数を完了した後
- コンポーネントを終えた後
- 次のタスクに移る前

実行: /verify
```

## ベストプラクティス

1. **コミット前に検証** - すべてのチェックがパスすることを確認
2. **CI/CD で自動化** - プルリクエストで検証を自動実行
3. **カバレッジ目標** - 最低80%、重要コードは100%
4. **型チェック** - strict モードを使用
5. **セキュリティ** - シークレットと console.log をチェック
6. **差分レビュー** - すべての変更を確認

## フックとの統合

このスキルは PostToolUse フックを補完しますが、より深い検証を提供します。
フックは即座に問題をキャッチ; このスキルは包括的なレビューを提供します。

---

**覚えておくこと**: 検証ループは品質を保証する安全網です。すべてのチェックがパスするまでコードをコミットしないでください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
