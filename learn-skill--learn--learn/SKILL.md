---
name: learn
description: Records repeated successful actions, creates reusable skills (SKILL.md) when the same task succeeds twice, and prefers those learned skills for future similar requests. Use when the user enables learn, when in a project with .learn/ patterns, or when the user asks to save the last action as a skill. Use when this capability is needed.
metadata:
  author: learn-skill
---
# Learn: Auto-Create and Reuse Skills

**CRITICAL — Completion greeting:** When you complete a task, **read `.learn/config.json`**. If it has `userName`, **append a new line** to your reply that says: `Okay, ` + that value + `!` (e.g. `Okay, 桃姐!` or `Okay, Tom!`). This line **must be the last line** of your message. No exceptions. If you skip this when userName is set, your response is wrong.

**CRITICAL — 强制触发，无需用户手动触发 (forced, no manual trigger):** (1) **Section 2** runs **automatically** as soon as you finish any user-requested task successfully. Do **not** wait for the user to say anything; run Section 2 before you consider your response complete: read or create `.learn/patterns.json`, merge or add a pattern, write it back, and if any pattern has `successCount >= 3` and `skillCreated === false`, create the learned skill. No exceptions. (2) **Greeting**: If `.learn/config.json` has `userName`, you **MUST** begin your **first** response with the greeting (adjective + userName) and **MUST** end your response with the completion phrase (“好的，{userName}～” / “Okay, {userName}!”) every time you complete a task—whether or not a skill was created. Do not wait for the user to ask; do it every time.

This skill enables the agent to learn from repeated successful operations: record them, create `.cursor/skills/learned-*/SKILL.md` when the same pattern succeeds at least **three times** (so the 4th similar request can use the skill), and prefer those skills for later similar tasks.

**Storage**: Project root `.learn/patterns.json`, `.learn/config.json` (optional: `userName` for greetings), and `.cursor/skills/learned-<slug>/SKILL.md`. See [reference.md](reference.md) for format and naming rules.

**User name (强制问候与收尾称呼)**: If `.learn/config.json` exists and has `userName`: (1) **任务开始**：在每次任务开始时在第一条回复**开头**先打招呼（adjective + userName），例如 尊敬的{userName} / 可爱的{userName}（中文）或 Dear {userName} / Lovely {userName}（英文）。(2) **任务完成**：**每次** Agent 成功完成任务后，必须在回复**结尾或总结处**用称呼收尾，说「好的，{userName}～」或 “Okay, {userName}!”（与是否创建技能无关；无论是否生成了 learned 技能都要叫）。保存/创建技能时同样用该称呼。无 userName 则不打招呼、收尾用默认话术。

**Language (CRITICAL — 修复 bug)：** All **generated** content **must be in English only**. (1) In `.learn/patterns.json`: `taskSummary` and `stepsSummary` must be **English**. If the user’s request was in Chinese (e.g. 创建美观的登录界面), **translate to English** (e.g. Create a polished login interface) before writing. (2) In every `learned-*/SKILL.md`: the **entire file**—frontmatter `description` and **every line of the body** (steps, commands, explanations)—must be **100% English**. No Chinese or other languages in these files; this avoids bugs and keeps skills portable.

**Important**: Section 2 is **强制自动触发**：每次成功完成任务后**必须自动**执行记录与生成技能逻辑，**无需用户手动触发或说任何话**。Do not skip; do not wait for the user to ask. If the same kind of task was repeated (e.g. three times), merge into one pattern; when `successCount` reaches 3, run step 4 to generate the skill file.

---

## 1. At task start (before doing the user request)

0. **Greeting (强制；若 `.learn/config.json` 有 `userName`)**  
   **必须**在本次对话的第一条回复**开头**先打招呼，无需用户要求。Use adjective + userName, e.g. 尊敬的{userName}、可爱的{userName}、亲爱的{userName} (Chinese) or Dear {userName}, Lovely {userName} (English). Pick adjective by name style. Then proceed with the task.

