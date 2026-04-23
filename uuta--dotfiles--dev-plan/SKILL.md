---
name: dev-plan
description: Create TDD-based implementation plan from docs/goal.md, save to Obsidian vault, and split into step files. Use when this capability is needed.
metadata:
  author: uuta
---

# Dev Plan

docs/goal.md を元にTDDベースの実装計画を作成し、Obsidian vault に保存後、stepファイルに分割する。

## Workflow Schema

See [workflow.yaml](../dev/schema/workflow.yaml) for YAML structure.

## Procedure

### 1. Setup & Validation

1. Get branch name from git: `git branch --show-current`
   - Extract number: `feat/#1473` → `1473`

2. Check if `docs/{branch_name}/dev-workflow.yaml` exists
   - If not exists → Create with initial state:
     ```yaml
     plan:
       status: Ready
     execute:
       status: Pending
       current_step: 1
       total_steps: 0
     ```

3. Read YAML and check `plan.status`
   - If `Done` → Ask user: "計画は既に完了しています。再作成しますか？"
   - If `Approved` → Skip to Step 4 (Save to Obsidian)
   - If `In progress` → Resume from where left off
   - If `Ready` → Proceed

### 2. Read Requirements

1. Read `docs/goal.md`
   - If not exists → Error: "docs/goal.md が見つかりません"

2. Update YAML: `plan.status: In progress`

### 3. Create Plan

**DO NOT use EnterPlanMode/ExitPlanMode.** Instead, replicate the same workflow using subagents directly.

#### 3a. Explore (parallel)

Launch up to 3 Explore subagents in parallel to research the codebase:
```
Task(subagent_type="Explore",
     prompt="[goal.md の内容] + 調査したいコードエリアの指示",
     description="Explore codebase")
```
- 1 agent: タスクが特定ファイルに限定される場合
- 複数 agents: スコープが広い・複数エリアにまたがる場合

#### 3b. Design

Explore の結果を元に Plan subagent で実装計画を設計:
```
Task(subagent_type="Plan",
     prompt="[goal.md] + [Explore結果] + TDD構造 (Red→Green→Refactor) に従って計画を立てて",
     description="Design implementation plan")
```
- Plan subagent は read-only なのでファイル変更不可
- TDD 構造: [tdd-plan.md](../dev/steps/tdd-plan.md) を参照

#### 3c. Present & Approve

1. Plan subagent の出力をそのままユーザーに表示
2. **AskUserQuestion tool** を使って確認:
   ```
   Question: "この計画でよいですか？"
   Options: ["承認する", "修正が必要"]
   ```
   - "修正が必要" → ユーザーのフィードバックを受けて 3b からやり直し
   - "承認する" → 次へ

3. Update YAML: `plan.status: Approved`

### 4. Save to Obsidian

Launch sub-agent:
```
Task(subagent_type="generalist",
     prompt="Execute /plan-on-md skill to save the plan to Obsidian vault",
     description="Save plan to Obsidian")
```

### 5. Split into Steps

Launch sub-agent:
```
Task(subagent_type="generalist",
     prompt="Execute /plan-steps-split skill to split the plan into step files",
     description="Split plan into steps")
```

### 6. Finalize

1. Count total steps created
2. Update YAML (`Approved` → `Done`):
   ```yaml
   plan:
     status: Done
   execute:
     status: Ready
     current_step: 1
     total_steps: {count}
   ```

3. Report to user:
   - Plan saved location
   - Number of steps created
   - Next action: `/dev-step` to start implementation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
