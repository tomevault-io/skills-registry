---
name: super-review
description: Use when user requests code review with multiple perspectives, wants parallel review from different models, or uses trigger words: super-review, 超级审查, 并行审查, 全面 review
metadata:
  author: zuozuo
---

# Super Review

使用 Claude Code 原生 subagent 并发能力进行多模型并行审查。

## 核心能力

- 并发启动多个 reviewer（Claude Code opus/sonnet/haiku、Codex、Gemini CLI）
- 智能选择 prompt 模板
- 结果去重、分级、汇总
- 只关注 P0/P1 问题

## 执行流程

### 1. 确定审查内容

**优先级：**
1. 用户明确指定（文件/目录/commit/commit 范围）
2. 智能推断：
   - 检查 staged changes：`git diff --cached --name-only`
   - 检查最近 commit：`git log -1 --format=%H`
   - 如都无，询问用户

**推断逻辑：**
```bash
# 检查 staged changes
staged=$(git diff --cached --name-only)
if [ -n "$staged" ]; then
    echo "发现 staged changes，建议审查这些变更"
fi

# 检查最近 commit
last_commit=$(git log -1 --format=%H 2>/dev/null)
if [ -n "$last_commit" ]; then
    echo "发现最近 commit: $last_commit"
fi
```

**推断后必须向用户确认：**
- "检测到有 staged changes，是否审查这些变更？"
- "检测到最近的 commit [hash]，是否审查该 commit？"

### 2. 收集背景信息

从以下来源提取业务背景：
- 用户在 prompt 中的描述
- commit messages（如审查 commit）
- PR 描述（如有）
- 相关文档（CLAUDE.md、README.md、docs/ 目录）

**整理为结构化背景：**
- **purpose**：这次修改要实现什么目标
- **scope**：修改范围概述
- **requirements**：具体需求或约束
- **related_docs**：相关文档路径列表

**获取 commit message 示例：**
```bash
# 单个 commit
git log -1 --format=%B <commit-id>

# 多个 commit
git log --format="- %s" HEAD~3..HEAD
```

**注意**：背景信息是业务层面的（做什么、为什么），不是技术层面的（用什么语言、框架），技术信息 reviewer 自己从代码中获取。

### 2.5 检查固定文档路径

**固定路径规范（项目纪律）：**

| 文档类型 | 固定路径 | 说明 |
|----------|----------|------|
| UI 设计规范 | `docs/design/ui-spec.md` | 前端 UI 一致性审查的参照 |
| 设计文档 | `docs/design/` | 架构设计、技术设计 |
| 开发计划 | `docs/plans/` | 开发计划、实现方案 |

**检查步骤：**

1. 检查变更是否涉及前端文件（.tsx, .jsx, .vue, .css, .scss 等）
2. 如果涉及前端，检查 `docs/design/ui-spec.md` 是否存在
3. 检查 `docs/design/` 和 `docs/plans/` 目录是否存在设计文档
4. 如果必要文档缺失，向用户确认是否继续

**用户确认场景：**

```
┌────────────────────────────────────────┐
│ ⚠️ 文档路径检查                         │
├────────────────────────────────────────┤
│ 发现前端文件变更，但缺少 UI 规范：       │
│ - docs/design/ui-spec.md (不存在)       │
│                                        │
│ 将跳过 UI 一致性审查阶段。继续？         │
├────────────────────────────────────────┤
│ [继续] [取消]                           │
└────────────────────────────────────────┘
```

### 2.6 确定审查范围

**范围来源优先级：**

| 优先级 | 来源 | 场景 |
|--------|------|------|
| 1 | 外部注入 (`--context`) | Workflow 调用 |
| 2 | 自主推断 | 用户手动调用 |

**自主推断信息来源：**

| 优先级 | 来源 | 提取的信息 |
|--------|------|-----------|
| 1 | Commit message / PR description | 本次变更的目标 |
| 2 | `docs/plans/` 开发计划文档 | 当前阶段、任务范围 |
| 3 | `docs/design/` 设计文档 (PRD/架构) | 参照物，用于一致性审查 |
| 4 | 代码变更涉及的目录结构 | 推断影响的模块 |

**推断步骤：**

1. **从 commit message 提取目标**：
   ```bash
   git log -1 --format=%B HEAD
   ```

2. **查找相关计划文档**：
   ```bash
   # 查找最近修改的计划文档
   ls -t docs/plans/*.md 2>/dev/null | head -3
   ```

