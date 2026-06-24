---
name: vibe-manifest-generator
description: 自动分析项目结构并生成适配 Cursor, Claude Code, Windsurf 等主流 Vibe Coding 软件的 Manifest 配置文件（CLAUDE.md, AGENTS.md, .cursorrules）。使用场景：初始化项目、迁移 Vibe Coding 配置、优化 AI Agent 项目上下文。 Use when this capability is needed.
metadata:
  author: rinbarpen
---

# Vibe Manifest Generator

该技能旨在帮助开发者快速为现有项目构建 "Vibe Coding" 环境，使 AI Agent（如 Cursor, Claude Code）能够立即理解项目架构、运行命令和开发规范。

## 工作流 (Workflow)

当用户要求 "setup vibe coding", "initialize manifest" 或 "configure cursor/claude" 时，执行以下步骤：

### 1. 项目分析 (Analysis)
- **识别语言**: 检查 `package.json` (JS/TS), `pyproject.toml/requirements.txt` (Python), `go.mod` (Go), `Cargo.toml` (Rust) 等。
- **识别包管理器**: `npm/pnpm/yarn`, `uv/pip/conda`, `go`, `cargo`。
- **识别测试框架**: `jest/vitest`, `pytest`, `go test`。
- **提取命令**: 从 `scripts` 或 `Makefile` 中提取构建、测试、格式化命令。
- **识别架构**: 扫描目录结构（如 `src/`, `lib/`, `cmd/`, `tests/` 或 `chapters/`, `outline/`, `characters/`）。

### 2. 配置文件生成 (Generation)
根据分析结果，生成以下文件：

- **`CLAUDE.md`**: 核心上下文文件，包含常用命令、架构说明和开发注意事项（支持通用开发与小说创作场景）。
- **`AGENTS.md`**: 专门针对 AI Agent 的指令，定义开发流程（Plan -> Implement -> Verify）或创作流程（Ideate -> Outline -> Draft -> Polish）。
- **`.cursorrules` / `.cursor/rules/*.mdc`**: 适配 Cursor 的规则，包含环境管理、代码风格或文学创作风格的约束。

### 3. 软件适配 (Software Adaptation)
- **Cursor**: 生成 `.cursorrules` 或 `.cursor/rules/` 下的 `.mdc` 文件。
- **Claude Code**: 确保 `CLAUDE.md` 格式符合 A 级标准，包含详细的命令表格。
- **Windsurf**: 确保配置文件能被 Memex/Context 引擎有效解析。

## 核心场景 (Core Scenarios)

- **软件开发**: 默认场景，侧重代码结构、测试与部署。
- **小说创作**: 侧重人设一致性、剧情逻辑、世界观管理。

## 核心指令 (Core Instructions)

1. **优先提取**: 始终优先从现有文档（README.md）和配置文件中提取信息，不要凭空猜测。
2. **简洁明了**: 生成的配置文件应保持简洁，避免冗余的解释。
3. **验证驱动**: 在 `AGENTS.md` 中强制要求 Agent 在完成任务前运行 `ReadLints` 或进行逻辑校验。

## 参考模板 (Reference Templates)

- [CLAUDE.md](CLAUDE.md)
- [AGENTS.md](AGENTS.md)
- [.cursorrules](.cursorrules)
- [CLAUDE.novel.md](../../../manifests/novel-writing/CLAUDE.md)
- [AGENTS.novel.md](../../../manifests/novel-writing/AGENTS.md)
- [.cursorrules.novel](../../../manifests/novel-writing/.cursorrules)

## 辅助脚本 (Helper Scripts)

使用 `scripts/vibe-gen.sh` 快速将生成的配置应用到项目根目录。

---
> Source: [rinbarpen/vibe-coding](https://github.com/rinbarpen/vibe-coding) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
