---
name: test-validate
description: 运行项目测试（涵盖 Python 后端、Vue 前端及多语言校验） Use when this capability is needed.
metadata:
  author: 4thfever
---

## 🚀 常用测试命令

### 后端 (Python)
推荐在已激活虚拟环境的情况下执行：

```bash
# 运行所有后端测试
pytest

# 运行特定测试文件
pytest tests/test_<name>.py -v
pytest tests/test_frontend_locales.py  # 检查前端多语言一致性
pytest tests/test_backend_locales.py   # 检查后端多语言一致性

# 运行并生成覆盖率报告
pytest --cov=src

# 运行服务器（开发模式）
python src/server/main.py --dev
```

### 前端 (Vue/Node)
必须在 `web` 目录下执行：

```bash
# 运行所有前端单元测试
cd web && npm run test

# 运行并生成前端覆盖率报告
cd web && npm run test:coverage

# 运行 TypeScript 类型检查
cd web && npm run type-check
```

## 📊 测试覆盖率与规范指南

在进行代码更改后，考虑是否需要测试：

| 更改类型 | 测试建议 |
|-------------|---------------------|
| 修复 Bug | 添加回归测试以防止再次发生，确保测试在**修复前会失败**并且在**修复后会通过** |
| 新功能 | 单元测试 + 如果影响多个模块则添加集成测试 |
| 重构 | 现有测试应通过；如果行为改变则添加测试 |
| 配置/文档 | 通常不需要测试 |
| 新增多语言词条 | **必须**运行 `test_frontend_locales.py` / `test_backend_locales.py` 确保各语言 Key 结构对齐 |

> **提示:** 如果你在编写后端测试，请参考 `.cursor/rules/testing.mdc`，优先使用项目中已经提供的 `dummy_avatar`、`mock_llm_managers` 等现成的 fixtures，避免重复 mock 或消耗真实 Token。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/4thfever) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
