---
name: checkstyle-fixer
description: 在提交前检查 git diff 文件的 Checkstyle 违规，生成带时间戳报告并规划可自动修复的问题。 Use when this capability is needed.
metadata:
  author: baobaoyeye
---

# Checkstyle Fixer

本技能用于在代码提交前，基于当前 git diff 中涉及的文件运行 Checkstyle 规范检查，生成带时间戳的报告，并根据问题清单给出修复计划，支持自动修复部分问题。

## 能力范围

1.  **Diff 分析**：识别本次变更中涉及的文件（包含 staged 与 unstaged）。
2.  **规范检查**：使用本 Skill 内置的 Checkstyle 工具对变更文件进行检查。
3.  **报告生成**：在 `checkstyle_results` 目录下生成带时间戳命名的摘要报告和问题清单。
4.  **修复计划**：基于问题清单生成可执行的修复方案。
5.  **交互式修复**：由研发人员确认后，自动对可修复问题进行代码修改。

## 使用说明

### 触发时机示例（研发可直接输入的提示词）
- “检查本次 diff 的 checkstyle”
- “提交前帮我跑一下 checkstyle 并生成报告”
- “根据 google_checks 规则检查并自动修复能修的”
- ”调用csf技能“

当本技能被调用时：

### 步骤 1：分析变更文件
- 运行 `git diff --name-only`（以及 `git diff --cached --name-only`）获取本次变更涉及的文件列表。
- 过滤出需要进行 Checkstyle 检查的源码文件（例如 `.java`）。
- 在分析和后续检查过程中，应遵守 `.gitignore` 中的忽略规则，对被 git 忽略的目录和文件不做检查。

### 步骤 2：运行 Checkstyle
- 使用本 Skill 所在目录下的 `tools` 子目录（例如 `.trae/skills/checkstyle-fixer/tools`），该目录对外部调用方透明，由 Skill 自行管理。
    - Checkstyle JAR：`tools/checkstyle-9.3-all.jar`
    - 规则配置：`tools/google_checks.xml`
- 需要检查步骤1中筛选出的全部变更文件，切记不要遗漏。
- 针对变更文件执行类似命令（示意）：
  - `java -jar tools/checkstyle-9.3-all.jar -c tools/google_checks.xml <file1> <file2> ...`

### 步骤 3：生成报告（带时间戳）
- 在项目根目录下创建 `checkstyle_results` 目录（若不存在）。
- 如果是首次创建该目录，应同时将 `checkstyle_results/` 加入项目根目录下的 `.gitignore`，避免报告文件在后续提交中成为脏数据。
- 解析 Checkstyle 输出，构建问题数据。
- 生成带时间戳命名的报告文件，例如：
  - 摘要报告：`checkstyle_results/YYYYMMDD_HHMMSS_summary_report.md`
  - 问题清单：`checkstyle_results/YYYYMMDD_HHMMSS_issue_list.md`，（注意，这里的问题清单要包括全部问题，而不是一部分问题） 
- 摘要报告包含：检查的文件总数、问题总数、按严重级别的统计等信息，报告正文使用中文描述（除代码片段与路径外）。
- 问题清单包含：文件路径、行号、严重级别、问题描述等信息，问题文案和说明同样使用中文表述（除规则名、代码片段等保留英文外）。

### 步骤 4：生成修复计划
- 基于最新生成的 `issue_list_YYYYMMDD_HHMMSS.md` 分析问题。
- 识别可自动修复的问题类型，例如：
  - 简单格式问题（缩进、空格等）。
  - 未使用的 import。
  - 明显可重命名的标识符（若能安全推断）。
- 生成修复计划，包含：
  - 可自动修复的问题列表（按文件/行号列出）。
  - 需要人工判断或复杂修改的问题列表。
- 向研发人员展示修复计划，并询问：
  - “请确认要自动修复的项（按文件/规则/全部等维度）。”
 - 修复计划中的所有描述性文字和清单说明需使用中文（除代码与规则标识外），并尽量引用 Checkstyle 报告中的规则名和约束含义，避免主观想象式的修改建议。

### 步骤 5：执行自动修复
- 在收到研发人员确认后，对选中的可自动修复问题：
  - 使用代码编辑能力对相关文件进行精准修改（例如删除未使用 import、调整格式等）。
- 自动修复时应严格依据 `tools/google_checks.xml` 中的规则和约束，只做为满足对应 Checkstyle 规则所必需的最小修改，避免与规则无关的“天马行空”式重构或风格改动。
- 修改完成后，可选择性地对受影响文件重新运行 Checkstyle：
  - 再次生成一组带时间戳的报告，用于对比修复前后结果。
- 所有报告均保留在 `checkstyle_results` 目录下，方便审计与回溯。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/baobaoyeye) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
