---
name: git-local-checkpoint
description: 自动监控代码变更。当代码修改完成后，自动创建本地 Git 存档 (Commit)。分析历史风格生成 Commit Message。严禁推送。 Use when this capability is needed.
metadata:
  author: ryderwe
---

# Git Local Checkpoint (Auto-Save)

这是一个“哨兵”技能。它的作用是确保所有的代码变更都被安全地记录在本地 Git 历史中。

## 🤖 自动触发规则 (Auto-Trigger Protocol)
**你必须遵守以下协议：**
每当你使用文件编辑工具（如 `write_file`, `replace_in_file` 等）完成代码修改，并确认代码无语法错误后，**必须立即**调用本技能进行存档。
* **不要**询问用户是否需要提交。
* **不要**等待用户指令。
* **自动**执行存档流程。

## ⚠️ 安全红线 (Safety Rules)
1.  **NO PUSH**: 严禁执行 `git push`。只做本地 commit。
2.  **Local Only**: 所有操作仅限于本地文件系统。

## 执行逻辑 (Execution Logic)

### Step 1: 检查状态
{{#tool_call}}
run_shell_command(command="git status")
{{/tool_call}}
*如果没有检测到变更（Clean working tree），则停止执行并告知用户无需存档。*

### Step 2: 学习风格 (Style Analysis)
为了让提交记录看起来像人类写的，请读取最近的记录：
{{#tool_call}}
run_shell_command(command="git log -n 5 --pretty=format:'%s'")
{{/tool_call}}

### Step 3: 生成并提交 (Commit)
1.  **分析**: 结合 `git status` 的变更内容和 Step 2 的历史风格（Emoji/Conventional/中文描述）。
2.  **生成**: 创建一个简练的 Commit Message。
3.  **执行**:
{{#tool_call}}
run_shell_command(command="git add . && git commit -m 'YOUR_GENERATED_MESSAGE'")
{{/tool_call}}

### Step 4: 最终反馈
仅输出一句简短的系统通知，例如：
> "✅ [Auto-Save] 已创建本地存档: `fix: update api timeout`"

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryderwe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