3. **查找相关设计文档**：
   ```bash
   # 根据变更文件路径推断相关设计文档
   # 例如：src/auth/ 变更 → 查找 docs/design/auth*.md
   ```

4. **分析变更涉及的目录**：
   ```bash
   git diff --name-only HEAD~1..HEAD | xargs dirname | sort -u
   ```

**审查范围结构：**

```yaml
review_scope:
  objective: "本次变更的目标"
  includes:
    - "包含的功能点1"
    - "包含的功能点2"
  excludes:
    - "明确不在范围内的内容"
  reference_docs:
    - "docs/design/xxx.md"
    - "docs/plans/xxx.md"
```

### 2.7 确认审查范围

**执行模式：**

| 调用方式 | 行为 |
|----------|------|
| `super-review HEAD` | 推断范围 → **展示确认** → 执行 |
| `super-review HEAD --skip-confirm` | 推断范围 → **直接执行** |
| `super-review HEAD --context:{...}` | 使用注入范围 → **直接执行** |

**默认模式（需确认）：**

推断完成后，向用户展示范围摘要并等待确认：

```
┌────────────────────────────────────────┐
│ 📋 审查范围                            │
├────────────────────────────────────────┤
│ 目标: {{scope.objective}}              │
│ 参照: {{scope.reference_docs}}         │
│ 不含: {{scope.excludes}}               │
├────────────────────────────────────────┤
│ [确认执行] [修正范围] [取消]            │
└────────────────────────────────────────┘
```

**跳过确认的场景：**

1. 用户明确指定 `--skip-confirm`
2. 通过 `--context` 注入完整范围
3. 在 Workflow 中被调用（检测到上下文）

**注入范围格式示例：**

```bash
super-review HEAD --context:'{"objective":"实现登录API","includes":["登录端点","JWT验证"],"excludes":["密码找回"],"reference_docs":["docs/design/auth.md"]}'
```

### 3. 读取配置

从 `.reviews/config.yaml` 读取配置。如不存在，使用默认配置。

**配置文件格式：**
```yaml
output_dir: .reviews
default_timeout: 300

reviewers:
  # Claude 系列：通过 Task tool 启动，无需 command 配置
  claude-opus:
    enabled: true
    timeout: 300

  claude-sonnet:
    enabled: true
    timeout: 300

  claude-haiku:
    enabled: true
    timeout: 300

  # 外部 CLI：通过 Bash tool 调用，需要 command 配置
  codex:
    enabled: true
    timeout: 300
    # command 可选，有默认值

  gemini:
    enabled: true
    timeout: 300
    # command 可选，有默认值

  # 外部 CLI（ccs + claude -p）：通过上下文切换使用其他 LLM
  gemini-cc:
    enabled: true
    timeout: 300
    # command: "$(ccs gemini) && claude -p --dangerously-skip-permissions '{{prompt}}'"

  glm-cc:
    enabled: true
    timeout: 300
    # command: "$(ccs glm) && claude -p --dangerously-skip-permissions '{{prompt}}'"

prompts:
  my-custom:
    path: .reviews/prompts/my-custom.md
```

**默认配置（当 config.yaml 不存在时）：**

| Reviewer | 默认启用 | 超时 | 启动方式 |
|----------|---------|------|----------|
| claude-opus | ✅ | 300s | Task tool (subagent) |
| claude-sonnet | ✅ | 300s | Task tool (subagent) |
| claude-haiku | ✅ | 300s | Task tool (subagent) |
| codex | ✅ | 300s | Bash tool (CLI) |
| gemini | ✅ | 300s | Bash tool (CLI) |
| gemini-cc | ✅ | 300s | Bash tool (ccs gemini + claude -p) |
| glm-cc | ✅ | 300s | Bash tool (ccs glm + claude -p) |

**注意**：Claude 系列使用 Claude Code 原生 subagent，不需要 CLI 命令配置。

### 3.5 前置检查 - CLI 工具可用性

在启动 reviewer 前，检查外部 CLI 工具是否存在。

**注意**：Claude 系列（opus/sonnet/haiku）使用 Task tool 启动，无需检查 CLI。

