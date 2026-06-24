---
name: review
description: Run code review against design documents, checking acceptance criteria, security, performance, and test coverage. Use when reviewing implementation quality or running /review. Use when this capability is needed.
metadata:
  author: ai-driven-school
---

# /review スキル

実装コードを設計書と照合し、品質チェックを行います。

## 使用方法

```
/review
/review auth
/review src/components/
/review --strict
```

## レビュー観点

```
[1] 受入条件チェック
    └─ 要件定義の受入条件を全て満たしているか

[2] 設計整合性
    └─ 画面設計・API設計との整合性

[3] コード品質
    ├─ TypeScript型安全性
    ├─ エラーハンドリング
    ├─ パフォーマンス
    └─ セキュリティ

[4] テストカバレッジ
    └─ 受入条件に対応するテストの有無
```

## 出力テンプレート

`docs/reviews/{feature}.md` に出力:

```markdown
# コードレビュー: {機能名}

## サマリー

| 項目 | 結果 |
|------|------|
| 受入条件 | {N}/{M} クリア |
| テストカバレッジ | {X}% |
| 改善提案 | {N}件 |
| ブロッカー | {N}件 |

## 判定: {✅ PASS / ⚠️ CONDITIONAL / ❌ FAIL}

---

## 受入条件チェック

### 機能要件

- [x] {条件1} → `src/app/login/page.tsx:15`
- [x] {条件2} → `src/lib/auth.ts:42`
- [ ] {条件3} → **未実装**

### UI/UX要件

- [x] レスポンシブデザイン
- [ ] キーボードナビゲーション → **要対応**

---

## セキュリティチェック

| チェック項目 | 結果 | 該当箇所 |
|-------------|:----:|---------|
| XSS対策 | ✅ | - |
| CSRF対策 | ✅ | `src/lib/csrf.ts` |
| SQLインジェクション | ✅ | Prisma ORM使用 |
| 認証バイパス | ⚠️ | `src/middleware.ts:12` |

---

## 改善提案

### 🔴 High Priority（ブロッカー）

#### 1. {問題タイトル}

**問題**: {問題の説明}

**該当箇所**: `{ファイル}:{行}`

```typescript
// Before
{問題のコード}

// After（推奨）
{改善後のコード}
```

### 🟡 Medium Priority

#### 2. {問題タイトル}

**問題**: {問題の説明}
**提案**: {改善提案}

### 🟢 Low Priority（推奨）

#### 3. {問題タイトル}

**提案**: {改善提案}

---

## テスト結果

```
Tests: {passed} passed, {failed} failed
Coverage: {X}%
```

| テストケース | 結果 |
|-------------|:----:|
| {テスト1} | ✅ |
| {テスト2} | ✅ |
| {テスト3} | ❌ |

---

## 結論

{総合評価とデプロイ可否の判断}
```

## 出力例

```
> /review auth

🔍 コードレビューを実行中... (Claude)

設計書を読み込み中...
  ✓ docs/requirements/auth.md
  ✓ docs/specs/auth.md

実装コードを解析中...
  ✓ src/app/login/page.tsx
  ✓ src/app/api/auth/login/route.ts
  ✓ src/components/auth/LoginForm.tsx
  ✓ src/lib/auth.ts

テスト結果を確認中...
  ✓ tests/auth.spec.ts (6 passed)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📄 docs/reviews/auth.md を作成しました
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

# コードレビュー: ユーザー認証

## サマリー
| 項目 | 結果 |
|------|------|
| 受入条件 | 5/5 クリア ✅ |
| テストカバレッジ | 85% |
| 改善提案 | 2件 |
| ブロッカー | 0件 |

## 判定: ✅ PASS

### 改善提案

🟡 Medium: パスワード強度チェックの追加を推奨
🟢 Low: aria-labelの追加でアクセシビリティ向上

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

デプロイを続行しますか？ [Y/n]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ai-driven-school) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
