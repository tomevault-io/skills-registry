---
name: deploy
description: Google Cloud Run へのデプロイ。ビルド確認→テスト→デプロイの手順を実行 Use when this capability is needed.
metadata:
  author: ando-s
---

# デプロイ手順

## Step 1: ビルド確認
```bash
npm run build
```
ビルドエラーがあれば修正してから次へ。

## Step 2: テスト実行
```bash
npm run test
```
テスト失敗があれば修正してから次へ。

## Step 3: 未コミット変更の確認
`git status` で未コミットの変更がないか確認。
変更があればユーザーに確認を取る。

## Step 4: デプロイ実行
```bash
gcloud run deploy ai-radio \
  --source . \
  --region asia-northeast1 \
  --allow-unauthenticated \
  --project ai-agent-radio
```

## Step 5: 動作確認
デプロイ完了後、本番URL を報告:
https://ai-radio-922674109498.asia-northeast1.run.app

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ando-s) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
