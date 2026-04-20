---
name: testing
description: 四层测试技能，按测试金字塔编写和执行单元/集成/接口/系统测试，生成结构化测试报告 Use when this capability is needed.
metadata:
  author: xshanesong
---

# 测试技能

## 何时使用

当需要编写测试计划、编写测试用例、执行测试、生成测试报告时使用。

## 输出格式

- 测试计划：按 `process_templates/test_plan.md` 模板
- 测试报告：按 `process_templates/test_report.md` 模板
- 测试结果：按 `process_templates/test_results.json` 格式

## 输出规范

测试计划必须包含以下章节：

| 章节         | 必填 | 说明                                        |
| ------------ | ---- | ------------------------------------------- |
| 测试范围     | 是   | 覆盖哪些模块、哪些验收标准                  |
| 单元测试计划 | 是   | 用例列表、覆盖目标、mock 策略               |
| 集成测试计划 | 是   | 用例列表、测试环境、依赖配置                |
| 接口测试计划 | 按需 | 端点覆盖矩阵、测试数据（项目有 API 时必选） |
| 系统测试计划 | 按需 | 场景列表、前置条件、环境要求                |
| 测试环境     | 是   | OS、运行时、依赖版本                        |

测试报告按四层分别汇报，每层包含通过/失败/跳过数量和失败详情。

## 测试金字塔原则

```
        /  系统测试  \        ← 少量，验证端到端场景
       / 接口测试(API) \      ← 适量，验证接口契约
      /   集成测试      \     ← 较多，验证模块协作
     /    单元测试        \   ← 大量，验证函数/类行为
    ‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾‾
```

数量比例参考：单元 > 集成 > 接口 > 系统

## 四层测试详解

### 第一层：单元测试（必选）

**定义**：验证单个函数、方法或类的行为，隔离所有外部依赖。

**编写原则**：

- 每个公共方法至少一个正常路径 + 一个异常路径测试
- 使用 mock/stub 隔离外部依赖（数据库、网络、文件系统）
- 测试应快速执行（单个用例 < 100ms）
- 覆盖率目标：C0（语句覆盖）100% + C1（分支覆盖）100%

**覆盖率定义**：

| 指标 | 名称     | 定义                                               | 目标 |
| ---- | -------- | -------------------------------------------------- | ---- |
| C0   | 语句覆盖 | 每条可执行语句至少被执行一次                       | 100% |
| C1   | 分支覆盖 | 每个条件分支（if/else、switch case）至少各执行一次 | 100% |

**覆盖率工具**：

| 语言       | 覆盖率工具                 | 报告格式          |
| ---------- | -------------------------- | ----------------- |
| Python     | pytest-cov (coverage.py)   | HTML / XML / JSON |
| C#         | coverlet + ReportGenerator | Cobertura / HTML  |
| TypeScript | jest --coverage / c8       | LCOV / HTML       |
| Java       | JaCoCo                     | HTML / XML        |
| Go         | go test -cover             | HTML / func       |
| Rust       | cargo-tarpaulin            | HTML / XML        |
| C/C++      | gcov + lcov                | HTML              |

**覆盖率排除规则**：

- 自动生成的代码（ORM 迁移、Protobuf 生成等）可排除
- 纯 DTO/POCO 类（无逻辑的数据传输对象）可排除
- 排除项必须在测试报告中明确列出并说明原因

**命名规范**：`test_<功能>_<场景>_<预期结果>`

**示例场景**：

- TS-U-001: 验证函数在正常输入下的返回值
- TS-U-002: 验证函数在空输入下抛出预期异常
- TS-U-003: 验证边界值处理（最大值、最小值、空集合）

**框架选择**：
| 语言 | 框架 | Mock 库 |
|------|------|---------|
| Python | pytest | unittest.mock / pytest-mock |
| C# | xUnit | Moq / NSubstitute |
| TypeScript | jest / vitest | jest.mock / vi.mock |
| Java | JUnit 5 | Mockito |
| Go | go test | testify/mock |
| Rust | cargo test | mockall |
| C++ | Google Test | Google Mock |

### 第二层：集成测试（必选）

**定义**：验证模块间的协作，测试真实或模拟的外部依赖交互。

**编写原则**：

- 测试模块间数据传递的正确性
- 测试错误在模块间的传播和处理
- 使用真实或轻量替代的外部依赖（SQLite 替代 PostgreSQL、内存队列替代 MQ）
- 每个模块间接口至少一个集成测试

**测试环境**：

- 数据库：SQLite（内存模式）/ TestContainers / H2
- 外部服务：WireMock / httpretty / responses
- 文件系统：临时目录（tempfile / tmp）

**示例场景**：

- TS-I-001: Service 层调用 Repository 层完成数据持久化
- TS-I-002: Service 层处理 Repository 层抛出的异常
- TS-I-003: 多模块协作完成一个完整业务操作

### 第三层：接口测试（项目暴露 API 时必选）

**定义**：验证 HTTP/RPC/WebSocket 接口的请求-响应契约。

**启用条件**：项目暴露了 HTTP / gRPC / WebSocket / MCP 等外部接口。

**编写原则**：