**检查方法（仅外部 CLI）：**
```bash
# 检查 codex CLI
which codex >/dev/null 2>&1 && echo "codex: OK" || echo "codex: NOT FOUND"

# 检查 gemini CLI
which gemini >/dev/null 2>&1 && echo "gemini: OK" || echo "gemini: NOT FOUND"

# 检查 ccs (context switch) 命令
which ccs >/dev/null 2>&1 && echo "ccs: OK" || echo "ccs: NOT FOUND"

# 检查 claude CLI
which claude >/dev/null 2>&1 && echo "claude: OK" || echo "claude: NOT FOUND"
```

**处理逻辑：**

| Reviewer 类型 | 检查方式 | 不可用时处理 |
|--------------|---------|-------------|
| Claude 系列 | 无需检查（始终可用） | - |
| codex | `which codex` | 跳过，标记 `skipped (CLI not found)` |
| gemini | `which gemini` | 跳过，标记 `skipped (CLI not found)` |
| gemini-cc | `which ccs && which claude` | 跳过，标记 `skipped (CLI not found)` |
| glm-cc | `which ccs && which claude` | 跳过，标记 `skipped (CLI not found)` |

- enabled: false → 跳过该 reviewer
- 至少需要一个可用的 reviewer，否则报错退出（Claude 系列始终可用，所以这种情况不会发生）

### 4. 选择 Prompt 模板

**V2 变更：使用统一审查模板**

不再根据内容类型选择不同模板，统一使用 `unified-review.md`。

**模板位置：** `~/.claude/skills/super-review/prompts/unified-review.md`

**模板特点：**
- 三阶段审查：一致性审查 → UI 一致性审查 → 探索性审查
- 三评分：compliance_score + ui_compliance_score + quality_score
- 自适应：reviewer 根据内容类型自行判断适用的审查维度

**旧模板保留：** `prompts/` 目录下的其他模板暂时保留，用于特定类型审查。

**模板选择逻辑：**

| 审查内容类型 | 模板 |
|-------------|------|
| 代码/Commit | unified-review.md（三阶段审查）|
| PRD 文档 | prd.md |
| 教程文档 | beginner-tutorial.md 或 expert-tutorial-review.md |
| UI 设计稿（独立审查）| ui-compliance.md |

### 5. 创建结果目录

**目录命名格式：**
```
.reviews/<content_name>_<YYYYMMDD_HHMMSS>/
```

**命名规则：**
- 文件：使用文件名（去除路径和扩展名）
- 目录：使用目录名
- commit：使用 commit message 前 20 字符（中文友好）
- 多文件/多 commit：使用 "multi" 或主题词

**示例：**
```
.reviews/login_20251228_143052/
.reviews/src_auth_20251228_150000/
.reviews/fix_用户登录_20251228_160000/
```

**创建目录：**
```bash
review_dir=".reviews/${content_name}_$(date +%Y%m%d_%H%M%S)"
mkdir -p "$review_dir"
```

### 6. 并发启动 Reviewers

**⚠️ 关键理解：两类 Reviewer 使用不同的启动方式**

| Reviewer 类型 | 启动方式 | 工具 | 说明 |
|--------------|----------|------|------|
| **Claude 系列** (opus/sonnet/haiku) | Task tool | Claude Code 原生 subagent | 高效，无需 CLI |
| **外部 CLI** (codex/gemini) | Bash tool | 调用外部 CLI 命令 | 必须用 Bash |

**强制要求：所有 reviewer 必须在单个消息中并行启动！**

这意味着你需要在一个响应中同时发起：
- 多个 Task tool 调用（用于 Claude 系列）
- 多个 Bash tool 调用（用于 codex/gemini）

---

#### 6.1 Claude 系列 Reviewer（Task tool）

对于 claude-opus、claude-sonnet、claude-haiku，使用 **Task tool** 启动 subagent：

