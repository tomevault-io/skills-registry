---
name: translation-checker
description: Reviews translation quality, consistency, terminology, and style. Use when checking translations, reviewing UI text, verifying translation guidelines, or ensuring consistent terminology across languages. Use when this capability is needed.
metadata:
  author: rizumi
---

# Translation Checker

文言・翻訳の品質と一貫性を評価するための汎用スキルです。

## このスキルでできること

- **一貫性チェック**: 同じ概念が統一された用語で表現されているか
- **用語統一チェック**: 技術用語やブランド名の表記揺れ検出
- **文体チェック**: トーンやスタイルの一貫性確認
- **翻訳漏れ検出**: 未翻訳の文言を特定
- **プレースホルダー整合性**: 変数や置換パターンの正確性確認
- **文字数チェック**: UI制約に対する妥当性確認
- **文化的配慮**: 各言語圏での適切性確認
- **自然さチェック**: 機械翻訳的な不自然さの検出

## クイックスタート

### ステップ1: 翻訳ファイルを特定

```bash
# iOS
find . -name "*.xcstrings" -o -name "*.strings"

# Android
find . -name "strings.xml" -path "*/values*/strings.xml"

# Web (JSON)
find . -name "*.json" \( -path "*/i18n/*" -o -path "*/locales/*" \)

# その他のプラットフォーム → PLATFORMS.md参照
```

### ステップ2: プロジェクトガイドラインを確認

以下のファイルが存在するか確認し、存在する場合は参照：
- `TRANSLATION_GUIDELINES.md`
- `STYLE_GUIDE.md`
- `TERMINOLOGY.md`

### ステップ3: チェックを実行

8つの主要チェック項目：
1. 一貫性チェック
2. 用語統一チェック
3. 文体・トーンチェック
4. プレースホルダー・変数チェック
5. 翻訳漏れチェック
6. 文字数・長さチェック
7. 文化的配慮チェック
8. 自然さ・品質チェック

詳細は **[CHECKLIST.md](CHECKLIST.md)** を参照

### ステップ4: レポート生成

```markdown
## 文言チェック結果

### 概要
- チェックしたファイル数: X個
- 対象言語: [言語リスト]
- 検出された問題: Y件（重大: A件、軽微: B件）

### 問題の詳細
[カテゴリ別に問題をリスト]

### 推奨事項
[改善案]
```

## 主要なチェック項目

### 一貫性チェック

同じ概念が統一された用語で表現されているか確認：
- 同じ英語キー/原文が異なる訳語になっていないか
- 類似機能で異なる表現が混在していないか
- アクション表現が統一されているか

### 用語統一チェック

技術用語やブランド名が統一されているか：
- ブランド名の表記（例：Face ID、App Store）
- カタカナ表記の統一（例：「データ」vs「データー」）
- 外来語の表記（例：「キャンセル」vs「取消」）

詳細なガイドラインは **[TERMINOLOGY.md](TERMINOLOGY.md)** を参照

### プレースホルダーチェック

動的に挿入される値のパターンが正しく維持されているか：

| プラットフォーム | パターン例 |
|----------------|-----------|
| iOS | `%@`, `%lld`, `%.0f` |
| Android | `%s`, `%1$s`, `%2$d` |
| Web | `{name}`, `{{var}}`, `${var}` |
| ICU | `{count, plural, one {} other {}}` |

### 文体・トーンチェック

**日本語の場合：**
- ボタンラベル: 名詞形・動詞形（例：「完了」「キャンセル」）
- 指示メッセージ: 丁寧な命令形（例：「～してください」）
- 状態メッセージ: です・ます調
- エラーメッセージ: 丁寧な説明
- 確認ダイアログ: 疑問形（例：「～しますか？」）

**韓国語の場合：**
- 존댓말（丁寧語）と반말（非丁寧語）の統一
- 격식체（格式体）と비격식체（非格式体）の適切な使い分け

**英語の場合：**
- フォーマル vs カジュアル
- 命令形 vs 依頼形

詳細は **[STYLE_GUIDE.md](STYLE_GUIDE.md)** を参照

## 対応プラットフォーム

- iOS (`.xcstrings`, `.strings`, `.stringsdict`)
- Android (`strings.xml`)
- Web (`.json`, `.yml`)
- その他（`.po`, `.properties`, `.arb`など）

プラットフォーム別の詳細は **[PLATFORMS.md](PLATFORMS.md)** を参照

## 対応言語

日本語、韓国語、中国語（簡体字・繁体字）、英語、その他主要言語

## 使用例

```
# 例1: 特定のファイルをチェック
このファイルの日本語翻訳をチェックして

# 例2: プロジェクト全体をチェック
プロジェクト全体の翻訳をレビューして

# 例3: 用語統一チェック
「キャンセル」と「取消」が混在していないか確認して

# 例4: 自然さチェック
この文言が日本語として自然か確認して
```

## ベストプラクティス

### 翻訳の一般原則

1. **一貫性を最優先**: 同じ概念には同じ用語
2. **文脈を考慮**: UIのどこで使われるかを理解
3. **簡潔さと明確さ**: 特にモバイルUIでは簡潔に
4. **文化的配慮**: 各言語圏の文化や慣習を尊重
5. **ネイティブチェック**: 可能であればネイティブスピーカーのレビューを

### UI文言の原則

1. **ユーザー中心**: ユーザーが理解しやすい言葉
2. **アクション指向**: ボタンは何が起こるかを明確に
3. **肯定的表現**: 否定形より肯定形を優先
4. **エラーは丁寧に**: エラーメッセージは特に丁寧に
5. **一貫したトーン**: アプリ全体で統一

詳細は **[BEST_PRACTICES.md](BEST_PRACTICES.md)** を参照

## 参照ドキュメント

- **[CHECKLIST.md](CHECKLIST.md)** - 詳細なチェック項目と手順
- **[PLATFORMS.md](PLATFORMS.md)** - プラットフォーム別のガイド
- **[TERMINOLOGY.md](TERMINOLOGY.md)** - 用語集と統一ガイドライン
- **[STYLE_GUIDE.md](STYLE_GUIDE.md)** - 文体・トーンのガイドライン
- **[BEST_PRACTICES.md](BEST_PRACTICES.md)** - ベストプラクティス集
- **[EXAMPLES.md](EXAMPLES.md)** - 実践例とパターン

## トラブルシューティング

### 翻訳ファイルが見つからない場合

```bash
find . -type f \( \
  -name "*.xcstrings" -o \
  -name "*.strings" -o \
  -name "strings.xml" -o \
  -name "*i18n*.json" -o \
  -name "*locale*.json" -o \
  -name "*.po" \
\) | grep -v node_modules | grep -v build
```

### ファイル形式が不明な場合

ファイルの内容を読み取って形式を推定します。

### 大量のファイルがある場合

優先度の高いファイル（ユーザーに直接表示されるUI文言）から順にチェックします。

## 注意事項

- 文言の品質評価を提供しますが、最終判断はネイティブスピーカーに委ねるべきです
- 機械的なチェックでは検出できない微妙なニュアンスの問題もあります
- プロジェクト固有のガイドラインがある場合、それを最優先します
- 文化的配慮や法的要件については、専門家のレビューを推奨します

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rizumi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
