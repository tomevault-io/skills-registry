---
name: unsplash-image-finder
description: Webページに画像が必要な場合にUnsplashから検索して挿入する。例：「ヒーロー画像が必要」「旅行ブログに風景写真を追加して」「コーヒーショップの画像を探して」 Use when this capability is needed.
metadata:
  author: toiee-lab
---

# Unsplash Image Finder

Unsplash APIを使用して高品質な画像を検索し、Webページに統合するSkillです。

## セットアップ

初回使用時に環境設定が必要です。詳細は `README.md` を参照してください。

```bash
# .env.local ファイルを作成し、Unsplash APIキーを設定
UNSPLASH_ACCESS_KEY=your_api_key_here
```

## 検索方法

スクリプトを実行して画像を検索します：

```bash
node .claude/skills/unsplash-image-finder/scripts/unsplash-search.js "検索キーワード"
```

### 検索のコツ
- 具体的で関連性の高いキーワードを使用
- 初回で理想的な結果が得られない場合は同義語や関連用語で再検索
- 複数キーワード: `"keyword1,keyword2,keyword3"`

## フォールバック

検索ツールでエラーが発生した場合は、Unsplash Sourceエンドポイントを使用：
```
https://source.unsplash.com/800x480/?keyword
```

## 画像最適化

URL形式:
```
https://images.unsplash.com/photo-[ID]?w=[WIDTH]&q=80&fm=webp&fit=crop
```

幅の設定:
| 用途 | 幅 |
|------|-----|
| 標準コンテンツ | w=800 |
| ヒーロー/大型背景 | w=1200 または 1600 |
| サムネイル/カード | w=400 または 600 |
| フルスクリーン背景 | w=1920 |

## 画像選定の考慮事項

- Webサイトの目的とターゲットユーザー
- カラースキームと全体的な美学
- コンテンツを補完し、ユーザー体験を向上させる画像

## HTML統合

以下の属性を必ず含める：

```html
<img
  src="[最適化URL]"
  alt="[画像内容を説明する適切なテキスト]"
  loading="lazy"
  decoding="async"
  class="[レスポンシブ対応のCSSクラス]"
/>
```

## 品質保証

画像を確定する前に：
- URLが正常に動作し、画像が読み込まれることを確認
- 画像品質が用途に適切か確認
- Webサイトのコンテンツ・ブランディングに合致するか確認

## 複数オプション

可能な場合、2〜3つの画像候補と選定理由を提示する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toiee-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
