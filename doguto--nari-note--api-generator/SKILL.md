---
name: api-generator-skill
description: |- Use when this capability is needed.
metadata:
  author: doguto
---

# Api Generator Skill

## 概要

作成・更新したAPI情報をフロントエンドのコードに反映させるスクリプトを実行する

## 実行手順

1. Pythonスクリプトの実行

リポジトリ直下の `scripts` ディレクトリに存在するPythonスクリプトを実行することでフロントエンドのコードが自動で更新される

```bash
python scripts/api-generator.py --force --verbose
```

2. 生成されたコードの確認

スクリプトの実行のみで生成が完了するので、念のため以下のファイル内容を確認し変更内容を把握する

- nari-note-frontend/src/lib/api/endpoint.ts
- nari-note-frontend/src/lib/api/hooks.ts
- nari-note-frontend/src/lib/api/types.ts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doguto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
