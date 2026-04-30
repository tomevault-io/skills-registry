---
name: deploy
description: Sets up deployment, analytics, and health monitoring for projects. Use when user mentions デプロイ, deploy, Vercel, Netlify, 公開, アナリティクス, analytics, GA, Google Analytics, 環境診断, health check. Do NOT load for: 実装作業, ローカル開発, レビュー, セットアップ.
metadata:
  author: aiskillstore
---

# Deploy Skills

デプロイとモニタリングの設定を担当するスキル群です。

## 含まれる小スキル

| スキル | 用途 |
|--------|------|
| deploy-setup | Vercel/Netlify デプロイ設定 |
| analytics | GA/Vercel Analytics 設定 |
| health-check | 環境診断 |

## ルーティング

- デプロイ設定: deploy-setup/doc.md
- アナリティクス: analytics/doc.md
- 環境診断: health-check/doc.md

## 実行手順

1. ユーザーのリクエストを分類
2. 適切な小スキルの doc.md を読む
3. その内容に従って設定

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