1. **Check for learned skills**
   - If `.learn/patterns.json` does not exist, skip and proceed with the task as usual.
   - If it exists, read it. For each entry with `skillCreated === true`, consider whether the **current user request** matches that pattern’s `taskSummary` (same or very similar kind of task).
   - If a match is found:
     - **You MUST read** `.cursor/skills/learned-<id>/SKILL.md` (directory name is `learned-` + that pattern’s `id`) **and execute its steps one by one** (run the commands, apply the edits, etc.). Do not just say “使用已学技能” and then do something generic; actually follow the skill’s body so the same outcome is achieved.
     - Briefly tell the user you are using the learned skill (e.g. “使用已学技能 learned-add-pytest-and-run”), then perform each step from that SKILL.md. If the current context differs (e.g. server already running), still run the skill’s steps where applicable, or report the situation and suggest the next action (e.g. “端口已占用，先结束占用进程再执行 npm run start”).
   - If no match, proceed with the task as usual (no need to mention learn).

2. **Do not** read or mention `.learn/` or learned skills if the user has not enabled learn or the project has no `.learn/` yet; in that case just do the task.

---

## 2. After a task completes successfully (强制自动触发，mandatory, no manual trigger)

**强制自动触发**：每次成功完成任务后**必须自动**执行本节，**无需用户手动触发**（用户不必说「记录」或「保存」等）。Do not skip; do not wait for the user to say anything. Run this section **only after the user’s task is fully complete** (all edits done, all tool runs done, full summary written). Do **not** create or update the learned SKILL.md in the middle of the task—wait until the task is done so the skill body reflects the **complete** steps. Run this section before you consider your response complete.

0. **Completion greeting (强制；与是否创建技能无关)**  
   每次完成任务后，在回复的**结尾或总结处**必须用称呼收尾。若 `.learn/config.json` 有 `userName`，说「好的，{userName}～」（或 啊/呀 等语气词）或 “Okay, {userName}!”；无 userName 则用默认收尾。**无论本节是否生成了 learned 技能，都要叫称呼**。

1. **Summarize the task (English only)**
   - Write a short, reusable **taskSummary** in **English only** (one sentence). If the user’s task was in Chinese (e.g. 创建美观的登录界面, 加上我的名字), **translate to English** (e.g. “Create a polished login interface”, “Add user name to welcome message”) before writing to `.learn/patterns.json`. Do not write Chinese into patterns.json.
   - Write **stepsSummary** in **English only**: a few bullet points of what you did (e.g. “Check deps”, “Add pytest”, “Write/run tests”). Use `[]` if none.

2. **Ensure `.learn/` exists**
   - If `.learn/patterns.json` does not exist, create `.learn/` and initialize with **pretty-printed JSON** (indentation, e.g. 2 spaces per level), not a single line:
     ```json
     {
       "version": 1,
       "patterns": []
     }
     ```

3. **Merge or add pattern (run once per task completion; write `.learn/patterns.json` only once)**
   - **One project = one pattern only. Never add a second id.** In each project (where `.learn/patterns.json` lives), keep **exactly one** pattern. **One project → one learned skill.** Do **not** create a new pattern with a new `id` (e.g. “add-dynamic-background”, “add-more-animations”, “add-analytics”)—**always** merge the new completion into the **existing single** pattern. If you see patterns already has one entry, you must only update that entry (increment successCount, append stepsSummary, update lastDoneAt); never push a second object into the array.
   - **Pattern object schema (use exactly these keys; do not use `task` or `summary`):** `id` (string, slug), `taskSummary` (string, **English only**), `stepsSummary` (array of strings, English, can be `[]`), `successCount` (number), `lastDoneAt` (string, **ISO 8601** e.g. `2026-02-03T12:00:00Z`), `skillCreated` (boolean).
   - Read existing `patterns` from `.learn/patterns.json` **once**.
   - If `patterns` has **more than one** entry (legacy or mistake): **merge all into one** before proceeding. Keep the first pattern’s `id`; set `successCount` to the sum of all patterns’ `successCount`; concatenate all `stepsSummary` arrays (dedupe if needed); set `lastDoneAt` to the latest; set `skillCreated` to `true` if any had `true`. Overwrite `patterns` with this single pattern so `patterns.length === 1`. Then treat as “one entry” below.
   - If `patterns` is **empty**: add **one** pattern with `id` (slug from current taskSummary, see reference.md), `taskSummary` (English), `stepsSummary` (array from this run), `successCount: 1`, `lastDoneAt` (ISO 8601), `skillCreated: false`.
   - If `patterns` has **exactly one** entry: **always merge** the new completion into it. Update: increment `successCount`, set `lastDoneAt` to current ISO time, append or merge `stepsSummary` with the new steps (keep all steps in English). Optionally broaden `taskSummary` to reflect the full project (e.g. “Build and refine project site (structure, theme, pages, features)”). Do **not** add a second pattern.
   - Write the updated list back to `.learn/patterns.json` **once**, in **pretty-printed** form (indentation, e.g. 2 spaces per level; one property per line). The `patterns` array must contain **exactly one** pattern after this step.

