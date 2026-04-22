---
name: test-edge-cases
description: 边界条件测试skill。验证空输入、超长输入和特殊字符处理。 Use when this capability is needed.
metadata:
  author: zhuangbiaowei
---

# Edge Cases Test Skill

用于测试SmartBot的边界条件处理能力。

## 测试场景

### 场景1: 空输入处理
输入: `test_edge_cases` (无额外参数)
期望: 正常执行，报告无参数

### 场景2: 超长输入
输入: 超过1000字符的长文本
期望: 不截断或优雅处理

### 场景3: 特殊字符
输入包含: `< > & " ' \ / @ # $ % ^ * ( ) [ ] { } | \n \t`
期望: 正确处理，不导致解析错误

### 场景4: Unicode字符
输入: emoji和各种Unicode字符
期望: 正确编码处理

### 场景5: 控制字符
输入: 包含换行符、制表符等
期望: 正确处理或安全过滤

### 场景6: 注入测试
输入: 可能的注入模式如 `${}`, `{{}}`, `<script>`
期望: 安全处理，不执行恶意代码

## 预期输出

```
🔬 Edge Cases Test Results:
════════════════════════════════════════
Test: <测试场景名称>
Input length: <字符数>
Sanitized: <是否经过清理>
Special chars found: <发现的特殊字符列表>
Status: ✅ PASS / ⚠️ WARNING / ❌ FAIL
════════════════════════════════════════
```

## CLI测试命令

```bash
# 空输入测试
smart_bot agent -m "test_edge_cases"

# 特殊字符测试
smart_bot agent -m 'test_edge_cases special: <>&"\'\\/@#$%^*()[]{}|'

# Unicode/Emoji测试
smart_bot agent -m "test_edge_cases emoji: 😀🎉🚀 中文：测试 日本語：テスト"

# 多行输入测试
smart_bot agent -m "test_edge_cases multiline:
line 1
line 2
line 3"

# 注入模式测试（安全验证）
smart_bot agent -m 'test_edge_cases injection: ${dangerous} {{template}} <script>alert(1)</script>'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhuangbiaowei) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
