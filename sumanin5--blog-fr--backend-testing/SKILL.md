---
name: backend-testing
description: 后端测试开发规范。强调函数式测试优先，详细说明了 fixtures 的使用、pytest markers 的分类约定以及数据库隔离策略。 Use when this capability is needed.
metadata:
  author: sumanin5
---

# 后端测试开发指南

## 📖 简介

本指南规范了后端 pytest 测试的编写方式。本项目倾向于使用 **函数式测试 (Function-based Tests)** 而非类式测试，并利用 pytest fixture 体系进行依赖注入。

## 🎯 测试原则

1.  **函数式优先**：简单的测试函数比测试类更易读、易组合。
2.  **API 测试为主**：重点测试端到端的 API 行为，包括状态码、响应结构和业务约束。
3.  **工具类单元测试**：针对 `utils.py` 中的复杂逻辑编写纯单元测试。
4.  **Fixture 注入**：通过 `conftest.py` 管理通用的数据库连接 (`session`) 和 HTTP 客户端 (`client`)。
5.  **标记 (Marker) 分类**：必须为测试打上正确的标记，以便按模块运行测试。
6.  **数据库隔离**：测试使用嵌套事务回滚机制，确保不污染数据库。
7.  **uv 环境运行**：本项目使用 `uv` 管理依赖，运行测试必须通过 `uv run` 或 `make` 指令。

## 📍 标记规范 (Markers)

在 `backend/pyproject.toml` 中维护了所有的 markers。

**现有常用标记：**
- `unit`: 单元测试，通常用于测试 `utils.py`。
- `integration`: 集成测试，通常涉及 API 调用。
- `middleware`: 中间件测试。
- `users`: 用户模块测试。
- `posts`: 文章模块测试。
- `media`: 媒体模块测试。
- `permissions`: 权限相关测试。
- `git_ops`: Git 同步相关测试。

**⚠️ 新增模块时：**
如果你添加了新模块（例如 `app/comments`），请务必在 `backend/pyproject.toml` 的 `[tool.pytest.ini_options].markers` 列表中添加对应的标记：

```toml
[tool.pytest.ini_options]
markers = [
    # ... existing markers
    "comments: mark test as a comments module test",
]
```

## 🧪 测试编写范例

### 1. API 路由测试 (集成测试)

主要验证 Endpoint 的行为。
文件路径示例：`backend/tests/api/new_module/test_items.py`

```python
import pytest
from httpx import AsyncClient
from fastapi import status

# 必须打上 integration 和 模块 tag
@pytest.mark.integration
@pytest.mark.new_module
async def test_create_item(
    client: AsyncClient,              # 自动注入 HTTP 客户端
    superuser_token_headers: dict,    # 注入管理员 Token Header
):
    data = {"title": "New Item"}

    response = await client.post(
        "/api/v1/items/",
        headers=superuser_token_headers,
        json=data
    )

    assert response.status_code == status.HTTP_201_CREATED
    result = response.json()
    assert result["title"] == "New Item"
```

### 2. 工具类测试 (单元测试)

主要验证 `utils.py` 中的函数逻辑。
文件路径示例：`backend/tests/unit/test_new_module_utils.py`

```python
import pytest
from app.new_module.utils import format_item_name

@pytest.mark.unit
@pytest.mark.new_module
def test_format_item_name():
    name = "   hello world   "
    result = format_item_name(name)
    assert result == "Hello World"
```

## 🛠️ 常用 Fixtures

这些 Fixtures 定义在 `backend/tests/conftest.py` 或 `backend/tests/api/conftest.py` 中：

- `client`: `AsyncClient` 实例，用于发送请求。
- `session`: `AsyncSession` 实例，每个测试结束后自动回滚。
- `normal_user_token_headers`: 普通用户的 Authorization header。
- `superuser_token_headers`: 超级管理员的 Authorization header。
- `normal_user`: 一个已创建的普通用户对象。

## 🏃‍♂️ 运行测试

必须使用 `uv` 运行测试，推荐使用 `Makefile` 中的捷径。

```bash
cd backend

# 方式一：使用 Makefile (推荐)
make test             # 运行所有
make test-posts       # 运行指定模块
make test-unit        # 运行单元测试
make test-cov-html    # 查看覆盖率

# 方式二：直接使用 uv run
uv run pytest
uv run pytest -m posts
uv run pytest tests/api/new_module/test_items.py
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sumanin5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
