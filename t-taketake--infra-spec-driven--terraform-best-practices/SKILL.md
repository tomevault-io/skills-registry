---
name: terraform-best-practices
description: 業界のベストプラクティスとアーキテクチャ標準に照らしてTerraformコードをレビューします。ユーザーがコードレビュー、ベストプラクティスのアドバイス、またはアーキテクチャの改善を求めた場合に使用してください。 Use when this capability is needed.
metadata:
  author: t-taketake
---

# Terraform ベストプラクティススキル

このスキルを使用して、Terraform コードがベストプラクティスに準拠しているかを検証します。

---

## 重要な原則 (MCP Guidelines)

1. **AWS モジュールの優先**: ゼロから作る前に、AWS モジュールが存在しないか確認する
2. **AWSCC プロバイダーの優先**: 可能な限り `hashicorp/awscc` (Cloud Control API) を使用する
3. **セキュリティファースト**: 開発ワークフローにセキュリティスキャン (Checkov 等) を組み込む

---

## 実行手順

### Step 1: モジュール選定の検証
- 実装されているリソースが、既存の AWS モジュールで代替可能でないか確認
- 特に AI/ML (Bedrock, SageMaker) や OpenSearch 関連は AWS モジュールの使用を強く推奨

### Step 2: プロバイダー使用の検証
- AWSCC プロバイダーが優先的に使用されているか確認

### Step 3: セキュリティと構造の検証
- 標準的なディレクトリ構造と環境分離がなされているか
- ステート管理（S3 Backend + S3 Lockfile）が適切か
- 機密情報がハードコードされていないか

---

## ディレクトリ構造（このリポジトリの正）

```text
terraform/
├── aws/                      # AWS の共通リソース定義
├── gcp/                      # GCP の共通リソース定義
├── modules/                  # 再利用モジュール
│   ├── aws/                  # 例: vpc/, subnet/, ecs/, alb/, rds/ ...
│   └── gcp/                  # 例: gke/, cloud_run/, cloud_sql/ ...
└── environments/             # 環境ルート（modules を組み立てる場所）
    ├── aws/
    │   └── dev/
    └── gcp/
        └── prod-dr/
```

---

## チェックリスト

### 1. コードベースの構造

| 項目 | チェック内容 |
| :--- | :--- |
| **環境分離** | `terraform/environments/<cloud>/<env>/` に環境固有の差分が寄せられているか |
| **モジュール化** | ルート環境に `resource` を直接増やしていないか |
| **標準ファイル** | `main.tf` / `variables.tf` / `outputs.tf` / `backend.tf` の分割を維持しているか |

### 2. セキュリティ

| 項目 | チェック内容 |
| :--- | :--- |
| **最小権限** | IAM ロールに必要最小限の権限のみ付与されているか |
| **暗号化** | S3, EBS, RDS 等のデータストアで暗号化が有効か |
| **機密情報** | パスワード・API キーが Secrets Manager / SSM で管理されているか |
| **パブリックアクセス** | S3 / RDS へのパブリックアクセスがブロックされているか |

### 3. バックエンド設定

| 項目 | チェック内容 |
| :--- | :--- |
| **リモートステート** | S3 等のリモートバックエンドを使用しているか |
| **ステートロック** | `use_lockfile = true` を使用しているか（DynamoDB は非推奨） |
| **暗号化** | `encrypt = true` でステートファイルを暗号化しているか |

### 4. AWS 固有のベストプラクティス

| 項目 | チェック内容 |
| :--- | :--- |
| **タグ付け** | `Environment`, `Project`, `ManagedBy` 等のタグが付与されているか |
| **AZ 分散** | リソースが複数の AZ に分散されているか（Multi-AZ） |
| **マネージドサービス** | EC2 自前構築ではなく RDS / ElastiCache / ALB 等を活用しているか |

---

## レポート出力フォーマット

```markdown
## 📋 ベストプラクティスレビュー結果

### ✅ 準拠している項目
- モジュール化
- タグ付け
- 暗号化

### ⚠️ 改善が必要な項目
| 項目 | 現状 | 推奨 |
| :--- | :--- | :--- |
| xxx | ... | ... |

### 🛠 修正案
\`\`\`hcl
# 修正後のコード
\`\`\`
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t-taketake) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
