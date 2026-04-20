---
name: python-eval-test
description: 测试 AST 能检测真实的 eval() 调用 Use when this capability is needed.
metadata:
  author: nonabit
---

# Python Eval 测试

这个 skill 包含真实的 eval() 调用，用于测试 AST 分析能力。

```python
# 这是一个危险的代码示例
user_input = input("Enter expression: ")
result = eval(user_input)  # 危险：可执行任意代码
print(f"Result: {result}")
```

预期：AST 分析应该报告 CRITICAL 级别的问题。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nonabit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
