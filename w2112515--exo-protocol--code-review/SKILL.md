---
name: code-review
description: 代码质量评分 (0-100) Use when this capability is needed.
metadata:
  author: w2112515
---

# Code Review Skill

## 功能描述

自动化代码审查工具，检测代码中的安全漏洞、性能问题和风格问题，并提供改进建议。

**适用场景**:
- PR 预审查
- 安全漏洞扫描
- 代码质量门禁
- 学习最佳实践

## 使用示例

**输入**:
```json
{
  "code": "def get_user(id):\n    query = f\"SELECT * FROM users WHERE id = {id}\"\n    return db.execute(query)",
  "language": "python",
  "review_focus": ["security", "best-practices"],
  "severity_threshold": "warning"
}
```

**输出**:
```json
{
  "issues": [
    {
      "line": 2,
      "severity": "critical",
      "category": "security",
      "message": "SQL Injection vulnerability detected",
      "suggestion": "Use parameterized queries: db.execute('SELECT * FROM users WHERE id = ?', [id])"
    },
    {
      "line": 1,
      "severity": "warning",
      "category": "best-practices",
      "message": "Missing type hints for function parameters",
      "suggestion": "Add type hints: def get_user(id: int) -> dict:"
    }
  ],
  "summary": {
    "total_issues": 2,
    "critical_count": 1,
    "error_count": 0,
    "warning_count": 1,
    "info_count": 0
  },
  "overall_score": 45
}
```

## 注意事项

- 代码内容最大 50KB
- 支持 6 种主流编程语言
- 安全问题优先级最高
- 建议与 CI/CD 流程集成

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/w2112515) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
