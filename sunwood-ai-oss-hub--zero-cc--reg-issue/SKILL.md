---
name: reg-issue
description: | Use when this capability is needed.
metadata:
  author: sunwood-ai-oss-hub
---

# Reg Issue (Register Issue)

計画からIssueを作成してプロジェクトに登録する省略形スキルです。

**フロー**: `plan` → `project-mgmt`

## 使い方

```
/reg-issue ユーザー認証機能を追加して
```

## ワークフロー

```
┌─────────────────────────────────────────────────────────────┐
│  1. plan モジュール                                           │
│     - 要望のヒアリング                                          │
│     - タスク分割                                               │
│     - Issue 作成（親子構造）                                   │
│     - 親子関係の設定                                           │
├─────────────────────────────────────────────────────────────┤
│  2. project-mgmt モジュール                                    │
│     - プロジェクトへの追加                                     │
│     - ステータス設定（Todo）                                   │
│     - 日付設定                                                 │
└─────────────────────────────────────────────────────────────┘
```

## 詳細

このスキルは以下のモジュールを組み合わせたものです：

- [.claude/skills/plan/SKILL.md](../plan/SKILL.md) - タスク分割 → Issue作成 → 親子関係設定
- [.claude/skills/project-mgmt/SKILL.md](../project-mgmt/SKILL.md) - プロジェクト追加・ステータス設定・日付設定

詳細なドキュメントは各モジュールを参照してください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sunwood-ai-oss-hub) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
