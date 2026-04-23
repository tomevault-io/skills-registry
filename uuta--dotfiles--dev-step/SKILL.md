---
name: dev-step
description: Execute one step at a time from split plan files. Reads step markdown from Obsidian vault and implements with user confirmation. Use when this capability is needed.
metadata:
  author: uuta
---

# Dev Step

stepファイルを1つずつ読み込み、実装を進める。各step完了後にユーザー確認。

## Workflow Schema

See [workflow.yaml](../dev/schema/workflow.yaml) for YAML structure.

## Procedure

### 1. Validation

1. Get branch name from git: `git branch --show-current`
   - Extract number: `feat/#1473` → `1473`

2. Check if `docs/{branch_name}/dev-workflow.yaml` exists
   - **If not exists** → Ask user:
     ```
     「dev-workflow.yaml が見つかりません。
     先に /dev-plan を実行して計画を作成しますか？」
     ```
   - Stop and wait for user response

3. Read YAML and validate state:
   - **If `plan.status` != `Done`** →
     ```
     「計画が未完了です（status: {current_status}）。
     先に /dev-plan を完了してください。」
     ```
   - Stop

4. Check `execute.status`:
   - If `Done` → "全stepが完了しています。再実行しますか？"
   - If `Pending` → Update to `In progress`, proceed
   - If `In progress` → Resume from `current_step`

### 2. Load Step File

1. Determine step file path:
   - Project: Extract from pwd (e.g., `mu-muc-app-service2` → `mu-muc-app-service`)
   - Path: `~/uuta/Projects/{project}/{branch_name}/*-step-{current_step:02d}.md`

2. Read step file using Glob + Read
   - If not found → Error with available files list

3. Display step info to user:
   ```
   ## Step {current}/{total}: {step_title}

   {step_content_summary}

   実装を開始しますか？
   ```

### 3. Execute Step

1. Implement according to step file instructions
2. Follow TDD approach if applicable:
   - Red: Write failing tests first
   - Green: Implement minimum code to pass
   - Refactor: Clean up

3. Run verification checks defined in step file

:::note warn
Don't do run git add or commit until user instructed
:::

### 4. Complete Step

1. Update step file status to `Done` (in Obsidian)

2. Update YAML:
   ```yaml
   execute:
     current_step: {current + 1}
   ```

3. Check if all steps complete:
   - If `current_step > total_steps`:
     ```yaml
     execute:
       status: Done
     ```
     → Call `/create-pr` automatically (no user confirmation needed)

   - Else:
     Report: "Step {current}/{total} 完了。次のstepに進みますか？"

### 5. Wait for User

- User says "next" / "続き" → Loop back to Step 2
- User says "stop" / "終了" → Exit gracefully
- User asks questions → Answer and wait

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
