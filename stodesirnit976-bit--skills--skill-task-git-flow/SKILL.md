---
name: skill-task-git-flow
description: 这个 skill 用于把你的日常开发流程标准化为 “一个任务 = 一个分支” 的 GitHub 工作流，并尽量自动化从 建仓/初始化 → 开任务分支 → 提交推送 → 创建 PR → 合并回主分支 的完整闭环。它通过约定统一的分支命名（如 task/YYYYMMDD-slug）和提交规范，减少手动操作与遗漏，保证每个任务都有清晰的变更边界、可追溯的提交记录和可审阅的 PR 流程；同时提供配套 PowerShell 脚本（如 ensure repo / start task branch / finish task）用于在 Windows 环境下一键完成关键步骤，适合个人或小团队在 GitHub 上进行规范化迭代开发与交付。"Automate a task-per-branch GitHub workflow in the current project (init repo + create GitHub repo if needed; per-task branch; commit/push; PR + merge). Use when the user asks to confirm/submit/commit, push and merge, create a repo, or explicitly requests a task-per-branch GitHub flow. Use when this capability is needed.
metadata:
  author: stodesirnit976-bit
---

# Task Git Flow

## Language

- Default output language: **中文（简体）**.
- If the user explicitly requests English (or another language), follow the user’s request.
- For code/comments/identifiers: keep whatever language is conventional for the codebase; do not translate API names, file paths, or exact error messages.

## Goal

Make Git + GitHub a repeatable “one task = one branch” loop:
1) Ensure the project is a Git repo and hosted on GitHub.
2) Start each task on a fresh branch.
3) When the user confirms a task is done, commit (detailed), push, PR, merge to default branch.

## Codex/Windows 沙箱常见卡点

- `git switch -c task/...` 报错 `cannot lock ref ...` / `unable to create directory ...` / `Access is denied`：通常是执行环境对 `.git/` 写入受限；在 Codex CLI 里需要用 `sandbox_permissions=require_escalated` 重新运行该 git 命令（理由：创建分支需要写 `.git` 元数据）。
- `gh auth status` 在不同终端/不同权限下结果不一致：优先在“将要执行 PR/合并”的同一执行环境里再次运行 `gh auth status -h github.com` 验证；若出现 `token in keyring is invalid`，用 `gh auth logout -h github.com -u <user>` + `gh auth login -h github.com` 重新登录。
- `gh pr create/merge` 失败但用户终端显示已登录：大概率是 keyring 访问/权限差异导致；同样用提升权限的执行环境重试（并再次 `gh auth status`）。

## Workflow Decision Tree

### A) First run / repo not set up

Trigger if **any** is true:
- `.git` does not exist / `git rev-parse --is-inside-work-tree` fails
- No `origin` remote is configured
- No GitHub repo exists for this project yet

Do:
1) Check prerequisites: `git` and GitHub CLI `gh`.
2) Initialize Git (if needed) and ensure a default branch exists (prefer `main`).
3) Choose a GitHub repo name (AI-generated) and create the repo.
4) Push the default branch to `origin`.

### B) Start a new task

Trigger when the user says “start next task”, or you are about to begin work on a new user-requested task.

Do:
1) Confirm the task title (1 line) and any acceptance criteria.
2) Ensure working tree is clean (or stash/commit before branching).
3) Create a branch for this task (see naming rules below) and push it.
4) Do the implementation work on that branch.

### C) Finish a task (user-confirmed)

Trigger when the user says anything like:
- “我确认这个 Task 已完成 / 可以提交了 / 提交并合并吧 / Task 已完成 / 可以 push”
- “commit this / push and merge / create PR and merge”
- Manual invocation of this skill to finalize work

Do (with explicit user confirmation before merge):
1) Ensure repo is clean enough to commit (only intended changes staged).
2) Write a detailed commit message that explains *what* and *why*.
3) Push branch.
4) Create a PR and merge it into the default branch.
5) Switch back to default branch, pull latest, and delete the task branch.

## Conventions

### Branch naming
Use: `task/<YYYYMMDD>-<slug>`
- Slug: lowercase, words separated by `-`, keep it short but meaningful.
- Example: `task/20260127-add-login-form-validation`

### Commit message
Prefer a clear title + bullet details:
- Title: imperative, ≤ 72 chars (e.g., `feat: add login form validation`)
- Body: 3–8 bullets describing behavior changes, edge cases, and any follow-ups.

If the repo already uses Conventional Commits, follow it; otherwise, keep it readable and consistent.

## Commands (manual, no scripts)

### Preflight checks
```powershell
git --version
gh --version
gh auth status
```

### Initialize + create GitHub repo (first time only)
```powershell
git init -b main
git add -A
git commit -m "chore: initial commit"
gh repo create <repo-name> --source . --remote origin --push --private
```

If `gh repo create` is unavailable, create the repo in GitHub UI, then:
```powershell
git remote add origin <repo-url>
git push -u origin main
```

### Start a task branch
```powershell
git switch main
git pull
git switch -c task/20260127-<slug>
git push -u origin task/20260127-<slug>
```

### Finish a task (commit + PR + merge)
```powershell
git status
git add -A
git commit
git push
gh pr create --fill
gh pr merge --merge --delete-branch
git switch main
git pull
```

## Resources (optional)

### scripts/
Use these scripts to execute the workflow deterministically (recommended):
- `scripts/ensure-github-repo.ps1`
- `scripts/start-task-branch.ps1`
- `scripts/finish-task.ps1`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stodesirnit976-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
