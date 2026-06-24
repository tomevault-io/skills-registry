---
name: ci-cd-and-automation
description: 自动化 CI/CD 流水线设置。用于设置或修改构建与部署流水线。用于需要自动化质量门禁、在 CI 中配置测试运行器或建立部署策略时。 Use when this capability is needed.
metadata:
  author: peakdong68
---

# CI/CD 与自动化

## 概览

自动化质量门禁，确保任何变更在未通过测试、lint、类型检查和构建前都无法到达生产环境。CI/CD 是所有其他技能的强制执行机制——它能捕捉人类和智能体遗漏的问题，并在每次变更时始终如一地做到这一点。

**左移：** 尽可能在流水线早期发现问题。在 lint 阶段捕获的 bug 只耗费数分钟；同样的 bug 在生产环境中则耗费数小时。将检查前移——静态分析在测试之前，测试在预发布之前，预发布在生产之前。

**更快即更安全：** 更小的批次和更频繁的发布降低而非增加风险。包含 3 个变更的部署比包含 30 个的更容易调试。频繁发布能建立对发布流程本身的信心。

## 何时使用

- 设置新项目的 CI 流水线
- 新增或修改自动化检查
- 配置部署流水线
- 当变更应触发自动化验证时
- 调试 CI 故障

## 质量门禁流水线

每个变更在合并前都要经过以下门禁：

```
拉取请求已开启
    │
    ▼
┌─────────────────┐
│   LINT 检查      │  eslint、prettier
│   ↓ 通过         │
│   类型检查       │  tsc --noEmit
│   ↓ 通过         │
│   单元测试       │  jest/vitest
│   ↓ 通过         │
│   构建           │  npm run build
│   ↓ 通过         │
│   集成测试       │  API/DB 测试
│   ↓ 通过         │
│   E2E（可选）    │  Playwright/Cypress
│   ↓ 通过         │
│   安全审计       │  npm audit
│   ↓ 通过         │
│   包体积检查     │  bundlesize 检查
└─────────────────┘
    │
    ▼
  等待审查
```

**任何门禁都不能跳过。** 如果 lint 失败，修正 lint——不要禁用规则。如果测试失败，修正代码——不要跳过测试。

## GitHub Actions 配置

### 基本 CI 流水线

```yaml
# .github/workflows/ci.yml
name: CI

on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  quality:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: '22'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Lint
        run: npm run lint

      - name: Type check
        run: npx tsc --noEmit

      - name: Test
        run: npm test -- --coverage

      - name: Build
        run: npm run build

      - name: Security audit
        run: npm audit --audit-level=high
```

### 包含数据库集成测试

```yaml
integration:
  runs-on: ubuntu-latest
  services:
    postgres:
      image: postgres:16
      env:
        POSTGRES_DB: testdb
        POSTGRES_USER: ci_user
        POSTGRES_PASSWORD: ${{ secrets.CI_DB_PASSWORD }}
      ports:
        - 5432:5432
      options: >-
        --health-cmd pg_isready
        --health-interval 10s
        --health-timeout 5s
        --health-retries 5

  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: '22'
        cache: 'npm'
    - run: npm ci
    - name: Run migrations
      run: npx prisma migrate deploy
      env:
        DATABASE_URL: postgresql://ci_user:${{ secrets.CI_DB_PASSWORD }}@localhost:5432/testdb
    - name: Integration tests
      run: npm run test:integration
      env:
        DATABASE_URL: postgresql://ci_user:${{ secrets.CI_DB_PASSWORD }}@localhost:5432/testdb
```

> **注意：** 即使是仅用于 CI 的测试数据库，也应使用 GitHub Secrets 来存储凭据，而不是硬编码值。这能培养良好习惯，并防止在其他场景下意外复用测试凭据。

### E2E 测试

```yaml
e2e:
  runs-on: ubuntu-latest
  steps:
    - uses: actions/checkout@v4
    - uses: actions/setup-node@v4
      with:
        node-version: '22'
        cache: 'npm'
    - run: npm ci
    - name: Install Playwright
      run: npx playwright install --with-deps chromium
    - name: Build
      run: npm run build
    - name: Run E2E tests
      run: npx playwright test
    - uses: actions/upload-artifact@v4
      if: failure()
      with:
        name: playwright-report
        path: playwright-report/
```

## 将 CI 故障反馈给智能体(Agents)

CI 与 AI 智能体的强大之处在于反馈循环。当 CI 失败时：

```
CI 失败
    │
    ▼
复制失败输出
    │
    ▼
将其反馈给智能体：
"CI 流水线失败，错误信息如下：
[粘贴具体错误]
修复问题并在重新推送前进行本地验证。"
    │
    ▼
智能体修复 → 推送 → CI 再次运行
```

**关键模式：**

```
Lint 失败 → 智能体运行 `npm run lint --fix` 并提交
类型错误   → 智能体读取错误位置并修复类型
测试失败 → 智能体遵循调试与错误恢复技能
构建错误 → 智能体检查配置和依赖
```

## 部署策略

### 预览部署(Preview Deployments)

每个 PR 都获得一个预览部署用于手动测试：

```yaml
# 在 PR 上部署预览（Vercel/Netlify 等）
deploy-preview:
  runs-on: ubuntu-latest
  if: github.event_name == 'pull_request'
  steps:
    - uses: actions/checkout@v4
    - name: Deploy preview
      run: npx vercel --token=${{ secrets.VERCEL_TOKEN }}
```

