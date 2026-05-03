---
name: active-terminal-error-catcher-and-fixer
description: Enforce strict terminal verification and automatic fix loops for development and build commands. Trigger this skill whenever a user runs or asks to run dev, start, build, typecheck, or compiler/watch commands across any language or framework, including npm, pnpm, yarn, bun, vite, next, react-scripts, tsc, and similar scripts. Never declare success from process state alone; always inspect logs, stop on diagnostics, explain, fix, rerun, and continue the original user task once clean. Use when this capability is needed.
metadata:
  author: tototofu123
---

# Active Terminal Error Catcher And Fixer

## Purpose
Prevent false success claims such as "dev server is running so everything is fine" when terminal logs actually contain warnings or errors.

## Mandatory Activation
Use this skill whenever terminal commands are executed for:
1. npm run dev, npm start, npm run build, npm run typecheck
2. pnpm, yarn, or bun equivalents
3. Direct tool commands such as vite, next, react-scripts, tsc
4. Any compiler, watcher, or dev-server command in any language/framework

## Core Rule
Never treat a running process or zero exit code as success by itself.
Success is valid only after log inspection confirms no warnings and no errors.

## Log Inspection Contract
1. Read both stdout and stderr every run.
2. Scan at least the most recent 500 lines of terminal output.
3. If warnings or errors exist, immediately mark run as failed.
4. Do not ignore warnings. Warnings are blocking in this workflow.

## Diagnostic Classes To Catch
Catch and treat as blocking:
1. Next.js, Vite, React, TypeScript diagnostics
2. Build/compile/transpile warnings or errors
3. Runtime exceptions, stack traces, module resolution failures
4. Language/tooling diagnostics from any framework or language

Useful signal patterns include:
1. error
2. warning
3. failed to compile
4. TypeScript error
5. TS followed by digits
6. Cannot find module
7. Module not found
8. ReferenceError, SyntaxError, TypeError
9. unhandled rejection, exception, panic, traceback

## Required Failure Response
When any diagnostic is detected:
1. Halt success reporting immediately.
2. Explain the error meaning in one short line.
3. Show the exact console log error lines.
4. Describe the fix method clearly and concretely.
5. Apply fixes, rerun, and re-check logs.

Use this reporting template exactly:
1. Error meaning: one-line plain explanation
2. Console log error message: exact lines from terminal
3. Fix method: what was changed and why it resolves the issue
4. Rerun result: clean or still failing
5. Next action: continue fixing or resume original user task

## Auto-Fix Loop
1. On first detected issue, begin fixing immediately. Do not wait for extra ready banners.
2. After each fix, rerun and re-scan logs.
3. Retry automatically for up to 3 reruns total.
4. If still failing after 3 reruns, report blockers and best next fix path.

## Recurring Error Follow-Up
If the same or related diagnostic persists across reruns:
1. Record a Hermes follow-up note with:
- error signature
- affected files/components
- attempted fixes
- remaining hypotheses
2. Continue with best-next fix strategy.
3. Once resolved, continue the pending user request flow automatically.

## Success Criteria
Declare success only when all are true:
1. Command execution is stable.
2. Last 500 log lines from stdout and stderr contain no warnings and no errors.
3. No unresolved diagnostics remain.
4. Original user task can proceed safely.

## Forbidden Behavior
1. Do not say "works" based only on process still running.
2. Do not say "no issues" if warnings exist.
3. Do not skip stderr.
4. Do not stop after detection without explanation and fix attempt.

---
> Source: [tototofu123/my-skills](https://github.com/tototofu123/my-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
