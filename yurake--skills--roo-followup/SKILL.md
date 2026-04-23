---
name: roo-followup
description: Before calling roo_task, load this skill to handle Roo follow-ups. Use when coordinating Roo responses that return resultType/needsReply: ask the user with numbered options, summarize prior results, and re-send as a new Roo task (Roo cannot append in place). Use when this capability is needed.
metadata:
  author: yurake
---

# Roo Follow-up Orchestrator

Roo side cannot append messages to an existing task. MCP/sidecar only signals `resultType` (`ask|completion|none`), `needsReply`, `question`, `options`, `sessionId`, and a `message` summary. This skill provides the client-side flow: ask the user, summarize, and re-send as a new Roo task when needed.

## When to use
- `resultType` is `ask` or `needsReply` is true from `roo_task` response.
- You must validate whether the previous Roo answer is sufficient and, if not, send a follow-up as a new task.
- You need to keep context continuity by summarizing prior answers.

## Inputs
- Roo MCP response: `message`, `resultType`, `needsReply`, `question`, `options`, `completion`, `sessionId`.
- User’s follow-up answer (free text or option).
- Optional: expected outcome to verify completeness.

## Workflow
1) **Classify response**
   - If `resultType=completion` and `needsReply=false`: consider done; show `completion` or `message` to user.
   - If `resultType=ask` or `needsReply=true`: proceed to follow-up handling.
   - If `resultType=none`: treat as inconclusive; ask user whether to retry or stop.

2) **Ask the user**
   - Show `question` and `options` (if any) **as a numbered list** so the user can reply with a single number. Example:
     ```
     Roo follow-up question: <question>
     1) <option1>
     2) <option2>
     3) <option3>
     Reply with a number or free text (type 'stop' to abort):
     ```
   - If no options, still encourage concise answers (“short answer is fine”, “stop to abort”).
   - If the user declines to continue, stop.

3) **Summarize prior context**
   - Extract the last `completion` (or `message` if completion absent).
   - Build a brief summary: e.g., `Previous result: ...`
   - Append the user’s new answer: e.g., `User follow-up answer: ...`
   - Add intent: `Continue the task based on this context.`

4) **Send a new Roo task**
   - Call `roo_task` with the composed prompt:
     ```
     Previous result: <summary>
     User follow-up answer: <answer>
     Continue the task based on this context.
     ```
   - Record the new `sessionId` (this will be new each time; Roo does not support append).

5) **Validate outcome**
   - On new response, check `resultType`.
   - If still `ask`, loop steps 2–4.
   - If `completion`, present to user and confirm acceptance; if not acceptable, craft another follow-up.

## Prompts / Templates
- **New task prompt skeleton**
  ```
  Previous result: <short summary of last completion/message>
  User follow-up answer: <user answer or chosen option>
  Continue the task based on this context.
  ```
- **User prompt (to collect answer)** — Use numbered options so the user can reply with digits only
  ```
  Roo follow-up question: <question>
  1) <option1>
  2) <option2>
  3) <option3>
  Reply with a number or free text (or type 'stop' to abort):
  ```

## Notes & Constraints
- Do not attempt to append to an existing Roo task; always create a new `roo_task`.
- `sessionId` from the previous call is not reusable for sending answers (Roo lacks append API).
- Keep summaries short to avoid prompt bloat; only include what is needed for the next step.
- If `resultType=none`, confirm with the user before retrying to avoid loops.

## Checklist
- [ ] Inspect `resultType/needsReply`.
- [ ] If ask: collect user answer; if completion: confirm sufficiency.
- [ ] Summarize prior result concisely.
- [ ] Re-send as new `roo_task` with summary + user answer.
- [ ] Repeat until completion is acceptable or user stops.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yurake) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
