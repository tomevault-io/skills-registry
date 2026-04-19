---
name: storybook-get-documentation
description: 从 Storybook manifests 中获取指定组件/文档条目的详情（等价于 get-documentation），用于写文档、写测试与排查用法。 Use when this capability is needed.
metadata:
  author: jiezhiyong
---

# Storybook：获取指定文档（基于 manifests）

本 Skill **不依赖 MCP Server**。它通过读取 `storybook-static/manifests/components.json`（本地或远程）来输出某个条目的“文档视图”（Markdown），包含：

- 组件/条目基本信息（`name`、`id`、story 文件路径）
- 建议 import 片段（若 manifests 提供）
- stories 列表与 snippet（若 manifests 提供）
- 部分 props 信息（若 manifests 提供 react-docgen）

该 Skill 的目标是替代 `@storybook/mcp` 的 docs 工具：`get-documentation`。

## When to Use

- 用户给了某个组件/条目 `id`，需要拉取它的文档详情时
- 需要从“权威文档/示例”出发编写测试用例、补齐 stories、或更新 README/MDX 时
- agent 需要确认某个组件有哪些关键状态（stories）与示例代码（snippet）时

## Instructions

### 前置条件（必须满足其一）

- 你已经知道目标条目的 `id`（例如 `component-button`）
- 如果不知道 `id`：先使用 Skill `storybook-list-documentation` 列出全部条目，再从列表里选择

### 配置 manifests 来源（优先级从高到低）

1. **命令行参数**：`--manifestsRoot <path|url>`（临时覆盖）
2. **环境变量**：`STORYBOOK_MANIFESTS_ROOT`（推荐用于业务项目配置远端 manifests）
3. **默认值**：`./storybook-static/manifests`（本地开发默认路径）

### 执行步骤（必须按顺序）

1. **优先使用配置的 manifests 路径并获取文档**

   - 如果已设置环境变量 `STORYBOOK_MANIFESTS_ROOT`，直接运行（把 `<ID>` 替换为目标 id）：

     ```bash
     node tools/storybook-manifest-skill.mjs get --id <ID>
     ```

   - 如果使用默认本地路径，运行：

     ```bash
     node tools/storybook-manifest-skill.mjs get --id <ID>
     ```

   - 如果需要临时覆盖路径，运行：

     ```bash
     node tools/storybook-manifest-skill.mjs get --id <ID> --manifestsRoot <path|url>
     ```

2. **若本地路径不存在/未产出 manifests，改用远程 manifestsRoot**

   - 方式一（推荐）：设置环境变量（适合业务项目长期配置）

     ```bash
     export STORYBOOK_MANIFESTS_ROOT=https://storybook.your-company.com/manifests
     node tools/storybook-manifest-skill.mjs get --id <ID>
     ```

   - 方式二：临时通过命令行参数指定

     ```bash
     node tools/storybook-manifest-skill.mjs get --id <ID> --manifestsRoot https://storybook.your-company.com/manifests
     ```

3. **把输出的 Markdown 文档作为结果返回**
   - 若用户要生成测试：从输出中的 `Stories`（以及 snippet）提取关键状态作为测试用例起点

### 失败处理（必须做）

- 报错“未找到 id”：说明 id 不存在或 manifests 不是同一套组件库
  - 解决：先运行 `storybook-list-documentation` 确认可用 id
- 输出信息缺失（例如没有 props / 没有 snippet）：说明 manifests 当前只包含部分信息
  - 解决：以现有信息继续，但不要凭空编造 props；必要时建议用户补充/检查 Storybook 的 manifests 生成能力

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jiezhiyong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
