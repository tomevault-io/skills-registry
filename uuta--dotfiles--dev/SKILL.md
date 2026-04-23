---
name: dev
description: Orchestrate TDD-based development workflow. Checks workflow state and delegates work to subagents (dev-plan, dev-step, create-pr). Use when this capability is needed.
metadata:
  author: uuta
---

# Dev Orchestrator

開発ワークフローの状態を確認し、実作業を subagent に委譲するマネージャー。
メインコンテキストは「状態管理 + subagent 起動」のみに限定する。

## Related Skills

- `/dev-plan` - 計画作成・分割
- `/dev-step` - step単位の実装
- `/dev-step-all` - 全step自動実行
- `/create-pr` - PR自動作成

## Workflow Schema

See [schema/workflow.yaml](schema/workflow.yaml) for YAML structure.

## Procedure

### 1. Check Workflow State

1. Get branch name: `git branch --show-current`
2. Read `docs/{branch_name}/dev-workflow.yaml`

### 2. Delegate to Subagent

状態に応じて Task tool で subagent に委譲する（subagent_type は常に `general-purpose`）:

```
YAML exists?
├─ No → plan.status: Ready とみなす
│
└─ Yes → Check status
    │
    ├─ plan.status: Ready / In progress
    │   → AskUserQuestion: "計画を作成します。開始しますか？"
    │   → Task(general-purpose,
    │          prompt="/dev-plan スキルを実行してください。ブランチ: {branch}",
    │          description="Run dev-plan")
    │
    ├─ plan.status: Done, execute.status: Pending / In progress
    │   → AskUserQuestion:
    │       "実行方法を選んでください"
    │       Options: ["手動（step-by-step）", "自動（全step）"]
    │   → 手動: Task(general-purpose,
    │               prompt="/dev-step スキルを実行してください。ブランチ: {branch}",
    │               description="Run dev-step")
    │   → 自動: Task(general-purpose,
    │               prompt="/dev-step-all スキルを実行してください。ブランチ: {branch}",
    │               description="Run dev-step-all")
    │
    ├─ execute.status: Done, pr.status: Pending (or pr missing)
    │   → Task(general-purpose,
    │          prompt="/create-pr スキルを実行してください。ブランチ: {branch}",
    │          description="Create PR")
    │
    └─ pr.status: Done
        → サマリー表示のみ（subagent 起動なし）
```

### 3. Report Status

subagent 完了後に YAML を再読してサマリー表示:

```
## Workflow Status: {branch_name}

| Phase   | Status                        |
|---------|-------------------------------|
| Plan    | {status}                      |
| Execute | {status} ({current}/{total})  |
| PR      | {status} ({url})              |
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