### 功能开关(Feature Flags)

功能开关将部署与发布解耦。将未完成或有风险的功能部署在开关后面，以便你能：

- **交付代码而不启用。** 尽早合并到主分支，准备就绪后再启用。
- **无需重新部署即可回滚。** 关闭开关而不是回退代码。
- **灰度发布新功能。** 对 1% 的用户启用，然后是 10%，最后是 100%。
- **运行 A/B 测试。** 比较有无该功能时的行为差异。

```typescript
// 简单的功能开关模式
if (featureFlags.isEnabled('new-checkout-flow', { userId })) {
  return renderNewCheckout();
}
return renderLegacyCheckout();
```

**开关生命周期：** 创建 → 为测试启用 → 灰度 → 全量发布 → 移除开关及死代码。永久存活的开关会变成技术债——创建它们时就设定一个清理日期。

### 分阶段发布

```
PR 合并到主分支
    │
    ▼
  预发布环境部署（自动）
    │ 手动验证
    ▼
  生产环境部署（手动触发或在预发布之后自动触发）
    │
    ▼
  监控错误（15 分钟窗口期）
    │
    ├── 检测到错误 → 回滚
    └── 无错误 → 完成
```

### 回滚计划

每次部署都应该是可逆的：

```yaml
# 手动回滚工作流
name: Rollback
on:
  workflow_dispatch:
    inputs:
      version:
        description: '要回滚到的版本'
        required: true

jobs:
  rollback:
    runs-on: ubuntu-latest
    steps:
      - name: Rollback deployment
        run: |
          # 部署指定的先前版本
          npx vercel rollback ${{ inputs.version }}
```

## 环境管理

```
.env.example       → 已提交（供开发者使用的模板）
.env                → 未提交（本地开发）
.env.test           → 已提交（测试环境，无真实密钥）
CI 密钥             → 存储在 GitHub Secrets / Vault 中
生产环境密钥       → 存储在部署平台 / Vault 中
```

CI 永远不应持有生产环境密钥。为 CI 测试使用单独的密钥。

## CI 之外的自动化

### Dependabot / Renovate

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: npm
    directory: /
    schedule:
      interval: weekly
    open-pull-requests-limit: 5
```

### 构建负责人 (Build Cop) 角色

指定专人负责保持 CI 状态为绿色（通过）。当构建失败时，构建负责人的工作是修复或回滚——而不是由导致失败的变更提交者来负责。这能防止每个人都以为别人会修复时，损坏的构建堆积起来。

### PR 检查

- **必需审查：** 合并前至少 1 人批准
- **必需状态检查：** 合并前 CI 必须通过
- **分支保护：** 禁止对主分支强制推送
- **自动合并：** 如果所有检查通过且已批准，则自动合并

## CI 优化

当流水线耗时超过 10 分钟时，按效果排序应用以下策略：

```
CI 流水线慢？
├── 缓存依赖
│   └── 使用 actions/cache 或 setup-node 的 cache 选项缓存 node_modules
├── 并行运行作业
│   └── 将 lint、类型检查、测试、构建拆分为独立的并行作业
├── 只运行发生变化的部分
│   └── 使用路径过滤器跳过无关作业（例如，对仅文档变更的 PR 跳过 e2e 测试）
├── 使用矩阵构建
│   └── 将测试套件分片到多个运行器上
├── 优化测试套件
│   └── 将慢速测试移出关键路径，改为按计划运行
└── 使用更大的运行器
    └── GitHub 托管的更大运行器或自托管运行器用于 CPU 密集型构建
```

**示例：缓存与并行化**

```yaml
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npm run lint

  typecheck:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npx tsc --noEmit

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22', cache: 'npm' }
      - run: npm ci
      - run: npm test -- --coverage
```

## 常见借口

| 借口                           | 事实                                                                                |
| ------------------------------ | ----------------------------------------------------------------------------------- |
| "CI 太慢了"                    | 优化流水线（见下方 CI 优化），不要跳过它。一个 5 分钟的流水线可以避免数小时的调试。 |
| "这个改动很简单，跳过 CI"      | 简单的改动也会破坏构建。CI 处理简单变更本来就很快。                                 |
| "这个测试不稳定，重新运行就好" | 不稳定的测试掩盖了真实 bug 并浪费所有人的时间。修复不稳定性。                       |
| "我们以后再添加 CI"            | 没有 CI 的项目会积累损坏状态。第一天就设置好它。                                    |
| "手动测试就够了"               | 手动测试无法扩展且不可重复。尽可能自动化。                                          |

## 危险信号

- 项目中没有 CI 流水线
- CI 失败被忽略或静默处理
- 测试在 CI 中被禁用以让流水线通过
- 未经预发布验证就部署到生产环境
- 没有回滚机制
- 密钥存储在代码或 CI 配置文件中（而非密钥管理器）
- CI 耗时过长且未进行优化

## 验证清单

设置或修改 CI 后：

- [ ] 所有质量门禁均已就位（lint、类型检查、测试、构建、审计）
- [ ] 流水线在每个 PR 及推送到主分支时运行
- [ ] 失败会阻止合并（已配置分支保护）
- [ ] CI 结果反馈回开发循环
- [ ] 密钥存储在密钥管理器中，而非代码中
- [ ] 部署有回滚机制
- [ ] 流水线运行测试套件耗时在 10 分钟以内

---
> Source: [peakdong68/my-agent-skills](https://github.com/peakdong68/my-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
