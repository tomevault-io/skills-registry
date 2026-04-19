---
name: browser-testing
description: ブラウザでの動作確認・テストガイド Use when this capability is needed.
metadata:
  author: soki0909
---

# ブラウザテストスキル

開発中のWebアプリをブラウザで確認・テストする手順。

## 開発サーバー起動

```bash
cd c:\Users\kumes\Documents\Soki0909.github.io
npm run dev
```

デフォルトURL: `http://localhost:5173`

## テスト対象ページ

| パス         | 説明                         |
| ------------ | ---------------------------- |
| `/`          | Hub Page（タイムライン一覧） |
| `/docs/{id}` | Document Page（詳細ページ）  |
| `/_old/...`  | 旧ページ（アーカイブ）       |

## ブラウザ操作手順

### 1. ページ遷移テスト

```
1. http://localhost:5173/ を開く
2. タイムラインカードをクリック
3. 詳細ページへの遷移を確認
4. ブラウザの戻るボタンで戻れることを確認
```

### 2. レスポンシブテスト

```
1. DevTools を開く (F12)
2. デバイスエミュレーションを有効化
3. 以下のサイズで確認:
   - Mobile: 375px (iPhone)
   - Tablet: 768px (iPad)
   - Desktop: 1280px+
```

### 3. 画像・メディア確認

```
1. 詳細ページを開く
2. 画像の読み込みを確認
3. 動画プレーヤーの操作を確認
4. LazyLoadingの動作を確認（スクロール時）
```

## Core Web Vitals チェック

DevTools > Lighthouse で計測：

| 指標 | 目標値  |
| ---- | ------- |
| LCP  | < 2.5s  |
| FID  | < 100ms |
| CLS  | < 0.1   |

## コンソールエラー確認

```
1. DevTools > Console タブ
2. 赤いエラーがないことを確認
3. 警告（黄色）も可能な限り解消
```

## ビルド確認

本番ビルドの確認は `npm run preview` を使用：

```bash
npm run build
npm run preview
```

## よくある問題

### 画像が表示されない

- パスが `/assets/...` で始まっているか確認
- ファイルが `public/assets/` にあるか確認

### スタイルが適用されない

- Tailwindクラス名のタイポを確認
- `npm run dev` を再起動

### ルーティングエラー

- `react-router-dom` のパス設定を確認
- `App.tsx` のルート定義を確認

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/soki0909) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
