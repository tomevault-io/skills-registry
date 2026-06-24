---
name: app-flight-agents
description: App Flight 系列的第 6 步（最后一步）——基于已经生成的 5 份文档（BRIEF / PRD / DESIGN / ARCHITECTURE / CONTENT）自动生成 AGENTS.md（Codex / Claude 入口文档）+ CLAUDE.md（一句话指向 AGENTS.md）。完成后用户在 Xcode 中创建项目并把文档放入，Codex 或 Claude 打开就能立即理解项目并开始写代码。当用户已经走完 CONTENT.md 准备进入最后一步、或者说"帮我生成 AGENTS.md / 让 AI 能上手这个项目 / wrap up the App Flight"时，请务必使用这个 skill。这是 App Flight 流程的第 6 步（BRIEF → PRD → DESIGN → ARCHITECTURE → CONTENT → **AGENTS**）。 Use when this capability is needed.
metadata:
  author: CHANCYstone
---

# App Flight · AGENTS

帮用户产出 `AGENTS.md`（+ 一行的 `CLAUDE.md`）——**这是 App Flight 流程的最后一步，把前面 5 份文档串起来给 AI 用**。

## AGENTS.md 是什么

**AGENTS.md 是 Codex / Claude 进入项目的入口文档**。当用户把 Xcode 项目目录交给 Codex 或 Claude 时，agent 应该首先读 AGENTS.md，理解项目结构、读其他文档的顺序、写代码时该遵循什么规则。

沿用 AGENTS.md 文件名约定：
- 项目根目录的 AGENTS.md = AI 入口
- CLAUDE.md = 一行内容指向 AGENTS.md（保持 Claude Code 的优先识别）

## 与其他文档的分界

- **AGENTS.md**（这个 skill 的输出）：项目简介 + 文档清单 + 阅读顺序 + 写代码时的核心规则 + 工具链命令
- **CLAUDE.md**（同时生成）：一行 `本项目以 AGENTS.md 为准。请先读 AGENTS.md。`
- **AGENTS.md 不是**：另一份 BRIEF 或 PRD（不要重复前 5 份的内容）；它**只是地图**，告诉 AI 去哪儿找信息

## 这一步几乎不需要新提问

到这一步，用户已经回答了所有实质问题。AGENTS.md 的内容**几乎完全可以从前 5 份文档机械生成**——不需要再批量提问。

只需要问 **1 个轻量问题**：

> AGENTS.md 是 AI 进入项目的入口。我会基于你之前的 5 份文档自动生成。在生成前，你有没有特别想强调的规则？例如：
> - "禁止在 BRIEF 边界之外发挥"
> - "所有新功能必须先更新 PRD 再写代码"
> - "提交前必须跑 swift build + SwiftLint"
> - "中文回复，代码注释用英文"
>
> 没有的话我直接生成。

记下用户的额外规则（可能 0-5 条），写进 AGENTS.md 的"协作规则"章节。

## 整体流程

```
Step 0   读 BRIEF + PRD + DESIGN + ARCHITECTURE + CONTENT（全部 5 份）
   ↓
Step 1   问用户 1 个轻量问题：有没有特别想强调的协作规则
   ↓
Step 2   机械生成 AGENTS.md
   ↓
Step 3   生成 CLAUDE.md（一行内容）
   ↓
Step 4   预览 → 写文件 → 完结庆祝 → 告诉用户"接下来做什么"
```

## AGENTS.md 模板

