---
name: pr
description: Pull Request作成を自動化します。lint→test→commit→PR作成まで。 Use when this capability is needed.
metadata:
  author: ta-061
---

# Pull Request作成スキル

コード変更をPull Requestとして提出するまでのフローを自動化します。

## 実行フロー

### Step 1: 変更内容の確認

```bash
# 変更されたファイル
git status

# 変更差分
git diff

# 未追跡ファイル
git ls-files --others --exclude-standard
```

### Step 2: コード品質チェック

```bash
# ruff でリントチェック
ruff check src/ta_inspector/

# 問題があれば自動修正
ruff check src/ta_inspector/ --fix

# mypy で型チェック
mypy src/ta_inspector/
```

問題があれば修正してから次へ。

### Step 3: テスト実行

```bash
# 全テスト実行
pytest tests/ -v

# カバレッジ確認（オプション）
pytest tests/ --cov=ta_inspector --cov-report=term-missing
```

テストが失敗する場合は `/fix` スキルで修正。

### Step 4: コミット作成

```bash
# 変更をステージング
git add <specific_files>

# コミット（Co-Authored-By付き）
git commit -m "$(cat <<'EOF'
<type>: <description>

<詳細な説明（任意）>

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
EOF
)"
```

**コミットタイプ:**
- `feat`: 新機能
- `fix`: バグ修正
- `refactor`: リファクタリング
- `test`: テスト追加・修正
- `docs`: ドキュメント
- `chore`: その他

### Step 5: リモートにプッシュ

```bash
# 現在のブランチをプッシュ
git push -u origin <branch_name>
```

### Step 6: PR作成

```bash
gh pr create --title "<PRタイトル>" --body "$(cat <<'EOF'
## Summary
- <変更点1>
- <変更点2>

## Test plan
- [ ] 全テストがパス
- [ ] lint/mypyがパス
- [ ] 手動テスト（必要な場合）

🤖 Generated with [Claude Code](https://claude.com/claude-code)
EOF
)"
```

### Step 7: 結果確認

```bash
# PR URLを表示
gh pr view --web
```

## PR作成のガイドライン

### タイトル
```
<type>: <簡潔な説明>
```
例:
- `feat: Add Linksys firmware collector`
- `fix: Handle malformed OPTEE headers`
- `refactor: Extract common parsing logic`

### 本文
1. **Summary**: 変更内容を箇条書きで
2. **Test plan**: テスト方法をチェックリストで
3. **関連Issue**: あれば `Closes #123`

## 使用例

```
/pr
```

変更内容を確認し、lint→test→commit→PR作成まで実行します。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ta-061) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
