---
name: update-design
description: 指定したIssueを元に設計書を自律的に更新する仕様駆動開発サポートコマンド。Issue番号を引数で指定可能（例: /update-design 15） Use when this capability is needed.
metadata:
  author: kasiopeiya
---

Issue番号: $ARGUMENTS

GitHub Issueの内容を解析し、該当する `docs/design/` 配下の設計書を自律的に更新してください。
更新後のレビューは `/doc-review` と人間が実施するため、対話的な確認は最小限にし自律的に作業を完了してください。
完了したタスクは `gh issue edit` コマンドでGitHub Issueのチェックリストを更新してください。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kasiopeiya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
