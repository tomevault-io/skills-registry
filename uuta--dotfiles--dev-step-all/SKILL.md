---
name: dev-step-all
description: Execute all remaining steps from split plan files without user confirmation. Automatically loops through steps until completion or error. Use when this capability is needed.
metadata:
  author: uuta
---

# Dev Step All

stepファイルを全て読み込み、ユーザー確認なしで連続実装する。エラー・テスト失敗時は停止して報告。

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

3. Display step info:
   ```
   ## Step {current}/{total}: {step_title}
   実装中...
   ```

### 3. Execute Step

1. **Use Claude Code Agent Teams (TeamCreate + Task tool) as much as possible** to parallelize work within a step:
   - Identify independent sub-tasks (e.g., writing tests for different modules, implementing unrelated files, running checks)
   - Spawn teammates to handle sub-tasks concurrently
   - Coordinate results before marking the step complete
   - Fall back to sequential execution only when tasks have strict dependencies

2. Implement according to step file instructions
3. Follow TDD approach if applicable:
   - Red: Write failing tests first
   - Green: Implement minimum code to pass
   - Refactor: Clean up

4. Run verification checks defined in step file
   - **On test failure or error → STOP immediately and report the issue.** Do not continue to the next step.

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
     Report: "全てのstepが完了しました！"
     → **STOP**

### 5. Auto-Continue

- If steps remain → **Loop back to Phase 2 immediately** (no user confirmation)
- Only stop when:
  - All steps are `Done`
  - An error or test failure occurs (report the issue)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
