---
name: chrome-devtools-playwright
description: AI辅助E2E测试工作流，结合Chrome DevTools MCP探索与Playwright测试生成。用于探索新页面进行测试、生成功能测试用例文档、生成Page Object Model和Playwright测试代码、诊断测试失败、集成CI/CD流水线。 Use when this capability is needed.
metadata:
  author: chuanminglu
---

# Chrome DevTools + Playwright 测试技能

混合工作流: MCP探索 → 生成测试文档 → 生成测试代码 → CI执行 → MCP诊断

## 适用场景

- 探索新页面/功能的测试覆盖
- 生成功能测试用例文档（先文档后代码）
- 生成 Playwright 测试代码（Page Object Model）
- 诊断 CI/CD 测试失败
- 从零构建 E2E 测试套件

## 5阶段工作流

```
EXPLORE → DOCUMENT → CODE → EXECUTE → DIAGNOSE
  (MCP)     (AI)     (AI)    (CI)      (MCP)
    ↑                                    │
    └────────── (on failure) ────────────┘
```

## Phase 1: Explore (MCP探索)

```javascript
// 1. 打开页面
mcp_io_github_chr_new_page({ url: TARGET_URL })

// 2. 截图
mcp_io_github_chr_take_screenshot({ fullPage: true })

// 3. DOM快照
mcp_io_github_chr_take_snapshot({ verbose: true })

// 4. 提取选择器
mcp_io_github_chr_evaluate_script({
  function: `() => {
    const elements = {};
    document.querySelectorAll('[data-testid], [data-test]').forEach(el => {
      elements[el.dataset.testid || el.dataset.test] = el.tagName;
    });
    return elements;
  }`
})

// 5. 验证交互
mcp_io_github_chr_click({ uid: "TARGET_UID" })
mcp_io_github_chr_wait_for({ text: "EXPECTED_TEXT" })
```

## Phase 2: Document (生成测试文档) 🆕

**在编写代码之前，先生成功能测试用例文档！**

### 输出位置
```
docs/
└── test-cases/
    └── {功能名称}-测试用例.md
```

### 测试用例表格字段

| 字段 | 说明 | 示例 |
|------|------|------|
| 用例编号 | `{系统}-{模块}-{序号}` | `SWAG-CART-001` |
| 用例概述 | 简短描述（10-20字） | `正常添加商品到购物车` |
| 优先级 | P0核心/P1重要/P2辅助 | `P0` |
| 标签 | 测试类型 | `功能测试,可自动化` |
| 前提条件 | 执行前置条件 | `1. 用户已登录` |
| 输入数据或操作 | 详细步骤 | `1. 点击【添加】按钮` |
| 预期结果 | 量化验证点 | `购物车显示"1"` |

See: `references/test-case-template.md` - 测试用例文档模板
See: `references/prompts.md` → Prompt 6 - 生成测试用例文档

## Phase 3: Code (生成测试代码)

**顺序:** 先 POM → 再 Tests

### 输出位置
```
pages/                  # Page Objects (选择器在这里)
├── LoginPage.ts
└── index.ts
tests/                  # 测试脚本 (无选择器)
├── fixtures/           # 测试数据
└── *.spec.ts
```

```typescript
// ❌ 选择器写在测试里 (错误)
await page.click('.submit-btn');

// ✅ 调用POM方法 (正确)
await loginPage.submit();
```

See: `references/prompts.md` → Prompt 1, 2
See: `references/page-object-template.md` - POM模板

## Phase 4: Execute (CI执行)

```yaml
# .github/workflows/e2e.yml
name: E2E Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npx playwright install --with-deps chromium
      - run: npx playwright test
      - uses: actions/upload-artifact@v4
        if: failure()
        with: { name: test-results, path: test-results/ }
```

## Phase 5: Diagnose (故障诊断)

```javascript
// 控制台错误
mcp_io_github_chr_list_console_messages({ types: ["error"] })

// 网络失败
mcp_io_github_chr_list_network_requests({ resourceTypes: ["xhr", "fetch"] })

// 当前状态
mcp_io_github_chr_take_screenshot()
```

See: `references/diagnosis.md` - 故障诊断模式

## 推荐目录结构

```
project/
├── docs/                         # 测试文档
│   └── test-cases/               # 测试用例文档
│       └── {功能}-测试用例.md
├── pages/                        # Page Objects
│   ├── LoginPage.ts
│   └── index.ts
├── tests/                        # 测试脚本
│   ├── fixtures/                 # 测试数据
│   └── *.spec.ts
├── playwright.config.ts
└── package.json
```

## 🚀 AUTO模式：一键生成全部测试

当测试用例文档已就绪，使用以下流程自动完成所有测试：

```
[测试用例文档] → AUTO → [POM] + [测试代码] + [CI配置]
```

**触发方式:**
```
请根据测试用例文档，自动生成所有测试

输入: docs/test-cases/{功能}-测试用例.md
输出:
- pages/*.ts (所有Page Object)
- tests/*.spec.ts (所有测试脚本)
- .github/workflows/e2e.yml (CI配置)
```

**AI执行步骤:**
1. 读取测试用例文档，提取所有「可自动化」用例
2. 分析涉及的页面，生成 Page Objects
3. 按功能模块生成测试脚本
4. 生成 CI 配置文件
5. 输出执行命令

See: `references/prompts.md` → Prompt 8 - AUTO模式一键生成

## 快速开始

### 标准流程（5阶段）
```bash
# 1. 探索页面 (Phase 1) - 使用MCP命令
# 2. 生成测试文档 (Phase 2) - AI生成 docs/test-cases/*.md
# 3. 生成测试代码 (Phase 3) - AI生成 pages/*.ts + tests/*.spec.ts
# 4. 执行测试 (Phase 4)
npx playwright test
# 5. 诊断失败 (Phase 5) - 使用MCP命令
```

### AUTO模式（文档已就绪）
```bash
# 1. 准备测试用例文档: docs/test-cases/{功能}-测试用例.md
# 2. 执行: "请根据测试用例文档，自动生成所有测试"
# 3. AI自动生成: pages/*.ts + tests/*.spec.ts + CI配置
# 4. 运行测试: npx playwright test
```

## 参考文档

- `references/workflow.md` - 详细5阶段工作流
- `references/prompts.md` - AI生成提示词
- `references/test-case-template.md` - 测试用例文档模板 🆕
- `references/page-object-template.md` - POM模板
- `references/diagnosis.md` - 故障诊断模式

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chuanminglu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