```markdown
# {项目名} · AGENTS

> 这份文档是 Codex / Claude 进入本项目的入口。拿到项目目录后，请先读完这份文档再开始工作。

## 项目一句话

{从 BRIEF §"一句话定义"复制}

## 文档地图

本项目由 App Flight 生成，文档分两类。**长期文档先读、迭代产物按需读**：

**长期文档（项目根，跨迭代共用）：**

| 文档 | 内容 | 字数 |
|---|---|---|
| [BRIEF.md](./BRIEF.md) | 项目长期纲领（本质、用户、价值、边界） | ~{字数} |
| [DESIGN.md](./DESIGN.md) | 视觉与 UX 风格（Apple HIG 标准 + 扩展） | ~{字数} |
| [ARCHITECTURE.md](./ARCHITECTURE.md) | 技术栈、代码组织、部署 | ~{字数} |
| [AGENTS.md](./AGENTS.md) | 本文件——AI 入口 + 协作规则 | - |

**迭代产物（`iterations/v{N}-{slug}/`）：**

| 文件 | 内容 |
|---|---|
| `iterations/v1-launch/PRD.md` | 首版功能、屏幕结构、目标衡量 |
| `iterations/v1-launch/CONTENT.md` | 首版内容（仅 v1） |
| `iterations/v{N}-{slug}/.plan/` | 各迭代的 plan + phase 实施目录 |

## 项目本质边界（来自 BRIEF）

**永远会是的**：
- {复制自 BRIEF §本质边界}

**永远不做的**：
- {复制自 BRIEF §本质边界}

⚠️ 任何看似偏离这些边界的请求，请先停下来和用户确认；不要默认接受。

## 技术栈一句话（来自 ARCHITECTURE）

SwiftUI + SwiftData + iOS 17+ + {其他来自 ARCHITECTURE §1 的关键技术}

详细决策见 [ARCHITECTURE.md](./ARCHITECTURE.md)。

## 写代码前的准备（来自 ARCHITECTURE §12 checklist）

如果还没做完这些，先停下来提醒用户：

- [ ] 创建 Xcode 项目（SwiftUI App 模板）并配置 Bundle ID
- [ ] 配置签名（Signing & Capabilities）+ Development Team
- [ ] 安装 SPM 依赖（见 ARCHITECTURE §5）
- [ ] 导入 DESIGN tokens（颜色 Asset Catalog / 字体配置）
- [ ] 准备好环境变量 / 配置文件（见 ARCHITECTURE §5）
- [ ] {其他 checklist 项}

## 写代码时的核心规则

### 必须遵循
1. **遵循 Apple HIG + DESIGN.md 的 Do's & Don'ts**——尤其 Don'ts 部分（如"不要自定义返回手势"、"不要使用非标准 Tab Bar"），不论看起来多自然都不能违反
2. **CONTENT.md 的三种模式**：
   - 📝 标记的文案——**原样使用**，不要替换或"优化"
   - 🤖 标记的需求——按需求 + DESIGN voice + BRIEF 语气生成，但生成完要主动给用户看一眼
   - 📦 标记的数据源——从指定路径读取，schema 见 ARCHITECTURE
3. **代码组织**——严格按 ARCHITECTURE §7 的目录结构
4. **不要引入未在 ARCHITECTURE 中列出的依赖**——如果发现需要新 SPM 包，先停下来和用户讨论、更新 ARCHITECTURE 再装
5. **复杂开发用 phase 管理**——改动 ≥ 5 步骤 / 跨多文件 / 跨多次会话时，**必须**触发 `app-flight-phases` skill，建立 `.plan/plan.md` + `.plan/phases/NN-*.md`，每个 phase 完成后停下让用户验收。**不要一口气做完**。phases skill 内有完整规则（状态符号、Evidence 强制、Gate 流程、三种例外处理、不能自行 commit / 跳过任务）。**首次开发和后续新功能都用同一套**。

### 编码风格
- Swift strict concurrency
- 类型名 PascalCase（`struct ContentView`、`class DataManager`）
- 属性 / 函数名 camelCase（`var userName`、`func fetchData()`）
- MVVM 架构模式（View / ViewModel / Model 分离）
- {其他来自 ARCHITECTURE §7 命名约定}

### Commit 前自检
- [ ] `swift build` 编译通过（零 error）
- [ ] SwiftLint 检查通过（零 warning）
- [ ] Xcode 零 warnings（包括 deprecation）
- [ ] Accessibility Audit 通过（VoiceOver 可用、Dynamic Type 适配）

### Spec Sync（按文档类型分类同步 —— 关键改动！）

**核心原则**：spec 是 source of truth，但**不同类型文档同步规则不同**。把更新归到对的地方。

#### 文档分两类（生成 AGENTS.md 时要解释清楚）

**长期文档**（根目录，跨迭代共用）：BRIEF / DESIGN / ARCHITECTURE / AGENTS / CLAUDE
**迭代产物**（`iterations/v{N}-{slug}/`，每次迭代一份）：PRD / CONTENT（仅 v1）/ `.plan/` 工作目录

#### 三种改动 → 三种处理

1. **改动影响长期文档** → 增量更新根目录文件（如主色调整 → DESIGN; 装新 SPM 包 → ARCHITECTURE）
2. **改动影响当前迭代** → 更新 `iterations/v{N}-{slug}/` 下文件（如当前迭代屏幕微调 → 当前迭代的 PRD.md）
3. **新需求 / 新功能** → **开新迭代**！不要动 `iterations/v1-launch/PRD.md`，触发 app-flight-prd skill 写新一份 `iterations/v{N+1}-{slug}/PRD.md`

#### 边界级变更

用户要做的事和 BRIEF "永远不做的" 冲突（如要加社交功能，BRIEF 说永远不做社交）：
- ⚠️ 停下，不是普通 Spec Sync 是 BRIEF 级变更
- 选项：要么更新 BRIEF 重审项目方向；要么收窄需求；不要默认接受

#### 判断原则

"下次 AI 看到这个项目时需要知道吗？" 是 → 进对应 spec；否 → 实现层面微调，不必。

### 用户特别强调的规则
{从 Step 1 收集的用户额外规则}

## 工具链常用命令

```bash
# 编译项目
swift build
xcodebuild -scheme {SchemeName} -destination 'platform=iOS Simulator,name=iPhone 16' build