- 覆盖所有端点的所有 HTTP 方法
- 验证成功响应的状态码、响应体结构、字段类型
- 验证错误响应的状态码、错误消息格式
- 验证输入校验（缺失必填字段、类型错误、越界值）
- 验证认证/授权（有认证时）

**工具选择**（由 Agent 根据技术栈自动选择）：
| 语言 | 工具 |
|------|------|
| Python | httpx + pytest / TestClient (FastAPI) |
| C# | WebApplicationFactory + HttpClient |
| TypeScript | supertest / pactum |
| Java | MockMvc / RestAssured |
| Go | net/http/httptest |

**示例场景**：

- TS-A-001: GET /api/items 返回 200 和正确的列表结构
- TS-A-002: POST /api/items 缺少必填字段返回 422
- TS-A-003: GET /api/items/{id} 不存在返回 404

### 第四层：系统测试（按需选择）

**定义**：端到端验证完整的业务场景，模拟真实用户操作流程。

**启用条件**：

- 项目涉及多个服务间交互
- 关键业务场景需要端到端验证
- 用户明确要求

**编写原则**：

- 基于用户故事和验收标准编写场景
- 每个场景模拟完整的业务流程（从输入到最终输出）
- 使用接近生产环境的配置
- 系统测试数量控制在关键路径（3-10 个场景）

**示例场景**：

- TS-S-001: 用户注册 → 登录 → 创建数据 → 查询数据 → 删除数据
- TS-S-002: 完整的订单流程：下单 → 支付 → 发货 → 确认收货

## 测试场景编写格式

每个测试场景按以下格式编写：

```
场景编号：TS-{层级}-{编号}
层级标记：U(单元) / I(集成) / A(接口) / S(系统)
场景描述：一句话描述测试目的
前置条件：测试执行前的环境要求
操作步骤：
  1. 步骤一
  2. 步骤二
预期结果：期望的输出或行为
对应验收标准：AC-{编号}
```

## 测试执行流程

按层级顺序执行，前一层通过后再执行下一层：

1. **执行单元测试** → 全部通过后继续
2. **执行集成测试** → 全部通过后继续
3. **执行接口测试**（如需）→ 全部通过后继续
4. **执行系统测试**（如需）→ 全部通过后汇总

### 执行命令参考

```bash
# Python
pytest tests/unit/ -v --tb=short --cov=src --cov-branch --cov-report=html
pytest tests/integration/ -v --tb=short
pytest tests/api/ -v --tb=short

# C# / .NET
dotnet test --filter "Category=Unit" --collect:"XPlat Code Coverage" -- DataCollectionRunSettings.DataCollectors.DataCollector.Configuration.Format=cobertura
dotnet test --filter "Category=Integration"

# Node.js / TypeScript
npx jest tests/unit --coverage --coverageThreshold='{"global":{"statements":100,"branches":100}}'
npx jest tests/integration

# Go
go test ./tests/unit/... -v -cover -coverprofile=coverage.out
go test ./tests/integration/... -v

# Rust
cargo tarpaulin --out Html -- --test-threads=1

# Java
mvn test -Dtest="*UnitTest" -Djacoco.destFile=target/jacoco-unit.exec
```

## 测试结果结构

```json
{
  "run_id": "TR-YYYYMMDD-001",
  "project": "项目名称",
  "result": "pass | fail",
  "total_passed": 0,
  "total_failed": 0,
  "total_skipped": 0,
  "layers": [
    {
      "layer": "unit",
      "passed": 0,
      "failed": 0,
      "skipped": 0,
      "duration_ms": 0,
      "coverage": {
        "c0_statement": "100%",
        "c1_branch": "100%",
        "excluded_files": [],
        "exclusion_reason": ""
      },
      "cases": [
        {
          "name": "test_xxx",
          "result": "pass | fail | skip",
          "duration_ms": 0,
          "error": null
        }
      ]
    },
    {
      "layer": "integration",
      "passed": 0,
      "failed": 0,
      "skipped": 0,
      "cases": []
    },
    {
      "layer": "interface",
      "passed": 0,
      "failed": 0,
      "skipped": 0,
      "cases": []
    },
    {
      "layer": "system",
      "passed": 0,
      "failed": 0,
      "skipped": 0,
      "cases": []
    }
  ],
  "failures": [
    {
      "test": "test_xxx",
      "layer": "unit",
      "error": "错误描述",
      "file": "文件路径",
      "line": 0
    }
  ]
}
```

## 判定标准

- **pass**：所有层级的所有用例通过，且单元测试覆盖率达标（C0=100%, C1=100%）
- **fail**：任一层级的任一用例失败，或单元测试覆盖率未达标
  - 失败时：标注失败的层级、用例名称、错误信息
  - 通过 handoff 回退给 Developer 修复
  - 设置 `workflow_state.json` 的 `status: rollback`

## 验收标准 ↔ 测试用例映射

每个验收标准（AC-{编号}）必须至少被一个测试用例覆盖。在测试计划中输出映射表：

| 验收标准 | 单元测试           | 集成测试 | 接口测试 | 系统测试 |
| -------- | ------------------ | -------- | -------- | -------- |
| AC-01    | TS-U-001           | TS-I-001 | TS-A-001 | —        |
| AC-02    | TS-U-002, TS-U-003 | —        | TS-A-002 | TS-S-001 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xshanesong) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