**Task tool 参数：**
```yaml
subagent_type: "general-purpose"
model: "opus"  # 或 "sonnet" / "haiku"
prompt: |
  你是一个代码审查专家，请对以下内容进行审查。

  ## 审查目标
  类型：{{review_target.type}}
  目标：{{review_target.value}}

  ## 审查范围
  目标：{{review_scope.objective}}
  包含：{{review_scope.includes}}
  不含：{{review_scope.excludes}}

  ## 项目文档路径（固定规范）
  - UI 设计规范: docs/design/ui-spec.md
  - 设计文档目录: docs/design/
  - 开发计划目录: docs/plans/

  ## 审查流程

  ### Phase 1: 一致性审查
  参照 docs/design/ 或 docs/plans/ 中的设计文档：
  1. 读取相关设计文档，提取设计要点清单
  2. 逐项核对实现情况（✅已实现 / ❌未实现 / ⏭️跳过 / ⚠️有偏差）
  3. 输出 compliance_score (0-10) 及理由

  如果没有相关设计文档，跳过此阶段，compliance_score 为 N/A。

  ### Phase 2: UI 一致性审查
  如果变更涉及前端文件（.tsx, .jsx, .vue, .css, .scss 等），执行此阶段：
  1. 读取 docs/design/ui-spec.md
  2. 严格核对前端代码是否符合 UI 设计规范
  3. 输出 ui_compliance_score (0-10) 及理由

  如果无前端文件变更或 ui-spec.md 不存在，跳过此阶段。

  ### Phase 3: 探索性审查
  在审查范围内自由发现问题：
  - **必查**：正确性、安全性
  - **可选**：性能、可维护性、边界情况

  输出 quality_score (0-10) 及理由。

  ## 输出要求
  1. 按 P0/P1/P2 分级列出问题
  2. 给出三个评分
  3. 将完整结果写入：{{output_file}}
```

**Subagent 执行步骤：**
1. 根据 review_target 获取内容（git show / 读取文件）
2. 自主研究项目结构、理解代码上下文
3. 检查固定文档路径下的设计文档和 UI 规范
4. 执行三阶段审查
5. 将结果写入 output_file
6. 返回执行状态

---

#### 6.2 外部 CLI Reviewer（Bash tool）

对于 codex、gemini 等外部工具，使用 **Bash tool** 调用 CLI：

**Codex CLI 调用：**
```bash
# 先准备 diff 内容
git diff HEAD~2..HEAD > /tmp/diff_content.txt

# 调用 codex（后台运行，避免阻塞）
codex --quiet "Review the following code changes. List issues by P0/P1/P2 priority. Give a quality_score (0-10).

$(cat /tmp/diff_content.txt)" > .reviews/xxx/review_codex.md 2>&1
```

**Gemini CLI 调用：**
```bash
# 调用 gemini
gemini "你是代码审查专家。请审查以下代码变更，按 P0/P1/P2 分级列出问题，给出 quality_score (0-10)。

变更内容：
$(git diff HEAD~2..HEAD)" > .reviews/xxx/review_gemini.md 2>&1
```

**Gemini-CC CLI 调用（ccs gemini + claude -p）：**
```bash
# 使用 ccs 切换到 gemini 上下文，然后通过 claude -p 执行审查
# ccs gemini 会设置 ANTHROPIC_API_KEY 等环境变量指向 gemini 兼容的 API
$(ccs gemini) && claude -p \
  --dangerously-skip-permissions \
  "你是代码审查专家。请审查以下代码变更，按 P0/P1/P2 分级列出问题，给出 quality_score (0-10)。

变更内容：
$(git diff HEAD~2..HEAD)" > .reviews/xxx/review_gemini-cc.md 2>&1
```

**GLM-CC CLI 调用（ccs glm + claude -p）：**
```bash
# 使用 ccs 切换到 glm 上下文，然后通过 claude -p 执行审查
# ccs glm 会设置 ANTHROPIC_API_KEY 等环境变量指向 glm 兼容的 API
$(ccs glm) && claude -p \
  --dangerously-skip-permissions \
  "你是代码审查专家。请审查以下代码变更，按 P0/P1/P2 分级列出问题，给出 quality_score (0-10)。

变更内容：
$(git diff HEAD~2..HEAD)" > .reviews/xxx/review_glm-cc.md 2>&1
```

**CLI 调用注意事项：**
- 使用 `> file 2>&1` 捕获所有输出
- 对于长时间运行的命令，考虑使用 `timeout` 包裹
- 检查 exit code 判断是否成功

---

#### 6.3 并行启动示例（完整）

**你必须在单个消息中同时发起所有调用：**

