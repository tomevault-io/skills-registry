---
name: implementing-secrets-scanning-in-ci-cd
description: 将 gitleaks 和 trufflehog 集成到 CI/CD 管道中，在部署前检测泄露的机密信息 Use when this capability is needed.
metadata:
  author: killvxk
---

## 概述

本技能涵盖使用 gitleaks 和 trufflehog 在 CI/CD 管道中实施自动化机密扫描。它使安全团队能够检测意外提交到源代码仓库的 API 密钥、令牌、密码和其他凭证，提供阻止包含高严重性发现的部署的 CI 门禁。

Gitleaks 使用正则表达式模式和熵分析扫描 git 仓库和目录中的硬编码机密。TruffleHog 对文件系统和 git 历史进行扫描，并可选择对实时服务进行机密验证。两者共同为机密检测提供全面覆盖。

## 前置条件

- Python 3.9 或更高版本
- 已安装 gitleaks v8.x 并在 PATH 中可用
- 已安装 trufflehog v3.x 并在 PATH 中可用
- 要扫描的 git 仓库或目录
- CI/CD 平台访问权限（GitHub Actions、GitLab CI、Jenkins）

## 步骤

1. **安装扫描工具**：通过包管理器或二进制下载安装 gitleaks。通过 `brew install trufflehog` 或从 GitHub releases 下载安装 trufflehog。

2. **配置 gitleaks**：在仓库根目录创建 `.gitleaks.toml` 配置文件，以定义自定义规则、允许列表和路径排除。使用 `--config` 标志指向自定义配置。

3. **运行 gitleaks 目录扫描**：执行 `gitleaks dir --source . --report-format json --report-path gitleaks-report.json` 以扫描工作目录并生成 JSON 报告。

4. **运行 trufflehog 文件系统扫描**：执行 `trufflehog filesystem /path/to/repo --json > trufflehog-report.json` 以扫描文件并将 JSON 发现输出到报告文件。

5. **解析和过滤发现**：使用代理脚本解析两个 JSON 报告，按严重性（严重、高危、中危、低危）过滤发现，并确定 CI 管道是否应通过或失败。

6. **集成到 CI 管道**：将扫描步骤添加到 GitHub Actions 工作流、GitLab CI 配置或 Jenkins 管道中，作为预部署门禁。使用 gitleaks 中的 `--exit-code` 标志控制管道行为。

7. **配置预提交钩子**：使用 `gitleaks protect --staged` 将 gitleaks 设置为预提交钩子，以在提交之前捕获机密。

8. **审查和分类发现**：检查 JSON 输出中的误报，将合法条目添加到 `.gitleaksignore`，并立即轮换任何已确认泄露的凭证。

## 预期输出

代理脚本生成包含以下内容的 JSON 报告：
- 每个扫描器的总发现数
- 按严重性级别分组的发现
- 包含文件路径、行号、规则 ID 和已编辑机密的单个发现详情
- 基于配置严重性阈值的 CI 门禁裁定（通过/失败）
- 包含扫描时长和工具版本的执行元数据

```json
{
  "scan_summary": {
    "tool": "both",
    "total_findings": 3,
    "critical": 1,
    "high": 1,
    "medium": 1,
    "low": 0,
    "ci_gate": "FAIL",
    "fail_reason": "Found 1 critical and 1 high severity findings"
  },
  "findings": [...]
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/killvxk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
