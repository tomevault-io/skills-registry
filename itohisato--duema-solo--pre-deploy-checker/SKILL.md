---
name: pre-deploy-checker
description: | Use when this capability is needed.
metadata:
  author: itohisato
---

# デプロイ前チェックスキル

## 実行トリガー
- ユーザーが `/pre-deploy-checker` を実行
- 「デプロイ前チェック」「本番リリース準備」と言われた時

## チェック項目一覧

### 1. ビルド検証 [必須]
```bash
echo "🔨 ビルドチェック開始..."

# TypeScript型チェック
echo "  → TypeScript型チェック"
npx tsc --noEmit
TSC_EXIT=$?

# プロダクションビルド
echo "  → プロダクションビルド"
npm run build
BUILD_EXIT=$?

# ビルド成果物のサイズ確認
echo "  → ビルドサイズ確認"
if [ -d ".next" ]; then
  du -sh .next/
elif [ -d "dist" ]; then
  du -sh dist/
fi

if [ $TSC_EXIT -eq 0 ] && [ $BUILD_EXIT -eq 0 ]; then
  echo "✅ ビルドチェック: PASS"
else
  echo "❌ ビルドチェック: FAIL"
fi
```

### 2. テスト実行 [必須]
```bash
echo "🧪 テストチェック開始..."

# ユニットテスト
echo "  → ユニットテスト実行"
npm run test -- --coverage --passWithNoTests
UNIT_EXIT=$?

# E2Eテスト（存在する場合）
echo "  → E2Eテスト確認"
if [ -f "playwright.config.ts" ] || [ -f "playwright.config.js" ]; then
  npx playwright test
  E2E_EXIT=$?
else
  echo "  → E2Eテストなし（スキップ）"
  E2E_EXIT=0
fi

if [ $UNIT_EXIT -eq 0 ] && [ $E2E_EXIT -eq 0 ]; then
  echo "✅ テストチェック: PASS"
else
  echo "❌ テストチェック: FAIL"
fi
```

### 3. リンター・フォーマッター [必須]
```bash
echo "📝 リンターチェック開始..."

# ESLint
echo "  → ESLint実行"
npx eslint . --max-warnings 0
ESLINT_EXIT=$?

# Prettier（チェックのみ）
echo "  → Prettierチェック"
npx prettier --check "src/**/*.{ts,tsx,js,jsx}" 2>/dev/null || true
PRETTIER_EXIT=$?

if [ $ESLINT_EXIT -eq 0 ]; then
  echo "✅ リンターチェック: PASS"
else
  echo "⚠️ リンターチェック: WARNING (警告あり)"
fi
```

### 4. セキュリティ監査 [必須]
```bash
echo "🔒 セキュリティチェック開始..."

# npm依存関係の脆弱性
echo "  → npm audit実行"
npm audit --audit-level=high
AUDIT_EXIT=$?

# 機密情報の混入チェック
echo "  → 機密情報チェック"
SECRETS_FOUND=0

# ハードコードされたパスワード
if grep -rn "password\s*=\s*['\"][^'\"]*['\"]" src/ --include="*.ts" --include="*.tsx" 2>/dev/null; then
  echo "  ⚠️ ハードコードされたパスワードを検出"
  SECRETS_FOUND=1
fi

# APIキーの混入
if grep -rn "api_key\s*=\s*['\"]" src/ --include="*.ts" --include="*.tsx" 2>/dev/null; then
  echo "  ⚠️ ハードコードされたAPIキーを検出"
  SECRETS_FOUND=1
fi

# 公開環境変数に機密情報
if grep -rn "NEXT_PUBLIC_.*SECRET\|NEXT_PUBLIC_.*KEY\|NEXT_PUBLIC_.*PASSWORD" src/ --include="*.ts" --include="*.tsx" 2>/dev/null; then
  echo "  ⚠️ 公開環境変数に機密情報の可能性"
  SECRETS_FOUND=1
fi

if [ $AUDIT_EXIT -eq 0 ] && [ $SECRETS_FOUND -eq 0 ]; then
  echo "✅ セキュリティチェック: PASS"
else
  echo "❌ セキュリティチェック: FAIL"
fi
```