4. **Create or update the learned skill**
   - **Create** (first time): **only if** `successCount >= 3` **and** `skillCreated === false`. If you **added** a new pattern (first time) or merged once or twice, then `successCount` is 1 or 2 — **do not create a skill**; wait until the same task is completed again and merged until `successCount` becomes 3 (so the 4th similar request can use the skill).
   - **Create** when `successCount >= 3` and `skillCreated === false`:
     - **Say to the user (auto-trigger phrase)**: If `.learn/config.json` has `userName`, say “好的，{userName}～” (or 啊/呀 as fits) then “我学会了一项技能，让我来把它打包为 skills。” In English: “Okay, {userName}! I’ve learned a skill and am packaging it as a skill.” If no userName, use “我学会了一项技能，让我来把它打包为 skills。” / “I’ve learned a skill, let me package it as a skill.” Then proceed.
     - **Generate slug/id** (if not already set) per reference.md; ensure uniqueness (see above).
     - **Create directory** `.cursor/skills/learned-<slug>/` (e.g. `learned-add-pytest-and-run`). Directory name = `learned-` + `id` (the slug stored in the pattern).
     - **Create `SKILL.md`** inside that directory. **Write the complete task**: the body must include **every step you actually performed** in this run (and in previous runs if you are summarizing)—do not truncate or write before the task is fully done. **The entire file must be 100% English** (no Chinese in description or body):
       - **Frontmatter**: `name: learned-<slug>` (must match the directory name exactly; Cursor requirement: max 64 chars, only `[a-z0-9-]`).
       - **description**: Expand `taskSummary` into a “when to use” description **in English** (third person, WHAT + WHEN), max 1024 chars.
       - **Body**: **All steps and text in English.** Concrete, repeatable steps so that when the skill is used later, the agent can execute them and get the same result. Include: exact commands (e.g. `npm run start`, `node server.js`), file paths, and conditions if needed—all in English. Do not write Chinese in steps, comments, or explanations. Number the steps. Keep under 500 lines.
     - Follow the structure and style of create-skill (see Cursor’s create-skill skill) so the generated skill is valid and discoverable.
     - In `.learn/patterns.json`, set that pattern’s `skillCreated` to `true`.
   - **Update** (4th, 5th, 6th, 7th... time): When you **merged** into a pattern that already has `skillCreated === true` (i.e. the same task has been done 4 or more times), **update** the existing `.cursor/skills/learned-<slug>/SKILL.md` so the skill stays complete. Read the existing SKILL.md, then overwrite the **body** (and optionally refresh the `description`) with the **full, concrete steps** from this run and any refinements. Include every step you performed in this completion; merge with or replace the previous body so the skill reflects the most complete version. Keep the same directory and frontmatter `name`; only the body (and description if needed) is updated. Do not leave the skill incomplete—each update should make it more accurate.

---

## 3. Naming and conflicts (summary)

