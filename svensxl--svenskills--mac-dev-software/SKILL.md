---
name: mac-dev-software
description: 在 macOS 上一键安装常用 AI coding agent 和开发工具。当用户需要配置 macOS 开发环境、安装 Homebrew、iTerm2、Node.js、NVM、Maven、OpenJDK、OpenCode、Codex CLI、Claude Code 等工具时使用此 skill。 Use when this capability is needed.
metadata:
  author: svensxl
---

# macOS AI Coding Agent 及开发工具安装指南

在 macOS 上安装常用的 AI coding agent 和开发环境工具，安装前自动检测已有软件并跳过。

## 安装软件清单

| 软件             | 说明                        |
| ---------------- | --------------------------- |
| Homebrew         | macOS 包管理器              |
| iTerm2 + AI 插件 | 增强型终端 + AI 辅助插件    |
| Node.js          | JavaScript 运行时           |
| NVM              | Node 版本管理器             |
| Maven            | Java 项目构建工具           |
| OpenJDK 21       | Java 21 开发环境            |
| OpenCode         | AI coding agent             |
| Codex CLI        | OpenAI Codex 命令行工具     |
| Claude Code      | Anthropic Claude 命令行工具 |

## 安装流程

### 一键安装

运行 skill 自带的安装脚本：

```bash
bash scripts/install.sh
```

脚本会自动完成以下所有步骤。每个步骤前会检测是否已安装，已安装则跳过。

### 手动安装步骤

#### 步骤 1：安装 Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

#### 步骤 2：安装 iTerm2 及 AI 插件

```bash
brew install --cask iterm2
```

下载并安装 AI 插件：

```bash
curl -fsSL -o /tmp/iTermAI.zip \
  "https://github.com/gnachman/iterm2-website/raw/refs/heads/master/downloads/ai-plugin/iTermAI-1.1.zip"
unzip -q /tmp/iTermAI.zip -d /tmp
mv /tmp/iTermAI.app /Applications/
```

#### 步骤 3：安装 Node.js 及 NVM

```bash
brew install node
brew install nvm
```

安装 NVM 后需按照 brew 输出的提示配置环境变量。

#### 步骤 4：安装 Maven 和 OpenJDK 21

```bash
brew install mvn
brew install openjdk@21
```

将 Java 命令加入 PATH：

```bash
echo 'export PATH="/usr/local/opt/openjdk@21/bin:$PATH"' >> ~/.zshrc
```

> **注意**：Apple Silicon Mac 上路径为 `/opt/homebrew/opt/openjdk@21/bin`，脚本会自动适配。

#### 步骤 5：安装 OpenCode

```bash
curl -fsSL https://opencode.ai/install | bash
```

#### 步骤 6：安装 Codex CLI

```bash
npm i -g @openai/codex
```

#### 步骤 7：安装 Claude Code

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

## 注意事项

- 脚本使用 `set -euo pipefail` 严格模式，遇到任何错误会立即停止
- Apple Silicon（M1/M2/M3）和 Intel Mac 的 Homebrew 安装路径不同，脚本会自动检测并适配
- 安装 Codex CLI 需要先安装 Node.js 和 npm

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/svensxl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
