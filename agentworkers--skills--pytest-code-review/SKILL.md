---
name: pytest-code-review
description: 审查 `pytest` 测试代码中的异步模式（async patterns）、测试 fixture（fixtures）、参数化（parametrize）以及模拟（mocking）实现。在审查 `test_*.py` 文件时，需要重点检查异步测试函数的编写方式、测试 fixture 的使用情况，以及模拟机制的实现是否正确。 Use when this capability is needed.
metadata:
  author: AgentWorkers
---
# Pytest代码审查

## 快速参考

| 问题类型 | 参考文档 |
|------------|-----------|
| 使用`async def test_*`定义异步测试函数、`AsyncMock`以及`await`模式 | [references/async-testing.md](references/async-testing.md) |
| `conftest.py`中的测试用例固定装置（fixtures）、作用域（scope）以及清理操作（cleanup） | [references/fixtures.md](references/fixtures.md) |
| 使用`@pytest.mark.parametrize`进行参数化测试 | [references/parametrize.md](references/parametrize.md) |
| `AsyncMock`的调用跟踪、`patch`模式以及何时使用模拟（mocking） | [references/mocking.md](references/mocking.md) |

## 审查 checklist

- [ ] 被测试的异步代码是否使用`async def test_*`定义？
- [ ] 是否对异步依赖项使用了`AsyncMock`而非`Mock`？
- [ ] 所有的异步模拟（mocks）和协程（coroutines）是否都正确地等待了结果（awaited）？
- [ ] 是否在`conftest.py`中定义了用于共享测试环境的固定装置？
- [ ] 固定装置的作用域是否合适（函数级、类级、模块级或会话级）？
- [ ] 使用`yield`的固定装置是否在`finally`块中进行了适当的清理操作？
- [ ] 是否使用了`@pytest.mark.parametrize`来简化相似测试用例的编写？
- [ ] 多个测试函数之间是否存在重复的测试逻辑？
- [ ] 模拟对象是否正确地记录了调用次数（使用`assert_called_once_with`进行验证）？
- [ ] `patch()`函数是否应用于正确的位置（即实际需要修改的地方，而不是仅在定义处使用）？
- [ ] 是否避免了对应该被测试的内部功能的模拟？
- [ ] 测试之间是否保持了隔离性（没有共享的可变状态）？

## 何时查阅相关参考文档

- 审查异步测试函数时 → 查阅`async-testing.md`
- 审查测试用例固定装置或`conftest.py`时 → 查阅`fixtures.md`
- 审查相似测试用例时 → 查阅`parametrize.md`
- 审查模拟（mocking）和`patch`操作时 → 查阅`mocking.md`

## 审查问题

1. 所有的异步函数是否都使用`async def test_*`进行定义？
2. 固定装置的作用域是否合适，并且是否包含了必要的清理操作？
3. 相似的测试用例是否可以通过参数化来减少代码重复？
4. 模拟对象是否正确地记录了调用次数，并且被应用于正确的位置？

---
> Source: [AgentWorkers/skills](https://github.com/AgentWorkers/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
