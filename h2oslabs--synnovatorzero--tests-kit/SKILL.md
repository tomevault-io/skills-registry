---
name: tests-kit
description: Test case management for Synnovator platform. Two modes: (1) Guard mode — before any synnovator skill or implementation change, verify existing test cases in specs/testcases/ are not broken. Use when modifying synnovator skill code, endpoint scripts, engine logic, or data model specs. (2) Insert mode — add new test cases to specs/testcases/. Use when user requests adding business scenarios, new feature test coverage, or regression tests. Triggers: 'run tests', 'check testcases', 'add test case', 'verify tests', 'test coverage', or any synnovator implementation change. Use when this capability is needed.
metadata:
  author: h2oslabs
---

# Tests Kit

Test case guardian and insertion tool for the Synnovator platform's business scenarios defined in `specs/testcases/`.

## Mode 1: Guard — Verify Existing Test Cases

Run this workflow **before committing any change** to synnovator skill scripts, data model docs, or spec files.

### Guard Workflow

1. **Run validation script** to check structural integrity:
   ```bash
   uv run python .claude/skills/tests-kit/scripts/check_testcases.py
   ```
   This checks: TC ID format, uniqueness across files, file structure, and format conventions.

2. **Identify affected test cases** based on the change:
   - Read [references/testcase-index.md](references/testcase-index.md) to find TC IDs related to the changed module
   - Read [references/testcase-format.md](references/testcase-format.md) for test layering strategy
   - Map changes to affected TC prefixes:
     - `content.py` / endpoint scripts → `TC-USER`, `TC-CAT`, `TC-RULE`, `TC-GRP`, `TC-POST`, `TC-RES`, `TC-IACT`
     - `relations.py` → `TC-REL-*`
     - `cascade.py` → `TC-DEL-*`
     - `cache.py` → `TC-IACT-003`, `TC-IACT-013`, `TC-IACT-021` (counter tests)
     - `rules.py` → `TC-RULE-100+`, `TC-ENGINE-*`, `TC-ENTRY-*`, `TC-CLOSE-*`
     - `docs/data-types.md` → All content type TCs
     - `docs/relationships.md` → `TC-REL-*`, `TC-FRIEND-*`, `TC-STAGE-*`, `TC-TRACK-*`, `TC-PREREQ-*`
     - `docs/crud-operations.md` → `TC-PERM-*`, all CRUD TCs
     - `docs/rule-engine.md` → `TC-ENGINE-*`, `TC-ENTRY-*`, `TC-CLOSE-*`
     - **frontend/lib/api-client.ts** → `TC-FEINT-090` (CRUD 完整性)
     - **frontend/app/**/create/page.tsx** → `TC-FEINT-001~091` (前端集成)
     - **frontend/app/**/edit/page.tsx** → `TC-FEINT-040~041` (编辑集成)
     - **specs/seed-data-requirements.md** → 种子数据映射的测试用例 (需检查覆盖度)
     - **scripts/seed_dev_data.py** → 种子数据脚本变更需验证前置条件覆盖
   - Map E2E implementation files to TC prefixes:
     - **e2e/test_journey_anonymous.py** → `TC-JOUR-002`, `TC-BROWSE-*`
     - **e2e/test_journey_team_join.py** → `TC-JOUR-005`, `TC-GRP-004~007`
     - **e2e/test_journey_team_registration.py** → `TC-JOUR-007`, `TC-REL-CG-*`
     - **e2e/test_journey_post_creation.py** → `TC-JOUR-009`, `TC-POST-*`, `TC-CREATE-*`
     - **e2e/test_journey_certificate.py** → `TC-JOUR-010`, `TC-CLOSE-030~032`
     - **e2e/test_journey_post_edit.py** → `TC-JOUR-011-*`, `TC-REL-PP-*`
     - **e2e/test_journey_post_delete.py** → `TC-JOUR-012`, `TC-DEL-012`
     - **e2e/test_journey_community.py** → `TC-JOUR-013`, `TC-IACT-*`, `TC-SOCIAL-*`

3. **Read the affected test case files** in `specs/testcases/` and verify each scenario still holds given the proposed change.

4. **Report conflicts** — If any test case would be broken:
   - List each broken TC ID with explanation
   - Propose how to resolve (fix implementation to preserve TC, or update TC with user approval)

