---
name: verification-loop
description: Claude Codeセッションのための包括的な検証システム。変更後にビルド・テスト・リントを実行し、品質を保証する。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# 検証ループスキル

Claude Codeセッションのための包括的な検証システム。

## いつ使用するか

このスキルを呼び出す:
- 機能または重要なコード変更を完了した後
- PRを作成する前
- 品質ゲートがパスすることを確認したいとき
- リファクタリング後

## 検証フェーズ

### フェーズ1: テスト実行
```bash
# テストがパスするかチェック
uv run pytest --cov 2>&1 | tail -20
```

テストが失敗したら、続行前に停止して修正します。

### フェーズ2: 型チェック
```bash
# Pythonプロジェクト
uv run mypy . 2>&1 | head -30
```

すべての型エラーを報告します。続行前に重要なものを修正します。

### フェーズ3: Lintチェック
```bash
# Python
uv run ruff check . 2>&1 | head -30
```

### フェーズ4: フォーマットチェック
```bash
# フォーマット差分をチェック
uv run ruff format --check . 2>&1 | tail -50
```

レポート:
- 総テスト数: X
- 成功: X
- 失敗: X
- カバレッジ: X%

### フェーズ5: セキュリティスキャン
```bash
# シークレットをチェック
grep -rn "sk-" --include="*.py" . 2>/dev/null | head -10
grep -rn "api_key" --include="*.py" . 2>/dev/null | head -10

# print文をチェック
grep -rn "print(" --include="*.py" src/ 2>/dev/null | head -10
```

### フェーズ6: Diffレビュー
```bash
# 変更内容を表示
git diff --stat
git diff HEAD~1 --name-only
```

各変更ファイルをレビューする対象:
- 意図しない変更
- 欠けているエラー処理
- 潜在的なエッジケース

## 出力フォーマット

すべてのフェーズを実行した後、検証レポートを作成:

```
検証レポート
==================

テスト:     [PASS/FAIL] (X/Y成功、Z%カバレッジ)
型:         [PASS/FAIL] (Xエラー)
Lint:      [PASS/FAIL] (X警告)
フォーマット: [PASS/FAIL]
セキュリティ: [PASS/FAIL] (X問題)
Diff:      [Xファイル変更]

全体:       [準備完了/未完了] PR用

修正すべき問題:
1. ...
2. ...
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

## フックとの統合

このスキルはPostToolUseフックを補完しますが、より深い検証を提供します。
フックは即座に問題をキャッチ; このスキルは包括的なレビューを提供します。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
