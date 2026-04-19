---
name: managing-azure-devops-stories
description: Use when user wants to view or interact with Azure DevOps work items by ID. Trigger phrases include "查看 story", "看一下 7120", "view story", or similar requests to examine specific work items.
metadata:
  author: horacewang893101
---

# Managing Azure DevOps Stories

## Overview

This skill helps you interact with Azure DevOps work items through the requirements_sync tool. It enables viewing work item details, creating related requirements, and managing the sync workflow.

## When to Use

Use this skill when the user:
- Asks to view a specific story by ID: "查看 story 7120", "看一下 7732"
- Wants to examine work item details before taking action
- Needs context about an existing work item before creating related requirements
- Mentions a work item ID in the context of requirement management

## Core Capabilities

### 1. View Work Item Details

**Quick Start:** View detailed information about a specific work item.

```bash
make view STORY_ID=7120
```

<details>
<summary>When to use</summary>

**Trigger phrases:**
- "查看 story 7120"
- "看一下 7732 这个需求"
- "view work item 7795"
- "帮我看下 DevOps 上的 7120"

**What to do:**
1. Extract the work item ID from the user's request
2. Run: `make view STORY_ID=<id>`
3. Present the work item details to the user
4. This is a **read-only operation** - it only fetches and displays information
</details>

<details>
<summary>Technical details</summary>

**What it shows:**
- Work item ID, title, type, state
- Assigned to, created by
- Full description
- Link to Azure DevOps

**Implementation:**
- Uses `src/requirements_sync/commands/view.py`
- Calls Azure DevOps REST API `workitems/{id}`
- No local files are created or modified
- Logs to `logs/sync.log`

**Error handling:**
- Work item not found → Clear error message  
- No PAT configured → Setup instructions
- Network errors → Logged and surfaced
</details>

### 2. Pull Requirements from Azure DevOps

**Quick Start:** Sync all requirements from Azure DevOps queries to local files.

```bash
make pull
```

<details>
<summary>When to use</summary>

**Trigger phrases:**
- "同步需求"
- "拉取最新的需求"
- "pull requirements"
- "从 Azure DevOps 更新本地文件"

**What happens:**
1. Fetches work items from configured queries
2. Creates/updates local Markdown files in `requirements/`
3. Preserves local notes and custom fields
4. Refreshes the requirements index
5. Marks deleted work items as `azure_deleted: true`

**When to run:**
- Before starting work on requirements
- After changes are made in Azure DevOps
- To ensure local copy is up-to-date
</details>

<details>
<summary>Technical details</summary>

**Process:**
1. Loads queries from `config/azure_devops_config.yaml`
2. Calls Azure DevOps WIQL API for each query
3. Fetches full work item details for each result
4. Merges with existing local files (preserves custom fields)
5. Writes Markdown files with YAML front matter
6. Updates `requirements/index.md`

**File format:**
```markdown
---
id: 7120
title: Story Title
type: User Story
state: Active
assigned_to: John Doe
# ...more fields...
---

## 描述
[Work item description]

## 本地备注
[Your local notes - preserved across syncs]
```

**Implementation:**
- Uses `src/requirements_sync/commands/pull.py`
- Preserves local notes section
- Safe to run repeatedly - idempotent operation
- Logs progress to `logs/sync.log`
</details>

### 3. Push Local Requirements to Azure DevOps

**Quick Start:** Upload local requirement files to Azure DevOps (智能模式).

```bash
make push         # 智能推送：仅推送 git 变更和新建文件
make push-all     # 全量推送：推送所有文件（慎用）
```

<details>
<summary>When to use</summary>

**Trigger phrases:**
- "推送需求到 Azure DevOps"
- "上传本地需求"
- "push requirements"
- "同步到 DevOps"

**智能推送模式（默认）：**
仅推送以下文件：
1. Git 中有变更的文件（staged/unstaged/untracked）
2. `id` 字段为 `null` 的新建文件（即使已 commit）

这样避免每次都推送全部155+个文件，大幅提升速度！

