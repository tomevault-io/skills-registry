---
name: frontend-code-review
description: 前端代码审查，覆盖代码质量、性能优化、可维护性、安全漏洞、重复代码检测、复杂度检查等，确保代码符合生产标准 Use when this capability is needed.
metadata:
  author: yuchunbo
---

# 前端代码审查技能

## 适用场景

**对话触发词**：
- "代码审查"
- "代码质量检查"
- "代码审计"
- "安全检查"
- "性能优化检查"
- "复杂度分析"
- "代码门禁"

**工具自动触发时机**：
- AI Frontend Guard 执行 `analyze` 命令时默认启用
- 执行代码审查/质量门禁流程时自动触发
- 检测到安全敏感操作时自动触发

## 执行流程

1. **扫描阶段**：遍历项目目录，收集所有代码文件
2. **规则加载**：读取 `references/` 下的审查规则清单
3. **逐项审查**：
   - 代码质量检查（重复代码、圈复杂度）
   - 性能优化检查（大型包引入、渲染优化）
   - 安全检查（XSS、CSRF、硬编码密钥）
   - 可维护性检查（代码可读性、注释覆盖率）
4. **问题分级**：根据严重程度分类（错误/警告/提示）
5. **报告生成**：输出结构化审查报告，包含修复建议和优先级

## 规则清单

### 1. 代码质量检查
参考：[checklist.md](references/checklist.md)

### 2. 性能检查清单
参考：[performance-checklist.md](references/performance-checklist.md)

### 3. 安全检查清单
参考：[security-checklist.md](references/security-checklist.md)

### 4. 可维护性检查清单
参考：[maintainability-checklist.md](references/maintainability-checklist.md)

## 输出模板

```
┌─────────────────────────────────────────────────────────────┐
│              前端代码审查报告                                │
├─────────────────────────────────────────────────────────────┤
│ 项目: {{projectName}}                                       │
│ 审查时间: {{timestamp}}                                     │
│ 代码行数: {{totalLines}}                                    │
├─────────────────────────────────────────────────────────────┤
│ 总体评分: {{overallScore}}/100                             │
├─────────────────────────────────────────────────────────────┤
│ 分类评分:                                                   │
│   代码质量: {{qualityScore}}/100                            │
│   性能优化: {{performanceScore}}/100                        │
│   安全性: {{securityScore}}/100                             │
│   可维护性: {{maintainabilityScore}}/100                    │
├─────────────────────────────────────────────────────────────┤
│ 问题统计:                                                   │
│   ✗ 错误: {{errorCount}}                                    │
│   ⚠ 警告: {{warningCount}}                                  │
│   ℹ 提示: {{infoCount}}                                     │
├─────────────────────────────────────────────────────────────┤
│ 问题详情:                                                   │
│ [{{severity}}] {{filePath}}:{{line}}                        │
│   规则: {{ruleName}}                                        │
│   描述: {{description}}                                     │
│   建议: {{suggestion}}                                      │
├─────────────────────────────────────────────────────────────┤
│ 修复优先级:                                                 │
│ {{priorityRecommendations}}                                 │
└─────────────────────────────────────────────────────────────┘
```

### 严重等级说明

| 等级 | 标识 | 说明 |
|------|------|------|
| error | ✗ | 必须修复，存在安全风险或严重质量问题 |
| warning | ⚠ | 建议修复，影响性能或可维护性 |
| info | ℹ | 优化建议，提升代码质量 |

### 评分标准

| 分数范围 | 等级 | 说明 |
|----------|------|------|
| 90-100 | A | 优秀 |
| 75-89 | B | 良好 |
| 60-74 | C | 及格 |
| <60 | D | 需改进 |

---
> Source: [yuchunbo/ai-frontend-guard](https://github.com/yuchunbo/ai-frontend-guard) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
