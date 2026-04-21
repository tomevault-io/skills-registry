---
name: creating-illustrations
description: Generates high-quality image prompts for AI image generators (Midjourney, DALL-E, Stable Diffusion). Use when user mentions "イラスト作成", "画像プロンプト", "プロンプト生成", or "画像生成".
metadata:
  author: masayan1126
---

# 汎用イラストプロンプト作成

## ワークフロー

1. **用途確認**: 用途を確認（アルバムカバー/商品写真/コンセプトアート/etc.）
2. **プリセット確認**: 使用可能なプリセットの利用有無
   - **プリセットあり** → PRESETS.md から設定を適用
   - **プリセットなし** → ステップ3へ
3. **要件収集**: `AskUserQuestion` ツールで6つの要素をヒアリング
   - **主題・被写体**: メインの対象と詳細
   - **色彩**: カラーパレット、カラーコード
   - **スタイル・画風**: アニメ調/写実的/etc.
   - **雰囲気**: ムード、感情
   - （オプション）光源・照明、視点・構図
4. **情報構造化**: 6つの原則に基づいて収集した情報を整理
5. **AIっぽさ緩和オプション確認**: [ANTI_AI_STYLE.md](../ANTI_AI_STYLE.md) 参照
   - 「ベタ塗り」「デフォルメされたフォルム」を含めるか確認
6. **プロンプト生成**: PROMPT_TEMPLATE.md 使用して実行可能なプロンプトを生成
7. **クリップボードコピー**: `AskUserQuestion` で選択肢提示後、`pbcopy`（Mac）でコピー
8. **アイデア確認**: `progress/ideas.md` をチェックし、該当するアイデアがあれば提案
9. **成果物保存**:
   - プロンプト: `output/illustration/{タイトル}.md`（ディレクトリがなければ作成）
   - 生成画像: `output/illustration/images/{タイトル}.png`
10. **進捗更新**: `progress/ideas.md` を更新（完了したアイデアにチェック）

## 6つの原則

高品質プロンプト作成の実証済みテクニック:

| 原則 | 説明 |
|------|------|
| **優先順位** | 最重要要素を冒頭に配置 |
| **具体性** | 単語ではなく詳細な説明を追加 |
| **光源** | 光の方向、質、色温度を指定 |
| **視点** | カメラアングルと構図を明確化 |
| **画風** | 様式（印象派、アニメ等）を指定 |
| **色調** | カラーパレットで雰囲気を統一 |

詳細は [TECHNIQUES.md](TECHNIQUES.md) 参照

## 参照ファイル

- [TECHNIQUES.md](TECHNIQUES.md): 6つのテクニック詳細
- [PRESETS.md](PRESETS.md): プリセット集（レトロオブジェクト/ゆるふわインコ）
- [PROMPT_TEMPLATE.md](PROMPT_TEMPLATE.md): テンプレート

## プリセット一覧

| プリセット名 | 概要 |
|-------------|------|
| レトロオブジェクト | 80-90年代ノスタルジック、ローファイアニメスタイル |
| ゆるふわインコ | パステルカラー、癒し系手描き風イラスト |

## 必須ルール

- 優先順位を守り最重要要素を冒頭に
- 具体的な詳細を含める（曖昧表現を避ける）
- 光源・色彩を明確に指定してAI感を軽減
- カラーコード（#XXXXXX）で正確な色指定
- ネガティブプロンプトで不要要素を除外

## 著作権への配慮

- 特定のアーティスト名・作品名の使用を避ける
- スタイル・様式の一般的な説明を使用（例: "impressionist style" ✓ / "in the style of [artist]" ✗）
- 不適切・差別的な表現を避ける

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masayan1126) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