- **Slug (id)**:
  - From `taskSummary` (which must be in English): keep only letters/numbers, replace spaces and punctuation with one `-`, collapse multiple `-`, trim; result only `[a-z0-9-]`, total length including `learned-` ≤ 64.
  - If the user’s task was in Chinese, translate to English first, then slugify (e.g. “添加 pytest” → “add-pytest”).
- **Uniqueness**: With one pattern per project, there is only one `id` per project. If you ever have more than one pattern in the same project, merge them into one (sum successCount, merge stepsSummary, keep one id) and delete the extra pattern(s).
- **Directory and name**: Directory = `learned-<slug>`. In SKILL.md frontmatter, `name: learned-<slug>` (same as directory name). See [reference.md](reference.md) for full rules.

---

## 4. User-triggered “save as skill”

When the user says they want to **save the last action as a skill** (e.g. “把刚才的操作存成技能”“save that as a skill”):

1. **Say to the user (manual-trigger phrase)**: If `.learn/config.json` has `userName`, start with “好的，{userName}～” (or 啊/呀 as fits) or “Okay, {userName}!” then “我来把 [short task description] 打包成技能.” If no userName, use “我来把 [short task description] 打包成技能.” Use a brief description of the last task (e.g. “add pytest and run tests” → “我来把 add pytest and run tests 打包成技能”). Then proceed.
2. Treat the last completed task as successful (if it was the one they mean).
3. Ensure a pattern exists for it (same merge/add logic as in section 2). If it’s the first time, add with `successCount: 1`; you may set `successCount: 3` so that a skill is created immediately, or follow the normal rule and create the skill only when `successCount >= 3` (creating immediately on explicit user request is reasonable).
4. If `successCount >= 3` (or user explicitly asked to create now), create `.cursor/skills/learned-<slug>/SKILL.md` with **the entire file in English** (description and body; no Chinese) and set `skillCreated: true` as in section 2.
5. Confirm to the user that the skill was created and where it lives (e.g. “已创建技能 learned-xxx，路径 .cursor/skills/learned-xxx/SKILL.md”).

---

## 5. What not to do

- Do not create or edit skills under `~/.cursor/skills-cursor/`.
- Do not record when the user clearly rejected the result or asked to undo.
- **Never add a second pattern; never create a new id.** One project = one pattern only. Every completion (including “add dynamic background”, “add more animations”, “add analytics”) must **merge** into the single existing pattern—do not add “add-more-animations” or any other new id. If patterns has one entry, only update that entry; never push a second object.
- Do not use a `name` or directory that does not follow the `learned-<slug>` convention or Cursor’s name rules (see reference.md).
- **Do not write Chinese or any non-English text** in `.learn/patterns.json` (taskSummary, stepsSummary) or in any `learned-*/SKILL.md` (description or body). Both must be **100% English**; if the user’s task was in Chinese, translate to English before writing. This is required to avoid bugs and keep SKILL.md portable.
- Do not set `successCount` to 3 or more when **adding** a new pattern in Section 2 (auto flow). New pattern = first time for this task = always `successCount: 1`, `skillCreated: false`. Only when you **merge** (same task done again) does `successCount` increase; create a skill only when it reaches 3.
- Do not run Section 2 more than once per task completion; do not write `.learn/patterns.json` more than once per response. Do not add duplicate patterns (same or very similar `taskSummary`). Use the key `taskSummary` (not `task` or `summary`). Use English only for `taskSummary` and `stepsSummary`. Use full ISO 8601 for `lastDoneAt` (e.g. `2026-02-03T12:00:00Z`), not a date-only string like `2026-02-03`. When writing `.learn/patterns.json`, always use pretty-printed JSON (indentation, newlines), not a single line.
- Do **not** create or update the learned SKILL.md **before** the user’s task is fully complete (all edits and tool runs done). The skill body must reflect the **complete** steps actually performed; otherwise the skill will be incomplete. When the same task is done again (4th, 5th, 6th, 7th... time), **update** the existing learned SKILL.md so it stays complete.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/learn-skill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