```
# 在同一个响应中，同时发起以下调用：

## Task tool 调用 1: Claude Opus
subagent_type: "general-purpose"
model: "opus"
prompt: [审查 prompt，输出到 review_claude-opus.md]

## Task tool 调用 2: Claude Sonnet
subagent_type: "general-purpose"
model: "sonnet"
prompt: [审查 prompt，输出到 review_claude-sonnet.md]

## Task tool 调用 3: Claude Haiku
subagent_type: "general-purpose"
model: "haiku"
prompt: [审查 prompt，输出到 review_claude-haiku.md]

## Bash tool 调用 1: Codex
command: codex --quiet "[prompt]" > .reviews/xxx/review_codex.md 2>&1

## Bash tool 调用 2: Gemini
command: gemini "[prompt]" > .reviews/xxx/review_gemini.md 2>&1

## Bash tool 调用 3: Gemini-CC
command: $(ccs gemini) && claude -p --dangerously-skip-permissions "[prompt]" > .reviews/xxx/review_gemini-cc.md 2>&1

## Bash tool 调用 4: GLM-CC
command: $(ccs glm) && claude -p --dangerously-skip-permissions "[prompt]" > .reviews/xxx/review_glm-cc.md 2>&1
```

**错误示范（不要这样做）：**
```
❌ 先启动 Claude opus，等待完成
❌ 再启动 Claude sonnet，等待完成
❌ 最后启动 codex 和 gemini
```

**正确示范：**
```
✅ 在单个消息中同时发起 7 个 tool 调用（3 个 Task + 4 个 Bash）
✅ 所有 reviewer 并行执行
✅ 等待所有完成后汇总
```

---

#### 6.4 跳过不可用的 Reviewer

根据 3.5 节的 CLI 检查结果，跳过不可用的 reviewer：

```python
# 伪代码
available_reviewers = []

if claude_available:
    available_reviewers.extend(['claude-opus', 'claude-sonnet', 'claude-haiku'])

if codex_available:
    available_reviewers.append('codex')

if gemini_available:
    available_reviewers.append('gemini')

# 只启动可用的 reviewer
for reviewer in available_reviewers:
    launch_reviewer(reviewer)
```

在最终输出中标记跳过原因：
```
| codex | ⏭️ skipped | - | CLI not found |
```

### 7. 汇总结果

等待所有 subagent 完成后，读取所有 `review_*.md` 文件进行汇总。

**汇总步骤：**

1. **读取结果**：读取 `.reviews/<dir>/review_*.md` 所有文件
2. **提取评分**：
   - 从每个结果中提取 `compliance_score` 及理由
   - 从每个结果中提取 `ui_compliance_score` 及理由
   - 从每个结果中提取 `quality_score` 及理由
3. **去重**：识别多个 reviewer 提出的相同/相似问题，合并为一条
4. **分级**：按 P0/P1/P2 分类所有问题
5. **过滤**：只保留 P0 和 P1 问题
6. **评分**：对每个 reviewer 的审查质量打 `reviewer_score`

**评分汇总格式：**

```markdown
| Reviewer | Compliance | UI Compliance | Quality | Reviewer Score |
|----------|------------|---------------|---------|----------------|
| opus     | 8/10       | 7/10          | 7/10    | 8.5/10         |
| sonnet   | 9/10       | 8/10          | 6/10    | 7.0/10         |
| codex    | N/A        | N/A           | 5/10    | 6.5/10         |
| gemini   | 7/10       | N/A           | 8/10    | 6.0/10         |

**平均分**
- compliance_score: 8.0/10 (3/4 reviewers)
- ui_compliance_score: 7.5/10 (2/4 reviewers)
- quality_score: 6.5/10 (4/4 reviewers)
```

**reviewer_score 评分标准：**

| 分数 | 标准 |
|-----|------|
| 9-10 | 分析极其深入，问题发现全面，建议具体可执行 |
| 7-8 | 分析到位，问题识别准确，建议有价值 |
| 5-6 | 基本完成审查，但深度或建议质量一般 |
| 3-4 | 审查不够深入或遗漏重要问题 |
| 1-2 | 未能完成有效审查或输出异常 |
| 0 | 超时或执行失败 |

**生成文件：**
- `summary.md`：汇总报告，包含评分总览和 P0/P1 问题列表
- `meta.json`：元数据，包含所有评分和统计信息

### 8. 写入 meta.json

**meta.json 完整结构：**