## Mode 2: Insert — Add New Test Cases

Run this workflow when the user wants to add new test scenarios.

### Insert Workflow

1. **Understand the scenario** the user wants to test. Ask for:
   - What business behavior should be covered?
   - Which content types / relations / rules are involved?

2. **Check existing coverage** — Read [references/testcase-index.md](references/testcase-index.md) and search for overlapping or similar TCs. If the scenario is already covered, inform the user.

3. **Try to fit within existing specs** — Before proposing any spec changes:
   - Read relevant files in `docs/` and `specs/` to understand current data model and constraints
   - Attempt to express the test using **existing** content types, relations, and rule engine features
   - Consider: Can the test be decomposed into multiple steps using existing primitives?
   - Consider: Can an existing data type (e.g., `interaction`, `post:post` relation) serve as an indirect wrapper?
   - If the scenario can be expressed without spec changes, proceed to step 5

4. **If spec changes are needed** — Present to the user:
   - Which spec file(s) need modification
   - What the minimum change would be
   - Impact analysis: which existing TCs might be affected
   - Wait for user approval before proceeding

5. **Determine placement** — Read [references/testcase-format.md](references/testcase-format.md) for conventions:
   - **First, choose the correct layer** based on test scope:
     - 基础层 (01-10): 单个实体 CRUD/约束测试
     - 桥接层 (11): 完整业务流程（多步骤、跨实体）
     - 高级层 (12-17): 规则引擎、转移等高级功能
     - 场景层 (18-33): 细粒度端到端场景
   - **Avoid duplication** between layer 11 and 18-33:
     - 11 覆盖**完整流程**（1-2 个 TC 验证整条链路）
     - 18-33 覆盖**场景变体**（多个 TC 验证正向/负向/边界）
   - Identify the correct file (by module) or decide if a new file is needed
   - Pick the next available TC ID number within the appropriate range
   - Place positive cases in 001-099 (or 100-199 for feature-specific), negative in 900-999

6. **Write the test case** following format conventions:
   - Chinese language
   - `**TC-PREFIX-NNN：Title**` format
   - Scenario + expected result only, no test method
   - Single paragraph unless multi-effect cascade

7. **Run validation** after insertion:
   ```bash
   uv run python .claude/skills/tests-kit/scripts/check_testcases.py
   ```

## Running E2E Tests

```bash
# 运行所有用户旅程 E2E 测试
uv run pytest e2e/ -v -k "journey"

# 运行特定测试
uv run pytest e2e/test_journey_post_creation.py -v
```

## 调试 E2E 测试失败

当 E2E 测试失败时，使用 **Playwright Trace** 进行可视化调试：

```bash
# 启用 trace（失败时自动保存）
uv run pytest e2e/ -v --e2e-trace

# 所有测试都保存 trace（用于分析通过的测试）
uv run pytest e2e/ -v --e2e-trace-all

# 查看 trace 文件（可视化界面）
npx playwright show-trace /tmp/e2e_traces/<test_name>.zip
```

**Trace Viewer 提供：**
- 📸 时间线视图：每个操作的截图
- 🌐 网络面板：HTTP 请求/响应
- 📝 控制台日志：console.log/error
- 🔍 DOM 快照：可检查页面元素

**在测试中使用 traced_page fixture：**

```python
def test_something(traced_page):
    traced_page.goto("http://localhost:3000/explore")

    # 访问捕获的日志
    print(traced_page.console_errors)  # JS 错误
    print(traced_page.network_errors)  # 网络错误

    # 辅助函数
    from conftest import print_console_logs
    print_console_logs(traced_page)
```

## Resources

### scripts/

- `check_testcases.py` — Validate all test case files for format consistency, TC ID uniqueness, and structural correctness. Run with `uv run python .claude/skills/tests-kit/scripts/check_testcases.py`.

### references/

- [testcase-index.md](references/testcase-index.md) — Complete catalog of all 267 test cases with TC IDs, descriptions, and file locations. Read this to find related test cases or check coverage.
- [testcase-format.md](references/testcase-format.md) — File naming, TC ID conventions, number ranges, writing rules, and structural template. Read this before writing new test cases.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/h2oslabs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
