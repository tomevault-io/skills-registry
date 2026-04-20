---
name: git-commit-push
description: Automate staging, committing, and pushing git changes. Use when the user asks to create a commit and push it, or when the agent must finish a change with git add/commit/push steps. Use when this capability is needed.
metadata:
  author: chenxi750328ai
---

# Git Commit Push

## 概述
- 目标：一键完成 `git add` → `git commit` → `git push`
- 适用场景：
  - 用户只想说「帮我提交并推送」而不想再给命令
  - 需要确保所有步骤（检查状态、填写信息、推送）都按规范执行

> **注意**：在执行任何写操作前，请确认已经通过代码审阅、自测等流程。提交信息务必准确描述改动内容。

## 快速开始
1. 确保当前目录为仓库根目录（或包含 `.git`）。
2. 运行脚本（默认提交全部改动）：
   ```bash
   python3 scripts/git_commit_push.py "提交说明"
   ```
3. 若只想提交部分文件，在消息后追加文件列表：
   ```bash
   python3 scripts/git_commit_push.py "提交说明" path/to/file1 path/to/file2
   ```
4. 脚本会逐步执行：
   - `git add` （自动打印命令）
   - `git status`（确认变更）
   - `git commit -m "..."`
   - `git push`

## 工作流详情

### 1. 检查仓库状态
- `git status -sb` 快速查看是否存在未提交或冲突的文件。
- 若仓库与远程分支已分叉，先 `git pull --rebase` 或与用户确认如何处理。

### 2. 准备提交信息
- 提交信息建议格式：`<模块>: <一句话说明>`，下方附带详细 bullets。
- 如需多行信息，可手工运行 `git commit`，本文档脚本仅支持单行消息。

### 3. 执行脚本或手动命令
- **脚本方式**：使用 `scripts/git_commit_push.py`。
- **手动方式**：
  ```bash
  git add <files>
  git status
  git commit -m "..."
  git push
  ```

### 4. 推送失败处理
- 若因分叉导致 push 被拒绝：
  - `git pull --rebase origin <branch>`
  - 解决冲突后 `git push`
- 若有 pre-commit hooks 或 CI 失败，请按照错误日志修复后重试。

## 常见注意事项
- 提交前再次 Review 改动，避免将临时文件、日志等加入版本库。
- 如仓库包含子模块，需分别进入子模块执行提交与推送。
- 保持提交原子性（一次提交解决单一问题）。

## scripts/
- `git_commit_push.py`：自动执行 add/commit/push 的辅助脚本。
  - 需在仓库根目录运行。
  - 支持手动指定要提交的文件列表。

---

**完成用例**（示例）：
> 用户："提交吧"
>
> 代理：
> 1. 用 `python3 scripts/git_commit_push.py "feat: add jianghu skill"` 提交
> 2. 输出脚本日志，确认已经推送
> 3. 回复用户提交成功

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenxi750328ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