```json
{
  "id": "login_20251228_143052",
  "timestamp": "2025-12-28T14:30:52+08:00",

  "review_target": {
    "type": "commits",
    "value": "HEAD~3..HEAD"
  },

  "review_scope": {
    "objective": "实现用户登录 API",
    "includes": ["登录 API 端点", "JWT 验证中间件"],
    "excludes": ["密码找回", "邮箱验证"],
    "reference_docs": ["docs/design/auth-design.md"]
  },

  "fixed_paths": {
    "ui_spec": "docs/design/ui-spec.md",
    "design_dir": "docs/design/",
    "plans_dir": "docs/plans/"
  },

  "execution_mode": "confirm",

  "reviewers": {
    "claude-opus": {
      "status": "success",
      "duration_seconds": 45.2,
      "compliance_score": 8.0,
      "compliance_score_reason": "设计要点 8/10 已实现",
      "ui_compliance_score": 7.0,
      "ui_compliance_score_reason": "色彩系统正确，间距有 2 处违规",
      "quality_score": 7.5,
      "quality_score_reason": "代码结构清晰，但缺少错误处理",
      "reviewer_score": 8.5,
      "reviewer_score_reason": "分析全面，建议具体可执行"
    },
    "gemini": {
      "status": "success",
      "duration_seconds": 38.1,
      "compliance_score": null,
      "compliance_score_reason": "跳过一致性审查（未找到参照文档）",
      "ui_compliance_score": null,
      "ui_compliance_score_reason": "跳过 UI 审查（无前端变更）",
      "quality_score": 8.0,
      "quality_score_reason": "整体质量良好",
      "reviewer_score": 6.5,
      "reviewer_score_reason": "发现问题较少"
    }
  },

  "summary": {
    "total_reviewers": 4,
    "success_count": 4,
    "failed_count": 0,
    "timeout_count": 0,
    "skipped_count": 0,
    "avg_compliance_score": 8.0,
    "avg_ui_compliance_score": 7.5,
    "avg_quality_score": 7.0,
    "p0_issues": 1,
    "p1_issues": 3
  }
}
```

**字段说明：**

| 字段 | 说明 |
|------|------|
| `review_scope` | 新增，记录推断/注入的审查范围 |
| `fixed_paths` | 新增，记录固定文档路径 |
| `execution_mode` | 新增，记录执行模式 (confirm/skip-confirm/context-injected) |
| `compliance_score` | 新增，一致性评分 (可为 null) |
| `ui_compliance_score` | 新增，UI 一致性评分 (可为 null) |
| `quality_score` | 替换原 content_score |

**status 取值：**
- `success`：审查成功完成
- `failed`：执行出错
- `timeout`：超时未完成
- `skipped`：CLI 工具不存在或 reviewer 被禁用

### 9. 输出结果

审查完成后，向用户展示：

**执行状态汇总：**

```
## 审查完成

| Reviewer | 状态 | 耗时 | Compliance | UI Compliance | Quality |
|----------|------|------|------------|---------------|---------|
| claude-opus | ✅ success | 45.2s | 8/10 | 7/10 | 7.5/10 |
| claude-sonnet | ✅ success | 32.1s | 9/10 | 8/10 | 6/10 |
| codex | ⏭️ skipped | - | - | - | - |
| gemini | ✅ success | 38.1s | N/A | N/A | 8/10 |

**平均分**
- compliance_score: 8.5/10 (2/3 reviewers)
- ui_compliance_score: 7.5/10 (2/3 reviewers)
- quality_score: 7.2/10 (3/3 reviewers)

注：codex 跳过原因：CLI not found
```

**P0/P1 问题汇总：**

```
## P0 致命问题（共 1 个）

1. **SQL 注入风险** [claude-opus, claude-sonnet]
   - 位置：`src/auth/login.py:45`
   - 影响：攻击者可执行任意 SQL
   - 建议：使用参数化查询

## P1 严重问题（共 3 个）

1. **缺少输入验证** [claude-opus, gemini]
   ...
```

**结果目录：**

```
结果已保存到：.reviews/login_20251228_143052/
```

---

## 使用示例

### 示例 1：审查指定文件

用户：`super-review src/auth/login.py`

执行：
1. 确定审查内容：`src/auth/login.py`
2. 收集背景：从用户 prompt 和项目文档提取
3. 选择模板：backend-code.md（.py 文件）
4. 启动 reviewers 并汇总

### 示例 2：审查最近 commit

用户：`超级审查最近三个 commit`

执行：
1. 确定审查内容：`HEAD~3..HEAD`
2. 收集背景：从 commit messages 提取
3. 选择模板：根据涉及文件类型选择
4. 启动 reviewers 并汇总

### 示例 3：智能推断

用户：`并行审查`（无指定内容）

执行：
1. 检测 staged changes → 询问确认
2. 如无 staged，检测最近 commit → 询问确认
3. 用户确认后继续流程

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zuozuo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
