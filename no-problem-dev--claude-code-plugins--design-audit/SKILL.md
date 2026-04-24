---
name: design-audit
description: SwiftUI ViewのDesign System準拠性を監査する。既存UIのレビュー、デザインチェック時に使用。「デザインレビュー」「UIチェック」「design audit」「デザイン監査」「UI監査」「デザインチェック」「トークン確認」などのキーワードで自動適用。 Use when this capability is needed.
metadata:
  author: no-problem-dev
---

# デザイン監査スキル

既存の SwiftUI View が Swift Design System に準拠しているかを体系的に監査する。

---

## 監査手順

1. **対象ファイルを読み込む**: View のソースコードを全文読む
2. **6カテゴリで検査**: 下記の監査カテゴリを順番に確認
3. **重大度を分類**: Critical / Warning / Recommendation に分類
4. **レポートを出力**: 構造化された監査レポートを生成
5. **修正提案**: 自動修正可能な箇所を提示

---

## 監査カテゴリ

### 1. トークン準拠性

ハードコードされた値がないか検出する。

| 検出対象 | 正しい使用 | 重大度 |
|---------|-----------|--------|
| `Color.red`, `.red`, `Color(hex:)` など | `colors.error`, `colors.primary` 等 | Critical |
| `.font(.system(size:))` | `.typography(.bodyMedium)` 等 | Critical |
| `.padding(16)`, `spacing: 12` 等の数値直指定 | `spacing.lg`, `spacing.md` 等 | Warning |
| `cornerRadius: 8` 等の数値直指定 | `radius.lg` 等 | Warning |
| `.animation(.easeOut(duration:))` | `.animate(motion.tap, value:)` | Warning |

**ColorPalette プロパティ一覧**（これ以外の Color を使っていたら違反）:
- primary, onPrimary, primaryContainer, onPrimaryContainer
- secondary, onSecondary, secondaryContainer, onSecondaryContainer
- tertiary, onTertiary
- background, onBackground
- surface, onSurface, surfaceVariant, onSurfaceVariant
- error, onError, errorContainer, onErrorContainer
- warning, onWarning, success, onSuccess, info, onInfo
- outline, outlineVariant

**SpacingScale 値一覧**: none(0), xxs(2), xs(4), sm(8), md(12), lg(16), xl(24), xxl(32), xxxl(48), xxxxl(64)

**Typography 一覧**: displayLarge(57), displayMedium(45), displaySmall(36), headlineLarge(32), headlineMedium(28), headlineSmall(24), titleLarge(22), titleMedium(16), titleSmall(14), bodyLarge(16), bodyMedium(14), bodySmall(12), labelLarge(14), labelMedium(12), labelSmall(11)

### 2. コンポーネント使用

カスタム実装が既存コンポーネントで代替できないか確認する。

| パターン | 代替コンポーネント |
|---------|------------------|
| 手動の角丸+影+背景カード | `Card(elevation:)` |
| カスタムボタンスタイル | `.buttonStyle(.primary/.secondary/.tertiary)` + `.buttonSize()` |
| タイトル付きカードセクション | `SectionCard(title:elevation:)` |
| カスタムアイコンバッジ | `IconBadge(systemName:size:)` |
| カスタム進捗表示 | `ProgressBar` |
| カスタムテキスト入力 | `DSTextField` |
| カスタム通知バナー | `Snackbar` |
| タグ/フィルター UI | `Chip` + `.chipStyle(.filled/.outlined)` |
| フローティングボタン | `FloatingActionButton` |
| グリッドレイアウト | `AspectGrid` |

### 3. アクセシビリティ

| チェック項目 | 基準 | 重大度 |
|------------|------|--------|
| `accessibilityLabel` | インタラクティブ要素に必須 | Critical |
| タップターゲット | 最小 44x44pt | Critical |
| Dynamic Type | `.typography()` 使用で自動対応 | Warning |
| reduce motion | `.animate()` 使用で自動対応 | Warning |
| コントラスト比 | `on*` カラーと `*` カラーのペア使用 | Warning |

### 4. テーマ対応

