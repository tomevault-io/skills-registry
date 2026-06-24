---
name: code-review
description: Use immediately after writing code, before committing. Check quality, security, performance, and maintainability. Catch bugs and anti-patterns. | 写完代码后立即使用，检查质量/安全/性能/可维护性。 Use when this capability is needed.
metadata:
  author: XY523-iaou
---

# 🔍 代码审查

## 概述

代码变更完成后立即派审查子代理进行审查。在问题滚雪球之前捕获。理想情况是每个任务完成后都审查。

## 核心原则

**尽早审查，频繁审查。** 不要在合并前才第一次审查。

## 何时审查

**必须审查：**
- 每个任务完成后
- 完成主要功能后
- 合并到主分支前

**建议审查：**
- 卡住时（新视角）
- 重构前（基线检查）
- 修复复杂 Bug 后

## 审查检查清单

- [ ] 代码可读且命名良好
- [ ] 函数聚焦（<50 行）
- [ ] 文件内聚（<800 行）
- [ ] 无深层嵌套（>4 层）
- [ ] 错误显式处理
- [ ] 无硬编码密钥或凭据
- [ ] 无调试语句残留
- [ ] 新功能有测试
- [ ] 测试覆盖率 ≥ 80%

## 审查严重级别

| 级别 | 含义 | 行动 |
|------|------|------|
| **CRITICAL** | 安全漏洞或数据丢失风险 | 🚫 阻止合并 |
| **HIGH** | Bug 或重大质量问题 | ⚠️ 合并前修复 |
| **MEDIUM** | 可维护性问题 | ℹ️ 考虑修复 |
| **LOW** | 风格或次要建议 | 📝 可选 |

## 常见问题

### 安全
- 硬编码凭据
- SQL 注入（字符串拼接查询）
- XSS 漏洞
- 路径遍历
- CSRF 保护缺失

### 代码质量
- 大函数（>50 行）
- 大文件（>800 行）
- 深层嵌套
- 缺少错误处理
- 可变操作模式

### 性能
- N+1 查询
- 缺少分页
- 无界查询
- 缺少缓存

## 审查工作流

```
1. git diff 了解所有变更
2. 先检查安全检查清单
3. 审查代码质量检查清单
4. 运行相关测试
5. 验证覆盖率 ≥ 80%
6. 输出审查报告
```

---
> Source: [XY523-iaou/dev-workflow-pack](https://github.com/XY523-iaou/dev-workflow-pack) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
