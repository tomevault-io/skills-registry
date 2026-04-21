---
name: creating-characters
description: Generates Nano Banana Pro prompts for original character designs. Use when user mentions "キャラクター作成", "キャラデザ", or "オリキャラ".
metadata:
  author: masayan1126
---

# キャラクター作成

## ワークフロー

1. **アイデア確認**: `progress/ideas.md` をチェックし、該当するアイデアがあれば提案
2. **用途確認**: アイコン/イラスト/漫画/ゲーム/マスコット/グッズ
3. **設定ヒアリング**: 性別・年齢、外見、性格、特徴
   - **インコの場合**: 種類を確認（シロハラ/コザクラ/サザナミ/オカメ）→ PARROTS.md 参照
4. **スタイル選択**:
   - アニメ調
   - ゲーム調
   - ポップ調
   - リアル調
   - マスコット調
   - 手書き・ミニマル調
   - **スケッチ調**（鉛筆・デッサン/水彩/ボールペン/クレヨン）
   - **ゆるキャラ調**（動物マスコット用、A〜Dパターンあり）
5. **AIっぽさ緩和オプション確認**: [ANTI_AI_STYLE.md](../ANTI_AI_STYLE.md) 参照
   - 「ベタ塗り」「デフォルメされたフォルム」を含めるか確認
6. **プロンプト生成**: PROMPT_TEMPLATE.md 使用
   - インコ×スケッチ調の場合は専用テンプレート使用
   - **ゆるキャラ調の場合**: パターン選択（A:丸背景アイコン/B:ステッカー/C:4体セット/D:表情差分）
7. **タイトル提案**: 作品にふさわしいタイトル案を3〜5個提示
   - 絵本風なら擬音語・問いかけ（「ぴょん！」「なあに？」）
   - アート作品なら情景・雰囲気（「おさんぽ日和」「静かな午後」）
   - キャラクター名があれば名前を活かす
8. **バリエーション提案**（任意）: 表情/ポーズ/衣装差分
9. **成果物保存**:
   - プロンプト: `output/character/{タイトル}.md`（ディレクトリがなければ作成）
   - 生成画像: `output/character/images/{タイトル}.png`
   - ゆるキャラの場合: `output/yuru-chara/` 配下も可
10. **進捗更新**: `progress/ideas.md` を更新（完了したアイデアにチェック）

## 参照ファイル

- [STYLES.md](STYLES.md): スタイル定義（スケッチ調含む）
- [PROMPT_TEMPLATE.md](PROMPT_TEMPLATE.md): テンプレート
- [PARROTS.md](PARROTS.md): インコモチーフ定義
- [EXAMPLES.md](EXAMPLES.md): サンプル

## 必須ルール

- 設定は具体的に記述
- 用途に応じたスタイル選択

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masayan1126) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