**全量推送模式（`make push-all`）：**
推送所有 requirements/*.md 文件（除 README/index）

**What happens:**
1. 检测需要推送的文件
2. For each file with an `id` field → Updates existing work item
3. For files without `id` → Creates new work item
4. Updates the file with the work item ID and renames file
5. Preserves description and custom fields

**When to run:**
- After creating new requirements locally
- After editing requirement descriptions
- When you want Azure DevOps to reflect local changes
</details>

<details>
<summary>Technical details</summary>

**智能推送检测逻辑:**
```bash
# 1. Git 变更检测
git diff --name-only HEAD            # staged + unstaged
git ls-files --others --exclude-standard  # untracked

# 2. 新建文件检测
扫描所有 .md 文件，找出 front matter 中 id=null 的文件
```

**推送过程:**
1. Reads selected .md files
2. Parses YAML front matter and body
3. For new items (no `id`):
   - Creates work item via Azure DevOps API
   - Updates file with new ID and URL
   - Renames file: `local-xxx.md` → `7805-Title.md`
4. For existing items (has `id`):
   - Updates work item via Azure DevOps API
   - Preserves Azure-managed fields

**兼容 `git add .` 工作流:**
```bash
# 场景 1: 先改后推
vim local-xxx.md
make push               # ✅ 检测到 untracked 文件
git add .
git commit

# 场景 2: 先改先提交
vim local-xxx.md
git add .
git commit              # id 还是 null
make push               # ✅ 检测到 id=null，仍然推送
git add .               # add 包含 id 的新版本
git commit --amend

# 场景 3: 反复推送
make push               # ✅ 只推送有变化的
make push               # ✅ 无变化，跳过
vim file.md
make push               # ✅ 只推送这1个文件
```

**Field mapping:**
- `title` → System.Title
- `type` → System.WorkItemType
- `state` → System.State
- `assigned_to` → System.AssignedTo
- Body → System.Description

**Implementation:**
- Uses `src/requirements_sync/commands/push.py`
- Creates work items with JSON Patch format
- Updates are incremental - only changed fields
- Safely handles rate limits and network errors
</details>

### 4. Create New Local Requirement

**Quick Start:** Create a new requirement file locally (not yet in Azure DevOps).

```bash
make new
```

<details>
<summary>When to use</summary>

**Trigger phrases:**
- "创建新需求"
- "新建一个 story"
- "create a new requirement"
- "添加需求"

**What happens:**
1. Prompts for requirement title
2. Creates a new Markdown file in `requirements/`
3. Pre-fills with default template
4. File does NOT have an `id` yet (not in Azure DevOps)
5. Run `make push` later to upload to Azure DevOps

**When to run:**
- When drafting new requirements locally
- Before you want them in Azure DevOps
- When brainstorming multiple requirements
</details>

<details>
<summary>Technical details</summary>

**Process:**
1. Prompts user for title
2. Generates sanitized filename from title
3. Creates file with YAML front matter template:
   ```markdown
   ---
   title: Your Title
   type: User Story
   state: New
   # No 'id' field yet - will be added on push
   ---
   
   ## 描述
   TODO: 描述这个需求
   
   ## 本地备注
   TODO: 添加你的想法和笔记
   ```
4. File is ready for editing
5. ID will be assigned when you run `make push`

**Implementation:**
- Uses `src/requirements_sync/commands/new.py`
- Template from `config/azure_devops_config.yaml`
- Sanitizes filename to avoid special characters
- Creates `requirements/` directory if needed
</details>

### 5. Refresh Local Index

**Quick Start:** Rebuild the `requirements/index.md` file from existing requirements.

```bash
make index
```

<details>
<summary>When to use</summary>

**Trigger phrases:**
- "刷新索引"
- "rebuild index"
- "更新 index.md"

**What happens:**
1. Scans all files in `requirements/`
2. Generates a sorted index with links
3. Groups by state (Active, New, Closed, etc.)
4. Updates `requirements/index.md`

**When to run:**
- After manually adding/deleting requirement files
- When index seems out of sync
- Usually not needed - `pull` and `push` auto-refresh
</details>

<details>
<summary>Technical details</summary>

**Process:**
1. Reads all `requirements/*.md` files
2. Parses front matter from each file
3. Sorts by ID (or filename for new items)
4. Generates Markdown list grouped by state
5. Writes to `requirements/index.md`

**Index format (with summary column):**
```markdown
# 需求清单

| ID | Title | 概述 | Type | State | Queries | 在线状态 | 本地进度 | 本地备注 |
|----|-------|------|------|-------|---------|----------|----------|----------|
| 7805 | Databricks Table Metadata 接入 SERVICEME - 技术实现方案 | （30-50 字概述） | User Story | Active | MainQuery | 在线 | 进行中 | xxx |
```
**Implementation:**
- Uses `src/requirements_sync/commands/index.py`
- Auto-runs after `pull` and `push`
- Safe to run manually anytime
- Purely local operation - no Azure DevOps calls
</details>

### 6. 概述列与需求总结工作流

`requirements/index.md` 中包含一列 **「概述」**，用于用 30–50 字中文简要说明每个需求的核心内容，便于快速浏览和索引。

**重要原则：**
- 概述是给人看的索引，不是完整需求说明
- 字数控制在 30–50 字左右，尽量一句话讲清楚
- 不确定/看不懂的需求，可以暂时留空，不要乱编
- 生成和更新概述主要通过 GitHub Copilot 等助手完成

#### 6.1 概述在代码层的位置

- `requirements/index.md` 第三列是「概述」：
  - 代码会从每个需求文件的 front matter 里读取 `summary` 字段填到这一列
  - 如果 front matter 里没有 `summary`，这一列就为空
- `make pull` / `make index`：
  - 只负责**读写结构**，不会自动生成任何内容
  - 已经兼容含「概述」列的表头，不会破坏已有概述

> 结论：你可以放心让助手直接修改 `requirements/index.md` 的「概述」列，或在对应需求的 front matter 里写 `summary` 字段，再运行 `make index`/`make pull` 让代码帮你同步。

#### 6.2 按顺序批量总结所有需求

触发语例子：
- 「按 index.md 的顺序，帮我给每个需求写一个 30–50 字的概述。」
- 「先从 7805 开始，往后依次总结，能看懂的就写概述，看不懂的先空着。」

助手执行步骤（建议）：
1. 打开 `requirements/index.md`，按顺序读取每一行的 ID 和标题
2. 对于每个 ID：
   - 在 `requirements/` 下找到对应的 `ID-Title.md` 文件
   - 阅读关键部分（通常是描述/背景/目标这几段）
   - 用中文写一条 30–50 字的概述，抓住「这个需求解决什么问题、核心点 1/2/3 是什么」
3. 把概述写回：
   - **优先方式 A：** 修改 `requirements/index.md` 中该行的「概述」列
   - **可选方式 B：** 同时在需求文件 front matter 中加上 `summary: ...` 字段，便于将来脚本使用
4. 对内容明显混乱、语义不清或信息缺失的需求，保持「概述」列为空，并在「本地备注」中简单说明原因（例如「需求描述过少，暂不总结」）。

**建议提示词模板（给助手用）：**

> 你是一个需求索引编辑助手。按 `requirements/index.md` 的顺序，依次读取每个需求文件（`requirements/<id>-*.md`）。
> 对每个需求，基于本地 Markdown 内容，用 **一条 30–50 字的中文句子** 概括这个需求的核心目标/方案，写到 index 表里的「概述」列（或对应 front matter 的 `summary` 字段）。
> 不要复述标题本身；重点说明「在解决什么问题」「主要思路/改动点是什么」。
> 对内容明显不清楚的需求，跳过并保持「概述」列为空。

#### 6.3 按 ID 单条更新概述

触发语例子：
- 「更新一下 7805 的概述。」
- 「7805 的总结不太准确，帮我重新写一条。」

助手执行步骤（建议）：
1. 打开 `requirements/7805-*.md`，阅读完整需求（必要时也可以先用 `make view STORY_ID=7805` 对比 Azure DevOps 上的最新内容）
2. 根据最新内容，重写一条 30–50 字的中文概述
3. 更新：
   - `requirements/index.md` 中 ID=7805 那一行的「概述」列
   - （可选）对应文件 front matter 中的 `summary` 字段
4. 向用户简要复述新概述，并说明本次只改了索引，不会影响 DevOps 上的正式描述。

#### 6.4 概述撰写风格约定

- 用自然中文短句，一般不列 1/2/3 形式的项目符号，但可以在句子中隐含「三个要点」
- 不写实现细节（代码调用、表字段名），侧重业务意图和技术方案的轮廓
- 不夸大、不主观评价（避免「非常重要」「极大优化」等）
- 对已关闭/废弃需求，可以在概述中简单点明状态（例如「历史迁移方案，现已完成」）。

### 7. Create Related Requirements

After viewing a work item, the user may ask to:
- Create a new requirement based on the story
- Create a related or derived requirement

**What to do:**
1. First view the story (if not already done) to get context
2. Ask the user what kind of requirement they want to create:
   - Copy the story content?
   - Create a blank requirement with a reference?
   - Extract specific aspects?
3. Use `make new` to create the new requirement file
4. Help the user edit it based on their needs
5. Optionally run `make push` to sync to Azure DevOps

### 8. Workflow Integration

After viewing a work item, the user may ask to:
- Create a new requirement based on the story
- Create a related or derived requirement

**What to do:**
1. First view the story (if not already done) to get context
2. Ask the user what kind of requirement they want to create:
   - Copy the story content?
   - Create a blank requirement with a reference?
   - Extract specific aspects?
3. Use `make new` to create the new requirement file
4. Help the user edit it based on their needs
5. Optionally run `make push` to sync to Azure DevOps

### 7. Workflow Integration

All commands can be chained together as needed:

| Command | Purpose | Read/Write | Touches Azure DevOps |
|---------|---------|------------|----------------------|
| `make pull` | Sync from Azure to local | Write local files | Yes (read) |
| `make view STORY_ID=<id>` | View work item details | Read-only | Yes (read) |
| `make new` | Create local requirement | Write local file | No |
| `make push` | Sync from local to Azure | Read local, write Azure | Yes (write) |
| `make index` | Rebuild local index | Write index.md | No |

**Common workflows:** (智能推送变更)
- **Quick view:** `make view STORY_ID=7120` → gather context
- **New requirement:** `make new` → edit file → `make push` (自动检测新文件)
- **Fix index:** `make index` (rarely needed - auto-refreshed)
- **Force sync all:** `make push-all` (慎用 - 推送全部文件
- **Fix index:** `make index` (rarely needed - auto-refreshed)

## Key Principles

1. **View is Read-Only**
   - `make view` only fetches and displays information
   - It does NOT create, modify, or sync anything
   - Use it freely to gather context

2. **Separate Actions**
   - Viewing and creating are distinct operations
   - Always confirm with the user before creating/pushing
   - Don't assume the user wants to create something just because they viewed it

3. **Context First**
   - When user asks to "create based on X", first view X to understand it
   - Share the relevant context with the user
   - Then discuss what to create

4. **Respect Configuration**
   - All settings are in `config/azure_devops_config.yaml`
   - Logs are in `logs/sync.log`
   - Don't hardcode values that belong in config

## Example Workflows

### Workflow 1: View Only
```
User: "查看 story 7120"
You: [Run make view STORY_ID=7120]
You: [Present the work item details]
Done - user now has the context they need
```

### Workflow 2: View Then Create
```
User: "看一下 7120，然后帮我创建一个相关需求"
You: [Run make view STORY_ID=7120]
You: [Present the work item details]
You: "我看到这个 story 是关于 [summary]. 你想创建什么样的需求？"
User: [Explains what they want]
You: [Run make new to create the requirement]
You: [Guide user to edit the file]
You: "编辑完成后可以运行 make push 推送到 Azure DevOps"
```

### Workflow 3: Pull, View, Create
```
User: "先同步一下需求，然后看看 7732"
You: [Run make pull]
You: [Run make view STORY_ID=7732]
You: [Present details and wait for next instruction]
```

## Technical Details

### Code Structure

The tool follows a modular architecture:

```
src/requirements_sync/
├── config.py           # Configuration, logging, PAT management
├── models.py           # WorkItem dataclass
├── api.py              # Azure DevOps API client
├── file_utils.py       # File I/O, Markdown processing
├── commands/           # Command implementations
│   ├── pull.py        # Pull from Azure DevOps
│   ├── push.py        # Push to Azure DevOps
│   ├── new.py         # Create new requirement
│   ├── view.py        # View work item
│   └── index.py       # Refresh index
└── sync.py             # Main entry point
```

### Configuration Files
- **`config/azure_devops_config.yaml`** - All settings
- **`.env`** or environment - `AZURE_DEVOPS_PAT` token
- **`logs/sync.log`** - Rotating log file (weekly rotation)

### API Integration
- Uses Azure DevOps REST API v7.0
- Requires PAT in environment: `AZURE_DEVOPS_PAT`
- Fetches work item fields: title, type, state, description, assigned to, etc.

### Error Handling
- Work item not found → Clear error message
- No PAT configured → Helpful setup instructions
- Network errors → Logged and surfaced to user

## Common Pitfalls

❌ **Don't:**
- Automatically create requirements without asking
- Assume view means create
- Push changes without user confirmation
- Modify work items directly (not supported)

✅ **Do:**
- View freely to gather context
- Ask clarifying questions before creating
- Use the existing Makefile commands
- Respect the user's workflow preferences

## Related Skills

- **brainstorming** - Use before creating new features based on work items
- **systematic-debugging** - Use if Azure DevOps API calls fail
- **verification-before-completion** - Use after creating/pushing requirements

## Configuration Reference

Key config values in `config/azure_devops_config.yaml`:
- `organization` - Azure DevOps organization name
- `project` - Project name
- `queries` - List of WIQL queries to sync
- `requirements_dir` - Local directory for requirement files
- `new_item_defaults` - Default values for new requirements

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horacewang893101) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
