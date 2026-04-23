---
name: zenn-publishing
description: Zennでの技術記事・書籍公開の完全ガイド。カバー画像仕様、価格設定、config.yaml設定、GitHubデプロイ、よくあるエラーと対処法まで、Zenn公開の全てをカバー。 Use when this capability is needed.
metadata:
  author: gaku52
---

# Zenn Publishing Skill

## 📋 目次

1. [概要](#概要)
2. [いつ使うか](#いつ使うか)
3. [Zenn仕様](#zenn仕様)
4. [書籍公開手順](#書籍公開手順)
5. [記事公開手順](#記事公開手順)
6. [よくあるエラーと対処法](#よくあるエラーと対処法)
7. [実践例](#実践例)
8. [アンチパターン](#アンチパターン)
9. [Agent連携](#agent連携)

---

## 概要

このSkillは、Zennでの技術コンテンツ公開をサポートします：

- **技術記事** - Markdown形式の記事公開
- **技術書籍** - チャプター構成の書籍公開
- **カバー画像** - 適切なサイズ・形式での画像準備
- **価格設定** - 適正価格の決定
- **デプロイ管理** - GitHub連携での自動公開

### 原則

1. **仕様を守る** - Zennの仕様に厳密に従う
2. **品質を保つ** - 読者に価値を提供するコンテンツ
3. **自動化する** - GitHub連携で効率的な公開フロー
4. **エラーを防ぐ** - よくある失敗を事前に回避

## 📚 公式ドキュメント・参考リソース

**このガイドで学べること**: カバー画像仕様、価格設定、config.yaml設定、デプロイエラー対処法
**公式で確認すべきこと**: 最新の仕様変更、新機能、料金体系の変更

### 主要な公式ドキュメント

- **[Zenn公式ドキュメント](https://zenn.dev/zenn)** - Zennの使い方全般
  - [Zenn CLIで記事・本を管理する方法](https://zenn.dev/zenn/articles/zenn-cli-guide)
  - [本を執筆する](https://zenn.dev/zenn/books/how-to-create-book)
  - [本を有料で販売する](https://zenn.dev/zenn/books/how-to-create-book/viewer/set-price)

- **[GitHubリポジトリでZennのコンテンツを管理する](https://zenn.dev/zenn/articles/connect-to-github)** - GitHub連携

- **[Zenn FAQ](https://zenn.dev/faq)** - よくある質問
  - [本の販売や有料バッジの受取についてのよくある質問](https://zenn.dev/faq/sales)

### 関連リソース

- **[Zenn CLI](https://github.com/zenn-dev/zenn-editor)** - ローカル執筆環境
- **[Zenn Community](https://zenn.dev/topics/zenn)** - Zennに関する記事

---

## いつ使うか

### 🎯 必須のタイミング

- [ ] 新しい技術書籍を公開する時
- [ ] 書籍のカバー画像を作成・更新する時
- [ ] 価格設定を決定・変更する時
- [ ] デプロイエラーが発生した時

### 🔄 定期的に

- [ ] 書籍の内容を更新した時
- [ ] 新しいチャプターを追加した時
- [ ] 価格改定を検討する時

---

## Zenn仕様

### 1. 書籍（Books）の仕様

#### ディレクトリ構造

```
books/
└── book-slug/
    ├── config.yaml       # 書籍設定ファイル
    ├── cover.png         # カバー画像（500x700px、1MB以下）
    ├── chapter1.md
    ├── chapter2.md
    └── ...
```

#### カバー画像の仕様

**【重要】必ず守ること：**

| 項目 | 仕様 |
|------|------|
| サイズ | **500px × 700px（縦長）** |
| ファイルサイズ | **1MB以下（必須）** |
| フォーマット | PNG または JPG |
| ファイル名 | `cover.png` または `cover.jpg` |
| 配置場所 | `books/[book-slug]/cover.png` |

**よくある失敗：**
- ❌ 1280x670px（横長）を指定してしまう → **500x700pxが正解**
- ❌ 7MBの画像をそのまま使用 → **1MB以下に圧縮が必要**

#### config.yamlの仕様

```yaml
title: "書籍タイトル"
summary: "書籍の説明（140文字程度）"
topics: ["topic1", "topic2", "topic3"]  # 最大5個
published: true  # true=公開、false=非公開
price: 1200      # 200〜5000円、0円も可能
chapters:
  - chapter1     # .mdは不要
  - chapter2
  - chapter3
```

**価格設定のガイドライン：**

| ボリューム | 推奨価格帯 | 備考 |
|-----------|-----------|------|
| 5章以下 | 200〜500円 | 入門記事レベル |
| 6〜10章 | 500〜1,000円 | 標準的な技術書 |
| 11〜15章 | 1,000〜1,500円 | 充実した完全ガイド |
| 16章以上 | 1,500〜2,500円 | 大型技術書 |

**価格設定の例：**
- 14章・約24,000語 → **1,200円**（適正価格、高すぎない）
- 8章・約12,000語 → **800円**
- 20章・約40,000語 → **2,000円**

### 2. 記事（Articles）の仕様

#### ファイル構造

```
articles/
└── article-slug.md
```

#### フロントマター

```yaml
---
title: "記事タイトル"
emoji: "📘"
type: "tech"  # tech: 技術記事 / idea: アイデア
topics: ["nextjs", "react", "typescript"]
published: true
---

記事本文...
```

### 3. GitHub連携での公開フロー

```
1. ローカルで編集
   ↓
2. git commit & push
   ↓
3. Zennが自動でデプロイ（数分以内）
   ↓
4. デプロイ結果の確認（Zennのダッシュボード）
```

---

## 書籍公開手順

### Step 1: カバー画像の準備

```bash
# 画像サイズの確認
file cover.png
# 出力: PNG image data, 500 x 700

# ファイルサイズの確認
ls -lh cover.png
# 出力: -rw-r--r-- 1 user staff 628K cover.png  ← 1MB以下ならOK

# 画像が大きすぎる場合の圧縮（macOS）
sips -Z 700 cover.png --setProperty format png --out cover_compressed.png

# 圧縮後の画像で置き換え
mv cover_compressed.png books/[book-slug]/cover.png
```

**画像生成プロンプトの例（Gemini Pro等）：**

```
Create a professional technical book cover design for a vertical format book.

【Specifications】
- Dimensions: 500x700 pixels (vertical portrait format)
- Format: PNG, high resolution

【Design Requirements】
- Modern and professional technical book cover
- Theme: [技術スタック]
- Colors: [カラーパレット]

【Layout】
Top Section (30%): Book title
Middle Section (40%): Main visual
Bottom Section (30%): Subtitle and technology badge
```

### Step 2: config.yamlの設定

```yaml
title: "SwiftUI開発パターン完全ガイド 2026"
summary: "@State/@Binding/@Observable完全マスター、実測データと実践例で学ぶ完全ガイド。"
topics: ["swiftui", "swift", "ios"]
published: true
price: 1200
chapters:
  - 00-introduction
  - 01-state-management
  - 02-layout-patterns
```

**価格決定のチェックリスト：**

- [ ] チャプター数を確認
- [ ] 総文字数を確認（`find . -name "*.md" -exec wc -w {} + | tail -1`）
- [ ] 類似書籍の価格を調査
- [ ] 適正価格帯に設定（高すぎず、安すぎず）

### Step 3: デプロイ

```bash
# 変更をステージング
git add books/[book-slug]/cover.png
git add books/[book-slug]/config.yaml

# コミット
git commit -m "feat: Add cover and publish [book-name]"

# プッシュ
git push origin main

# 数分後、Zennで確認
# https://zenn.dev/[username]/books/[book-slug]
```

---

## よくあるエラーと対処法

### エラー1: カバー画像サイズ超過

```
エラーメッセージ：
デプロイが中断しました。カバー画像（books/xxx/cover.png）のサイズは1MB以下にしてください
```

**原因：**
画像ファイルサイズが1MBを超えている

**対処法：**

```bash
# 現在のサイズ確認
ls -lh books/[book-slug]/cover.png

# 圧縮（macOS）
sips -Z 700 books/[book-slug]/cover.png \
  --setProperty format png \
  --out books/[book-slug]/cover_compressed.png

# 置き換え
mv books/[book-slug]/cover_compressed.png books/[book-slug]/cover.png

# 再度サイズ確認（1MB以下であることを確認）
ls -lh books/[book-slug]/cover.png

# コミット・プッシュ
git add books/[book-slug]/cover.png
git commit -m "fix: Compress cover image to under 1MB"
git push
```

### エラー2: カバー画像が見つからない

```
エラーメッセージ：
books/xxx に本のカバー画像（cover.jpg もしくは cover.png）が見つかりませんでした
```

**原因：**
- カバー画像が存在しない
- ファイル名が間違っている（`Cover.png`、`cover.PNG`等）

**対処法：**

```bash
# 正しい場所に配置
cp /path/to/image.png books/[book-slug]/cover.png

# ファイル名を確認（小文字のcover.pngまたはcover.jpg）
ls books/[book-slug]/cover.*

# コミット・プッシュ
git add books/[book-slug]/cover.png
git commit -m "feat: Add cover image"
git push
```

### エラー3: チャプターがデプロイされない

```
エラーメッセージ：
本「xxx」のチャプター「chapter-name」は、config.yamlのchaptersに指定されていないためデプロイがスキップされました
```

**原因：**
`config.yaml`の`chapters`リストにチャプターが記載されていない

**対処法：**

```yaml
# config.yamlに追加
chapters:
  - 00-introduction
  - 01-chapter-one
  - 02-chapter-two
  - chapter-name  # ← 追加
```

### エラー4: 価格設定範囲外

**原因：**
価格が200円未満、または5,000円を超えている（0円は可能）

**対処法：**

```yaml
# config.yaml
price: 1200  # 200〜5000の範囲内、または0
```

---

## 実践例

### Example 1: 新規書籍の公開

```bash
# 1. カバー画像を生成（Gemini Pro等で500x700px）
# 2. ファイルサイズ確認
ls -lh cover.png
# 628K → OK

# 3. 配置
cp cover.png books/swiftui-patterns-complete-guide-2026/cover.png

# 4. config.yamlを作成
cat > books/swiftui-patterns-complete-guide-2026/config.yaml <<EOF
title: "SwiftUI開発パターン完全ガイド 2026"
summary: "@State/@Binding/@Observable完全マスター"
topics: ["swiftui", "swift", "ios"]
published: true
price: 1200
chapters:
  - 00-introduction
  - 01-state-management
EOF

# 5. デプロイ
git add books/swiftui-patterns-complete-guide-2026/
git commit -m "feat: Publish SwiftUI patterns guide with cover"
git push
```

### Example 2: 価格変更

```bash
# 1. config.yamlを編集
# price: 800 → price: 1200

# 2. デプロイ
git add books/[book-slug]/config.yaml
git commit -m "chore: Update book price to 1,200 yen"
git push
```

### Example 3: カバー画像の更新

```bash
# 1. 新しい画像を準備（500x700px、1MB以下）
sips -Z 700 new_cover.png --out cover_resized.png

# 2. サイズ確認
ls -lh cover_resized.png  # 1MB以下であることを確認

# 3. 置き換え
mv cover_resized.png books/[book-slug]/cover.png

# 4. デプロイ
git add books/[book-slug]/cover.png
git commit -m "feat: Update book cover image"
git push
```

---

## アンチパターン

### ❌ 1. 画像サイズを確認せずにデプロイ

```bash
# ❌ 悪い例
cp huge_image.png books/my-book/cover.png
git add books/my-book/cover.png
git commit -m "Add cover"
git push
# → デプロイエラー: ファイルサイズが1MBを超えています
```

**正しい方法：**

```bash
# ✅ 良い例
ls -lh huge_image.png  # サイズ確認
sips -Z 700 huge_image.png --out cover.png  # 圧縮
ls -lh cover.png  # 再確認（1MB以下）
mv cover.png books/my-book/cover.png
git add books/my-book/cover.png
git commit -m "feat: Add optimized cover image"
git push
```

### ❌ 2. 横長の画像を使用

```
画像サイズ: 1280x670px（横長）
→ Zennの仕様は 500x700px（縦長）
```

**正しい方法：**

```
画像生成時に必ず指定:
- Dimensions: 500x700 pixels (vertical portrait format)
```

### ❌ 3. 不適切な価格設定

```yaml
# ❌ 安すぎる（内容に見合わない）
# 14章・24,000語の充実した内容
price: 200

# ❌ 高すぎる（初心者向け入門書）
# 5章・8,000語の入門書
price: 2500
```

**正しい方法：**

```yaml
# ✅ 内容に見合った適正価格
# 14章・24,000語の完全ガイド
price: 1200

# ✅ 入門書向けの手頃な価格
# 5章・8,000語の入門書
price: 500
```

### ❌ 4. publishedをtrueにしたまま編集

```yaml
# ❌ 公開中の書籍を大幅に編集
published: true  # ← 読者に未完成版が見える
```

**正しい方法：**

```yaml
# ✅ 大幅な編集時は一時的に非公開
published: false  # ← 編集中は非公開
# 編集完了後にtrueに戻す
```

---

## Agent連携

### 📖 Agentへの指示例

**書籍公開の全手順実行**
```
SwiftUI開発ガイドをZennに公開してください。
以下を含めてください：
- カバー画像の準備（500x700px、1MB以下）
- config.yamlの設定（適正価格の設定）
- デプロイ前のサイズ確認
- git commit & push
```

**カバー画像の圧縮**
```
books/my-book/cover.pngが7MBあります。
1MB以下に圧縮してデプロイしてください。
```

**価格設定の提案**
```
この書籍の適正価格を提案してください：
- チャプター数: 14章
- 総文字数: 約24,000語
- ジャンル: SwiftUI完全ガイド
```

**デプロイエラーの対処**
```
Zennデプロイエラーが発生しました：
「カバー画像のサイズは1MB以下にしてください」
対処してください。
```

### 🤖 Agentからの提案例

**カバー画像生成プロンプトの提示**
```
カバー画像が見つかりません。
画像生成AIで使用できるプロンプトを作成しますか？

含める内容：
- サイズ: 500x700px（縦長）
- デザイン: 技術書向けプロフェッショナルデザイン
- カラー: 書籍テーマに合った配色
```

**価格設定の提案**
```
書籍の内容を分析しました：
- チャプター数: 14章
- 総文字数: 約24,000語

推奨価格帯: 1,200円〜1,500円
理由: ボリュームと専門性に見合った適正価格

価格を1,200円に設定しますか？
```

**デプロイ前チェック**
```
デプロイ前にチェックします：
✓ カバー画像: 500x700px、628KB（OK）
✓ config.yaml: 設定完了
✓ published: true
✓ 価格: 1,200円（適正範囲）

デプロイを実行しますか？
```

---

## まとめ

### Zenn公開のベストプラクティス

1. **カバー画像** - 500x700px、1MB以下を厳守
2. **価格設定** - 内容に見合った適正価格（200〜5,000円）
3. **デプロイ前確認** - サイズ・設定を必ず確認
4. **エラー対処** - よくあるエラーを事前に回避

### チェックリスト

公開前に必ず確認：

- [ ] カバー画像は500x700pxか
- [ ] カバー画像は1MB以下か
- [ ] config.yamlに全チャプターが記載されているか
- [ ] 価格は適正か（200〜5,000円、または0円）
- [ ] published: trueに設定されているか
- [ ] Git commit & push完了

### よくあるエラーの即座対処法

| エラー | 対処コマンド |
|--------|------------|
| 画像サイズ超過 | `sips -Z 700 cover.png --out cover_compressed.png` |
| チャプター未記載 | `config.yaml`の`chapters`に追加 |
| 画像が見つからない | ファイル名を`cover.png`に統一 |

---

_Last updated: 2025-01-16_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaku52) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
