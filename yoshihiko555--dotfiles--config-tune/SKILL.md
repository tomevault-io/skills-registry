---
name: config-tune
description: | Use when this capability is needed.
metadata:
  author: yoshihiko555
---

# config-tune

Interactively tune a Claude Code configuration file based on its ANALYSIS.md.

## Inputs

- `$ARGUMENTS`: Path to the target configuration file, optionally followed by `--dry-run`
- Parse `--dry-run` flag: if present, do NOT apply any edits — only show proposals

## Phase 1: Preparation (automatic)

1. Parse `$ARGUMENTS` to extract the file path and `--dry-run` flag.
2. Read the target configuration file. If it does not exist, report an error and stop.
3. Check for `ANALYSIS.md` in the same directory as the target file.
   - If ANALYSIS.md exists: read it.
   - If ANALYSIS.md does NOT exist: inform the user and run `/config-analyze` on the target file first, then read the generated ANALYSIS.md.
4. Load the rubric from `references/rubric.md` (relative to this skill's directory).

## Phase 2: Issue Presentation (Japanese)

Present the tuning recommendations from ANALYSIS.md to the user in Japanese.

Format:

```
🔧 チューニング対象: {filename}
種別: {type}

━━━ チューニング推奨事項 ━━━

🔴 高優先度:
  1. {issue title} — {brief description}
  2. {issue title} — {brief description}

🟡 中優先度:
  3. {issue title} — {brief description}

🟢 低優先度:
  4. {issue title} — {brief description}
```

## Phase 3: Policy Decision (interactive)

Use AskUserQuestion to ask which items to address:

Question: "どの項目を改善しますか？"
Options:
- "高優先度のみ" — Address only high-priority items
- "高 + 中優先度" — Address high and medium priority items
- "番号指定" — Let user specify item numbers
- "キャンセル" — Abort tuning

If user selects "番号指定", ask a follow-up question for the specific numbers.
If user selects "キャンセル", stop and report that tuning was cancelled.

## Phase 4: Improvement Execution (interactive, per item)

For each selected item, execute the following loop:

### 4a. Generate improvement proposal

Based on the issue from ANALYSIS.md and the rubric evaluation criteria, generate a concrete improvement.

**Language rules:**
- User-facing explanations: Japanese
- Configuration file content (edits): English (maintain existing language for token efficiency)
- ANALYSIS.md content: Japanese

### 4b. Present preview to user

Show the proposal in Japanese:

```
━━━ 項目 {N}: {issue title} ━━━

📋 変更内容:
{Japanese description of what will change and why}

📝 Before:
{relevant section of the current file, abbreviated if long}

📝 After:
{proposed new content for that section}
```

### 4c. Get user confirmation

Use AskUserQuestion:

Question: "この変更を適用しますか？"
Options:
- "適用" — Apply this change
- "修正指示" — User wants to adjust the proposal
- "スキップ" — Skip this item

If "修正指示": ask the user for their feedback, revise the proposal, and repeat from 4b.
If "スキップ": move to the next item.
If "適用":
  - If `--dry-run` is active: record the proposal but do NOT edit. Inform user: "（dry-run: 変更は適用されません）"
  - Otherwise: use the Edit tool to apply the change to the target file.

## Phase 5: Summary (automatic)

After all items are processed, output a summary in Japanese:

```
━━━ チューニング完了 ━━━

📊 変更一覧:
  ✅ {item 1}: {brief description of change}
  ✅ {item 2}: {brief description of change}
  ⏭️ {item 3}: スキップ

📈 構造評価の変化:
  Before: {N}/{total} セクション充足
  After:  {M}/{total} セクション充足

📈 ルーブリック変化:
  {axis}: {before} → {after}
  {axis}: {before} → {after}
```

If `--dry-run`:
```
ℹ️ dry-run モードのため、変更は適用されていません。
上記の提案を適用するには --dry-run を外して再実行してください。
```

If NOT `--dry-run` and at least one change was applied:
- Regenerate ANALYSIS.md by re-analyzing the modified file (run the same analysis logic as config-analyze).
- Report: "ANALYSIS.md を再生成しました。"

## Constraints

- NEVER modify the target file without explicit user approval for each change
- In `--dry-run` mode, NEVER use Edit or Write on the target file
- Keep configuration file content in English for token efficiency
- All user interaction is in Japanese
- If ANALYSIS.md has no recommendations, report "チューニング推奨事項はありません" and stop
- Respect the rubric priorities: address High items before Medium, Medium before Low

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yoshihiko555) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
