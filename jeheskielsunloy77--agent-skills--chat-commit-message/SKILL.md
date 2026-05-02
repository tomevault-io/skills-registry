---
name: chat-commit-message
description: Write detailed Conventional Commit messages using only the active chat conversation as context. Use when the user asks for commit messages based on discussion history, requests module-scoped commit subjects, or explicitly forbids checking git logs, diffs, or code files. Use when this capability is needed.
metadata:
  author: jeheskielsunloy77
---

# Chat Commit Message

## Workflow

1. Read only the current conversation context in the chat window.
2. Do not inspect git history, staged changes, repository files, or command output.
3. Infer commit `type` from intent described in chat (`feat`, `fix`, `refactor`, `docs`, `test`, `chore`, etc.).
4. Infer commit `scope` from module names mentioned in chat. If no module scope is clearly present, use `global`.
5. Produce a Conventional Commit subject line in this format:
   `<type>(<scope>): <short summary>`
6. Add a detailed body explaining what was requested and what was done, based only on chat context.
7. Add optional footer lines only when relevant (`BREAKING CHANGE: ...`, issue references, co-authors).

## Output Rules

- Return plain text only.
- Do not use markdown formatting symbols.
- Keep the message easy to copy and paste into `git commit`.
- Use English for all user-facing text.

## Quality Checklist

- Subject uses Conventional Commit format.
- Scope is module-specific when available; otherwise `global`.
- Body details are factual and grounded in chat context.
- No claims based on repository inspection.
- No markdown syntax in final output.

## Example

feat(crm): add lead activity timeline filters

Implement timeline filtering controls for lead activity views as discussed in the conversation.
Define filter behavior for date range and activity type and align naming with existing CRM module terminology.
Document the expected behavior in the commit body using only conversation-provided context.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeheskielsunloy77) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