# 运行测试
swift test
xcodebuild test -scheme {SchemeName} -destination 'platform=iOS Simulator,name=iPhone 16'

# SwiftLint 检查
swiftlint lint --strict

# 启动模拟器
xcrun simctl boot "iPhone 16"
open -a Simulator

# 安装到模拟器
xcrun simctl install booted {.app路径}

# 截屏（用于验收）
xcrun simctl io booted screenshot screenshot.png

# 部署（通过 Fastlane）
fastlane beta    # TestFlight
fastlane release # App Store

# {从 ARCHITECTURE §6 提取的其他部署命令}
```

## 在哪里找信息

| 问题 | 去哪查 |
|---|---|
| 这个项目最终长期要变成什么样？ | BRIEF.md |
| 第一版要做哪些屏幕 / 功能？ | PRD.md §屏幕结构 |
| 这个按钮 / 卡片应该长什么样？ | DESIGN.md §Components |
| 用什么颜色 / 字体？ | DESIGN.md YAML tokens |
| 用什么技术栈 / 库？ | ARCHITECTURE.md §3 |
| 这个区块应该放什么内容？ | CONTENT.md（按屏幕 / 区块 / 内容槽查） |
| 部署到哪？怎么部署？ | ARCHITECTURE.md §6（App Store Connect） |
| 性能 / 无障碍目标是多少？ | ARCHITECTURE.md §8 |

## 协作姿态

- **请向用户提问而不是默认假设**——App Flight 的设计原则是"不确定先问"
- **遇到 BRIEF 边界冲突要停下**——比"我自己判断"重要得多
- **改文档要更新对应的 spec 文件**——比如改了屏幕结构要回去更新 PRD，改了配色要更新 DESIGN
- **保持文档的"权威性"**——5 份文档是 source of truth，代码是文档的实现

## 这是用 App Flight 生成的

本项目的 6 份文档（BRIEF / PRD / DESIGN / ARCHITECTURE / CONTENT / AGENTS）由 App Flight skill 系列引导生成。如需重新走 App Flight 或了解整个流程，参考 App Flight 的父 skill 文档。
```

## CLAUDE.md 内容（一行）

```markdown
# CLAUDE.md

本项目以 [AGENTS.md](./AGENTS.md) 为准。请先读 AGENTS.md 再开始工作。
```

## Step 4：预览 + 写文件 + 完结

把 AGENTS.md 内容展示给用户预览：

> 这是基于你前面 5 份文档自动生成的 AGENTS.md。看一下，有没有要调整的？

确认后写两个文件：`./AGENTS.md` + `./CLAUDE.md`。

## Step 5：完结庆祝

```
> 🎉 App Flight 完成！
>
> 你的项目目录现在有了完整的 6 份文档：
> ```
> {项目名}/
> ├── BRIEF.md           长期纲领
> ├── PRD.md             首版功能 + 屏幕结构
> ├── DESIGN.md          视觉与 UX（Apple HIG 标准）
> ├── ARCHITECTURE.md    技术栈
> ├── CONTENT.md         每个内容槽的填充
> ├── AGENTS.md          AI 入口（请 AI 先读这个）
> └── CLAUDE.md          一行指向 AGENTS.md
> ```
>
> **接下来你要做的事**：
>
> 1. 在 Xcode 中创建新项目（SwiftUI App），把这些文档放到项目根目录
> 2. 跑 ARCHITECTURE §12 的 checklist（配置签名、安装 SPM 依赖、导入 Design tokens 等）
> 3. 配置 SwiftLint（`.swiftlint.yml`）
> 4. 把项目交给 Codex 或 Claude，让它读 AGENTS.md 然后开始写代码
>
> 如果在写代码过程中发现 spec 需要改，**回来更新对应的 .md 文件**——它们是 source of truth。
>
> 祝起飞顺利 ✈️
```

## 写作风格守则

- AGENTS.md 是**地图**，不是**内容**——绝对不重复其他文档已经说过的事
- 每条规则都要**可执行**——"遵循 DESIGN" 不够，"遵循 Apple HIG + DESIGN.md 的 Do's & Don'ts，尤其 Don'ts" 才够
- 对 AI 用**第二人称指令**（"请你 / 不要"），不要用"开发者应该"——AGENTS.md 是给 AI 看的
- 给出**具体命令**——`swift build` 比"编译项目"好

## 参考

- `assets/agents-template.md`——AGENTS.md 模板
- `assets/claude-template.md`——CLAUDE.md 一行模板
- `references/example-agents.md`——iOS App 完整 AGENTS 示例
- AGENTS.md 文件名约定——Codex / Claude 进入项目时的入口文档格式

---
> Source: [CHANCYstone/app-flight](https://github.com/CHANCYstone/app-flight) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
