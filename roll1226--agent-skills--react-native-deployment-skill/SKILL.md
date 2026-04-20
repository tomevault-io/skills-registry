---
name: react-native-deployment-skill
description: React Native アプリケーションを複数のプラットフォーム（DeployGate、AppStoreConnect、GooglePlayConsole）にデプロイするスキル。テスト環境と本番環境を分離し、各環境に対応した環境変数とスキーマを参照します。 Use when this capability is needed.
metadata:
  author: roll1226
---

# React Native デプロイメントスキル

React NativeアプリをiOS/Androidで複数のデプロイ先にデプロイするスキルです。

## サポート対象

| デプロイ先 | 環境 | 用途 |
|-----------|------|------|
| DeployGate | テスト環境 | 開発チームのテスト配信 |
| AppStoreConnect | 本番環境 | iOS本番リリース |
| GooglePlayConsole | 本番環境 | Android本番リリース |

## クイックスタート

### 1. 環境設定ファイルの準備

まず環境設定ファイルを準備してください。詳細は[環境設定ガイド](references/environment-config.md)を参照。

```
project-root/
├── .env.test           # テスト環境（DeployGate用）
├── .env.production     # 本番環境（AppStore/GooglePlay用）
├── config/
│   ├── test-schema.json
│   └── production-schema.json
```

### 2. デプロイメントの実行

```bash
# テスト環境へのデプロイ（DeployGate）
npm run deploy:test

# 本番環境へのデプロイ（iOS→AppStoreConnect）
npm run deploy:ios:production

# 本番環境へのデプロイ（Android→GooglePlayConsole）
npm run deploy:android:production
```

## デプロイメントワークフロー

### 全体フロー

デプロイ前の検証から実際のアップロードまでの完全なフロー。詳細は[デプロイフロー詳細](references/deployment-flow.md)を参照。

### 環境別の処理分岐

```
環境判定（環境変数から参照）
├── TEST_ENV
│   └── DeployGateアップロード
│       ├── テスト環境の環境変数ロード（.env.test）
│       └── テスト用スキーマ検証（config/test-schema.json）
├── PRODUCTION_ENV (iOS)
│   └── AppStoreConnectアップロード
│       ├── 本番環境の環境変数ロード（.env.production）
│       └── 本番用スキーマ検証（config/production-schema.json）
└── PRODUCTION_ENV (Android)
    └── GooglePlayConsoleアップロード
        ├── 本番環境の環境変数ロード（.env.production）
        └── 本番用スキーマ検証（config/production-schema.json）
```

## 主要タスク

### 1. デプロイスクリプトの構築

スクリプトは[scripts/deploy.sh](scripts/deploy.sh)を参照してください。

**主な機能:**
- 環境変数の読み込みと検証
- ビルドプロセス
- 署名やプロビジョニング設定
- デプロイ先へのアップロード
- 成功/失敗のロギング

### 2. CI/CDパイプラインの構築

GitHub ActionsやGitlab CIなどでの自動化を想定。

**必要な設定：**
- 各デプロイ先の認証情報（Secrets管理）
- 環境変数の設定
- トリガー条件の定義

### 3. ビルド設定（package.json）

```json
{
  "scripts": {
    "deploy:test": "bash scripts/deploy.sh --env=test",
    "deploy:ios:production": "bash scripts/deploy.sh --env=production --platform=ios",
    "deploy:android:production": "bash scripts/deploy.sh --env=production --platform=android"
  }
}
```

## 必要な認証情報

**テスト環境（DeployGate）:**
- DeployGate API キー
- DeployGate API シークレット

**本番環境（iOS）:**
- Apple ID / App固有パスワード
- または: App Store Connect API キー（推奨）
- Signing Certificate & Provisioning Profile

**本番環境（Android）:**
- Google Play Console サービスアカウント JSON
- APK署名用の keystore ファイル

## トラブルシューティング

**ビルド失敗:**
- 環境変数が正しくロードされているか確認
- config/スキーマが環境に対応しているか確認

**認証エラー:**
- 認証情報がSecrets/環境変数に正しく設定されているか確認
- 認証情報の有効期限を確認

**アップロード失敗:**
- ネットワーク接続を確認
- バージョン番号が重複していないか確認
- App Store Connect/Google Play Console側の要件を確認

## 参考資料

- [環境設定の詳細ガイド](references/environment-config.md) - 環境変数とスキーマの詳細
- [デプロイメントフロー詳細](references/deployment-flow.md) - ステップバイステップガイド
- [deploy.sh スクリプト](scripts/deploy.sh) - 実行スクリプト

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/roll1226) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
