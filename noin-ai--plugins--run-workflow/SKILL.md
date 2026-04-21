---
name: run-workflow
description: 查找范围 (project/user/all) Use when this capability is needed.
metadata:
  author: noin-ai
---

# Run Workflow Skill

执行用户创建的工作流定义。

## Usage

```bash
/run-workflow <name>
/run-workflow scrape-prices --url "https://example.com"
```

## 执行流程

1. 查找 workflow YAML 文件
2. 解析变量和参数
3. 按步骤调度 agents
4. 返回执行结果

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/noin-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
