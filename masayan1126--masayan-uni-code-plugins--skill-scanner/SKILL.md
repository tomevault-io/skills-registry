---
name: skill-scanner
description: Macで登録済みClaude agent skillsをスキャンし一覧表示。「スキルを調べて」「登録済みスキル一覧」などで使用。読み取り専用で安全に実行。 Use when this capability is needed.
metadata:
  author: masayan1126
---

# Skill Scanner

登録されているClaude agent skillsをスキャンし一覧表示する（読み取り専用）。

## 使用方法

詳細は `scripts/scan_skills.py --help` を参照。

```bash
# 基本スキャン（ユーザーレベルのみ）
python3 scripts/scan_skills.py

# 指定ディレクトリ以下のプロジェクトをスキャン（要ユーザー確認）
python3 scripts/scan_skills.py --projects-dir ~/git
python3 scripts/scan_skills.py --projects-dir ~/work

# 複数ディレクトリを同時にスキャン
python3 scripts/scan_skills.py --projects-dir ~/git --projects-dir ~/work

# JSON形式で出力
python3 scripts/scan_skills.py --json
```

**重要**: プロジェクトディレクトリをスキャンする際は必ずユーザーに確認してから実行。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masayan1126) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