| チェック項目 | 基準 | 重大度 |
|------------|------|--------|
| ThemeProvider | ルートに `.theme(themeProvider)` | Critical |
| Environment 宣言 | `@Environment(\.colorPalette)` 等 | Critical |
| ダークモード | `colors.surface` / `colors.onSurface` 等のペア使用 | Warning |
| テーマ切替 | 複数テーマで表示が崩れないか | Recommendation |

### 5. Preview カバレッジ

| 必須 Preview | 説明 | 重大度 |
|-------------|------|--------|
| Default | 通常状態 | Warning |
| Dark Mode | `.theme(ThemeProvider(initialMode: .dark))` | Warning |
| Large Text | `.environment(\.sizeCategory, .accessibilityExtraLarge)` | Recommendation |
| Compact | 最小コンテンツ | Recommendation |
| Edge Case | 長文、大量データ | Recommendation |
| `.theme(ThemeProvider())` | すべての Preview に必須 | Critical |

### 6. 構造品質

| チェック項目 | 基準 | 重大度 |
|------------|------|--------|
| View の body 行数 | 40行以下推奨 | Recommendation |
| ViewModifier 抽出 | 繰り返しの修飾は ViewModifier に | Recommendation |
| サブView 分割 | 独立した論理単位は別 View に | Recommendation |
| `import DesignSystem` | 必須 | Critical |

---

## 重大度レベル

| レベル | 意味 | 対応 |
|-------|------|------|
| Critical | Design System 違反。修正必須 | 即座に修正 |
| Warning | 推奨パターンからの逸脱 | 早めに修正 |
| Recommendation | 改善提案 | 次回以降に検討 |

---

## 監査レポートフォーマット

```markdown
# Design System 監査レポート

**対象**: `FileName.swift`
**日時**: YYYY-MM-DD
**スコア**: X / 6 カテゴリ合格

## サマリー

| カテゴリ | 結果 | Critical | Warning | Recommendation |
|---------|------|----------|---------|----------------|
| トークン準拠性 | ✅/❌ | 0 | 0 | 0 |
| コンポーネント使用 | ✅/❌ | 0 | 0 | 0 |
| アクセシビリティ | ✅/❌ | 0 | 0 | 0 |
| テーマ対応 | ✅/❌ | 0 | 0 | 0 |
| Preview カバレッジ | ✅/❌ | 0 | 0 | 0 |
| 構造品質 | ✅/❌ | 0 | 0 | 0 |

## 詳細

### 🔴 Critical

1. **[トークン準拠性]** L15: `.foregroundColor(.red)` → `.foregroundStyle(colors.error)`
2. **[テーマ対応]** L3: `@Environment` 宣言なし → `@Environment(\.colorPalette) private var colors` を追加

### 🟡 Warning

1. **[トークン準拠性]** L22: `.padding(16)` → `.padding(spacing.lg)`

### 🟢 Recommendation

1. **[構造品質]** body が 55 行 → サブView に分割を検討
```

---

## よくある違反と自動修正

### ハードコードカラー → セマンティックカラー

```swift
// Before
.foregroundColor(.red)
.background(Color.white)
.foregroundColor(.gray)

// After
.foregroundStyle(colors.error)
.background(colors.surface)
.foregroundStyle(colors.onSurfaceVariant)
```

### 直値スペーシング → トークン

```swift
// Before
.padding(16)
VStack(spacing: 8) { ... }

// After
.padding(spacing.lg)
VStack(spacing: spacing.sm) { ... }
```

### 直接フォント → Typography

```swift
// Before
.font(.system(size: 14, weight: .medium))
.font(.title)

// After
.typography(.bodyMedium)
.typography(.titleLarge)
```

### 直接アニメーション → Motion トークン

```swift
// Before
.animation(.easeOut(duration: 0.1), value: isActive)

// After
.animate(motion.tap, value: isActive)
```

### カスタムカード → Card コンポーネント

```swift
// Before
content
    .padding(16)
    .background(Color.white)
    .clipShape(RoundedRectangle(cornerRadius: 12))
    .shadow(color: .black.opacity(0.1), radius: 4, y: 2)

// After
Card(elevation: .level2) {
    content
}
```

---

## 関連スキル

- **design-system-workflow**: トークン・コンポーネントの詳細リファレンス
- **component-gen**: 新規コンポーネント生成
- **design-diff**: 修正前後の視覚的差分
- **ios-build-workflow**: ビルド・テスト実行

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/no-problem-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