### 5. 環境変数確認 [必須]
```bash
echo "🔧 環境変数チェック開始..."

echo "  → 環境ファイル存在確認"
[ -f ".env.local" ] && echo "    .env.local: ✅ 存在" || echo "    .env.local: ⚠️ 不在"
[ -f ".env.production" ] && echo "    .env.production: ✅ 存在" || echo "    .env.production: ⚠️ 不在"
[ -f ".env.example" ] && echo "    .env.example: ✅ 存在" || echo "    .env.example: ⚠️ 不在"

# 必須環境変数の確認（.env.exampleから取得）
if [ -f ".env.example" ]; then
  echo "  → 必須環境変数チェック"
  while IFS= read -r line; do
    if [[ $line =~ ^[A-Z_]+= ]]; then
      VAR_NAME=$(echo "$line" | cut -d'=' -f1)
      if [ -z "${!VAR_NAME}" ]; then
        echo "    ⚠️ $VAR_NAME: 未設定"
      fi
    fi
  done < .env.example
fi

echo "✅ 環境変数チェック: 完了（手動確認推奨）"
```

### 6. パフォーマンス確認 [推奨]
```bash
echo "⚡ パフォーマンスチェック開始..."

# バンドルサイズ分析（Next.js）
echo "  → バンドルサイズ確認"
if [ -f ".next/BUILD_ID" ]; then
  # First Load JSサイズの確認
  find .next/static/chunks -name "*.js" -type f -exec du -h {} \; | sort -rh | head -10
fi

# 大きすぎるファイルの検出
echo "  → 大きなファイルの検出（100KB以上）"
find src -type f \( -name "*.ts" -o -name "*.tsx" \) -size +100k -exec ls -lh {} \;

echo "✅ パフォーマンスチェック: 完了"
```

### 7. Git状態確認 [推奨]
```bash
echo "📦 Git状態チェック開始..."

# 未コミットの変更
echo "  → 未コミット変更確認"
if [ -n "$(git status --porcelain)" ]; then
  echo "  ⚠️ 未コミットの変更があります:"
  git status --short
else
  echo "  ✅ 作業ツリーはクリーンです"
fi

# ブランチ確認
echo "  → ブランチ確認"
CURRENT_BRANCH=$(git branch --show-current)
echo "    現在のブランチ: $CURRENT_BRANCH"

# リモートとの差分
echo "  → リモートとの差分"
git fetch origin 2>/dev/null
AHEAD=$(git rev-list --count origin/$CURRENT_BRANCH..HEAD 2>/dev/null || echo "0")
BEHIND=$(git rev-list --count HEAD..origin/$CURRENT_BRANCH 2>/dev/null || echo "0")
echo "    ローカル: $AHEAD commits ahead, $BEHIND commits behind"

echo "✅ Git状態チェック: 完了"
```

## 出力フォーマット

```markdown
## 🚀 デプロイ前チェック結果

### 総合判定: ✅ PASS / ❌ FAIL

| # | チェック項目 | 結果 | 詳細 |
|---|-------------|------|------|
| 1 | ビルド | ✅ | 成功 (45s) |
| 2 | 型チェック | ✅ | エラーなし |
| 3 | ユニットテスト | ✅ | 42/42 passed, Coverage: 85% |
| 4 | E2Eテスト | ✅ | 12/12 passed |
| 5 | ESLint | ⚠️ | 警告 3件（エラーなし） |
| 6 | セキュリティ | ✅ | 脆弱性なし |
| 7 | 環境変数 | ✅ | 設定済み |
| 8 | Git状態 | ✅ | クリーン |

### ⚠️ 警告事項
- ESLint警告 3件（修正推奨）
  - src/components/Button.tsx:15 - no-unused-vars
  - ...

### 📋 デプロイ前チェックリスト
- [ ] CHANGELOGは更新されていますか？
- [ ] バージョン番号は更新されていますか？（package.json）
- [ ] 関係者への通知は完了していますか？
- [ ] ロールバック手順は確認済みですか？
- [ ] 監視ダッシュボードは準備できていますか？

### 🎯 次のステップ
すべてのチェックがPASSした場合:
\`\`\`bash
git tag v1.x.x
git push origin main --tags
npm run deploy  # または vercel --prod
\`\`\`
```

## デプロイ承認フロー

### 全チェックPASSの場合
1. デプロイ手順を提示
2. ユーザーの最終確認を求める
3. 確認後、デプロイコマンドを実行（ユーザー承認必須）

### 1つでもFAILがある場合
1. 失敗項目を明示
2. 具体的な修正方法を提示
3. 「修正後、再度 /pre-deploy-checker を実行してください」と案内
4. **デプロイは実行しない**

## 緊急時の対応

### ホットフィックスの場合
```bash
# 最小限のチェックのみ実行
npm run build && npm run test -- --testPathPattern="affected" && npm audit
```

### ロールバック手順
```bash
# 直前のバージョンに戻す
git revert HEAD
git push origin main

# または特定のタグに戻す
git checkout v1.x.x
npm run deploy
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itohisato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
