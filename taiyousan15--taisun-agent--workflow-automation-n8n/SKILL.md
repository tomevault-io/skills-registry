---
name: workflow-automation-n8n
description: n8n workflow automation design Use when this capability is needed.
metadata:
  author: taiyousan15
---

# n8n Workflow Automation

## Instructions

- 要件を「Trigger / Steps / Data / Error handling / Notification / Audit」に分解
- 最小ノード構成→拡張構成の順で提案
- 失敗時の再実行設計（冪等性）を必ず入れる

## Example

- Trigger: Webhook
- Steps: DB lookup → API call → Update DB
- Notification: 成功/失敗を統一通知

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/taiyousan15) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
