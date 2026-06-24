---
name: verification-loop-python
description: Python プロジェクトの品質検証ループ。ruff、mypy、pytest を実行して問題を自動修正。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# Python 検証ループスキル

Python プロジェクトのための包括的な検証システム。

## いつ使用するか

このスキルを呼び出す:
- 機能または重要なコード変更を完了した後
- PRを作成する前
- 品質ゲートがパスすることを確認したいとき
- リファクタリング後

## 検証フェーズ

### フェーズ1: Ruff Lint チェック

```bash
# コードをチェック
ruff check . 2>&1 | head -30

# 自動修正を適用
ruff check --fix . 2>&1

# フォーマットをチェック
ruff format --check . 2>&1 | head -20
```

Lint エラーがあれば、続行前に修正します。

### フェーズ2: Ruff フォーマット

```bash
# フォーマットを適用
ruff format . 2>&1

# 変更を確認
git diff --stat
```

### フェーズ3: 型チェック (mypy)

```bash
# 型チェックを実行
mypy . 2>&1 | head -30

# 厳格モード（推奨）
mypy --strict src/ 2>&1 | head -30
```

すべての型エラーを報告します。続行前に重要なものを修正します。

### フェーズ4: テストスイート (pytest)

```bash
# カバレッジ付きでテストを実行
pytest --cov=src --cov-report=term-missing 2>&1 | tail -50

# カバレッジしきい値をチェック
pytest --cov=src --cov-fail-under=80 2>&1

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
grep -rn "sk-" --include="*.py" . 2>/dev/null | head -10
grep -rn "api_key" --include="*.py" . 2>/dev/null | head -10
grep -rn "password" --include="*.py" . 2>/dev/null | head -10

# print文をチェック（本番コードには使用しない）
grep -rn "print(" --include="*.py" src/ 2>/dev/null | head -10

# デバッグコードをチェック
grep -rn "import pdb" --include="*.py" . 2>/dev/null | head -10
grep -rn "breakpoint()" --include="*.py" . 2>/dev/null | head -10
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
- 型ヒントの欠落

## 自動修正フロー

```bash
# 1. Lint を実行して自動修正
ruff check --fix .

# 2. フォーマットを実行
ruff format .

# 3. 型チェック
mypy .

# 4. テストを実行
pytest --cov=src --cov-report=term-missing

# 5. すべてパスしたら完了
```

## 出力フォーマット

すべてのフェーズを実行した後、検証レポートを作成:

```
検証レポート (Python)
==================

Lint (ruff):   [PASS/FAIL] (Xエラー)
Format:        [PASS/FAIL]
型 (mypy):     [PASS/FAIL] (Xエラー)
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
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install ruff mypy pytest pytest-cov

      - name: Ruff check
        run: ruff check .

      - name: Ruff format
        run: ruff format --check .

      - name: Type check
        run: mypy .

      - name: Run tests
        run: pytest --cov=src --cov-fail-under=80
```

## pre-commit フック

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.1.6
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: local
    hooks:
      - id: mypy
        name: mypy
        entry: mypy
        language: system
        types: [python]
        args: [--strict]

      - id: pytest
        name: pytest
        entry: pytest
        language: system
        pass_filenames: false
        always_run: true
        args: [--cov=src, --cov-fail-under=80]
```

## 連続モード

長いセッションの場合、15分ごとまたは大きな変更後に検証を実行:

```markdown
メンタルチェックポイントを設定:
- 各関数を完了した後
- モジュールを終えた後
- 次のタスクに移る前

実行: /verify
```

## ベストプラクティス

1. **コミット前に検証** - すべてのチェックがパスすることを確認
2. **CI/CD で自動化** - プルリクエストで検証を自動実行
3. **カバレッジ目標** - 最低80%、重要コードは100%
4. **型チェック** - mypy --strict を使用
5. **セキュリティ** - シークレットと print 文をチェック
6. **差分レビュー** - すべての変更を確認

---

**覚えておくこと**: 検証ループは品質を保証する安全網です。すべてのチェックがパスするまでコードをコミットしないでください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
