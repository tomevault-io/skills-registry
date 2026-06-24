---
name: docs
description: >- Use when this capability is needed.
metadata:
  author: lyrixi
---

# docs

生成或修改使用 **lyrixi-mobile** 组件/工具的代码时，**默认以 `.ai/docs` 为准**；仅当文档缺失或未覆盖时再查 `src/components`、`src/utils` 或 `.cursorrules`。

**不要**一次性读完整 `mapping.json` 以外的全部 docs；用 **mapping 索引 + 按需精读** 控制上下文。

## 何时启用

- 用户显式使用本技能（`docs`），或要求「按文档 / 按库规范」写 UI
- 选用、组合组件（表单、列表、弹窗、导航等）或工具（Request、DateUtil、DOMUtil 等）
- 需要 props、子组件名、示例写法，且希望减少编造 API

## 检索流程（必须按序）

### 1. 提取检索词

从用户任务归纳英文组件名、中文场景词、子组件（如 `Input.Text`、`DatePicker.Combo`）、工具名。

### 2. 解析索引（优先脚本，省 token）

在仓库根目录执行：

```bash
node .ai/skills/docs/scripts/resolve-docs.mjs --query "按钮,列表,Request"
```

或无 `--query`，直接跟关键词：

```bash
node .ai/skills/docs/scripts/resolve-docs.mjs 加载 弹窗 Form
```

- 解析 `hits`：按 `score` 从高到低取相关项（通常 1～5 个即可，避免贪多）
- **无命中**：再 `Read` [`.ai/docs/mapping.json`](../../docs/mapping.json)，对 `keywords` / `name` 做人工匹配；仍无则进入「回退」

### 3. 按需读取文档（渐进披露）

对每个命中项，**默认只读**：

1. `{name}-props.ts` — API / Ref（生成代码的主要依据）
2. `{name}-rules.md` — 何时使用、子组件、禁止自造组件

**仅当**需要具体写法或 demo 片段时：

1. 先读 `{name}-example.md` 的 **Demo 索引表**（薄索引，不含完整代码）
2. 再 **Read** 表中链接的 `demos/*.tsx`（示例源码唯一来源）

路径以 `resolve-docs` 输出为准（相对仓库根，如 `.ai/docs/components/Button/Button-props.ts`）。

维护示例：同步 `src/**/demos` 后运行 `node .ai/skills/docs/scripts/generate-example-index.mjs [组件名]` 刷新索引。

### 4. 生成代码

- 组件/工具从 `lyrixi-mobile` barrel 引入（业务代码）；内库改动用相对路径（见 `.cursorrules`）
- Props、子组件名、默认值 **必须与 props.ts 一致**，禁止编造未出现的 API
- 遵守 `.ai/rules/`（命名、import 顺序、页面结构等）

### 5. 回退（文档未覆盖时）

| 情况 | 做法 |
|------|------|
| mapping 无条目 | 查 `src/components/{Name}/index.zh-CN.md` 或 `src/utils/{Name}/` |
| props 与实现不一致 | 以 `src` 源码为准；需同步时直接改 `.ai/docs` 或 `mapping.json` |
| 页面级模板 | 另用 [create-page](../create-page/SKILL.md) + `pages/catalog.json` |
| 新建库组件 | 另用 [add-component](../add-component/SKILL.md) + `reference/catalog.json` |

## mapping.json 与 keywords

- 索引文件：[`.ai/docs/mapping.json`](../../docs/mapping.json)
- `keywords` 为 `|` 分隔，手工维护；检索不全时直接编辑对应条目的 `keywords` 或 `name`
- **keywords 不全会影响检索**：`resolve-docs.mjs` 为子串/相等打分，同义词未写入则易漏检

## 业务页面

整页骨架（列表/编辑/详情/报表）→ 使用 **create-page** 选 `pages/` 模板；新建库组件 → 使用 **add-component**；页面内或实现中引用的组件/工具仍按本技能查 `components/`、`utils/`。

## 输出自检

- [ ] 已用 mapping / resolve-docs 定位文档
- [ ] 已读对应 props（+ rules）
- [ ] 未使用 docs 中不存在的 props / 子组件
- [ ] 文档缺失处已说明回退依据

---
> Source: [lyrixi/lyrixi-mobile](https://github.com/lyrixi/lyrixi-mobile) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
