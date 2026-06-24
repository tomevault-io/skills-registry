---
name: terraform
description: Terraformに関するコーディング規約です。 Use when this capability is needed.
metadata:
  author: shibataka000
---

# Terraform

## Style guide

- https://developer.hashicorp.com/terraform/language/style に従ってください。

## File names

- https://developer.hashicorp.com/terraform/language/style#file-names に従ってください。
- リソース・データソースはAWSサービス単位でファイルを作成してください。

## Linting

```bash
make lint
```

---
> Source: [shibataka000/terraform-aws-notion-mcp-server](https://github.com/shibataka000/terraform-aws-notion-mcp-server) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
