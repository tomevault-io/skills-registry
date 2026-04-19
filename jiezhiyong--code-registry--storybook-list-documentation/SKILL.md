---
name: storybook-list-documentation
description: 列出 Storybook manifests 中的全部组件/文档条目（等价于 list-all-documentation），用于组件发现、盘点与测试覆盖率梳理。 Use when this capability is needed.
metadata:
  author: jiezhiyong
---

# Storybook：列出全部文档（基于 manifests）

本 Skill **不依赖 MCP Server**。它通过读取 `storybook-static/manifests/components.json`（本地或远程）列出组件库中所有可用的文档条目，并输出一个便于浏览的 Markdown 索引。

该 Skill 的目标是替代 `@storybook/mcp` 的 docs 工具：`list-all-documentation`。

## When to Use

- 需要快速了解“组件库里有哪些组件/blocks/文档条目”时
- 需要做文档覆盖率盘点（哪些组件缺文档/缺 stories）时
- 在生成测试用例前，先枚举有哪些组件可测、有哪些关键 stories 可用时
- 在 agent 不确定该用哪个组件时，先让它列出候选条目再继续决策

## Instructions

### 配置 manifests 来源（优先级从高到低）

1. **命令行参数**：`--manifestsRoot <path|url>`（临时覆盖）
2. **环境变量**：`STORYBOOK_MANIFESTS_ROOT`（推荐用于业务项目配置远端 manifests）
3. **默认值**：`./storybook-static/manifests`（本地开发默认路径）

> 本仓库已提供读取脚本：`tools/storybook-manifest-skill.mjs`

### 执行步骤（必须按顺序）

1. **优先使用配置的 manifests 路径**

   - 如果已设置环境变量 `STORYBOOK_MANIFESTS_ROOT`，直接运行：

     ```bash
     node tools/storybook-manifest-skill.mjs list
     ```

   - 如果使用默认本地路径，运行：

     ```bash
     node tools/storybook-manifest-skill.mjs list
     ```

   - 如果需要临时覆盖路径，运行：

     ```bash
     node tools/storybook-manifest-skill.mjs list --manifestsRoot <path|url>
     ```

2. **若本地路径不存在/未产出 manifests，改用远程 manifestsRoot**

   - 方式一（推荐）：设置环境变量（适合业务项目长期配置）

     ```bash
     export STORYBOOK_MANIFESTS_ROOT=https://storybook.your-company.com/manifests
     node tools/storybook-manifest-skill.mjs list
     ```

   - 方式二：临时通过命令行参数指定

     ```bash
     node tools/storybook-manifest-skill.mjs list --manifestsRoot https://storybook.your-company.com/manifests
     ```

3. **把输出的 Markdown 索引作为结果返回**
   - 如果用户后续要“获取某个条目的详情”，提示他们使用条目里的 `id`，并切换到 Skill `storybook-get-documentation`。

### 输出要求（重要）

- 输出**必须包含**：条目 `name` 与 `id`
- 发现条目数量异常（例如 0 条）时，要提醒用户：
  - 是否启用了 `experimentalComponentsManifest`
  - 是否执行过 `storybook build` 并生成了 `storybook-static`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jiezhiyong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
