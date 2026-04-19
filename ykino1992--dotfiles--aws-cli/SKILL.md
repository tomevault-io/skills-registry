---
name: aws-cli
description: AWS CLIコマンドを扱う際（質問への回答、GitHub Actionsワークフロー作成、スクリプト作成など）に公式ドキュメントを確認して、存在するコマンドと正しいオプション、必要なIAM権限を提示する。存在しないコマンドやIAM権限の指定ミスを防ぐために、必ずリファレンスを確認してから記述する。 Use when this capability is needed.
metadata:
  author: ykino1992
---

# AWS CLI

## 概要

AWS CLIコマンドを扱う際、存在しないコマンドやIAM権限の指定ミスを防ぐため、必ず公式ドキュメントを確認してから記述・提案する。

## 適用場面

以下の場面で自動的にこのスキルを適用する:
- AWS CLIコマンドに関する質問への回答
- GitHub ActionsワークフローファイルでのAWS CLIコマンド記述
- シェルスクリプトやPythonスクリプトでのAWS CLIコマンド記述
- Dockerfileやその他の設定ファイルでのAWS CLIコマンド記述

## ワークフロー

AWS CLIコマンドを扱う場合、以下の手順で対応する:

1. **公式ドキュメントを確認**
   - 対象サービスのCLIリファレンスをWebFetchで取得
   - 使用可能なコマンドとオプションを確認
   - 必要なIAM権限を確認

2. **正確な情報を提示**
   - 確認した実在するコマンドのみを提案
   - 正しいオプションと構文を提示
   - 必要なIAM権限を明示

## 主要サービスのドキュメントURL

- **S3**: https://docs.aws.amazon.com/cli/latest/reference/s3/
- **ECR**: https://docs.aws.amazon.com/cli/latest/reference/ecr/
- **ECS**: https://docs.aws.amazon.com/cli/latest/reference/ecs/
- **IAM**: https://docs.aws.amazon.com/cli/latest/reference/iam/
- **CloudWatch Logs**: https://docs.aws.amazon.com/cli/latest/reference/logs/
- **CloudWatch**: https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/
- **CloudFront**: https://docs.aws.amazon.com/cli/latest/reference/cloudfront/
- **App Runner**: https://docs.aws.amazon.com/cli/latest/reference/apprunner/

## 重要事項

- 推測でコマンドを提案しない
- 必ずドキュメントで実在を確認してから回答
- IAM権限も同様にドキュメントで確認
- 複数のサービスが関わる場合は、各サービスのドキュメントを確認

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ykino1992) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
