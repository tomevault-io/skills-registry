---
name: code-quality-check
description: 通用代码质量检查，包括语法错误、TypeScript 类型检查和 Prettier 代码美化。在提交代码前、完成功能开发后、或用户要求检查代码质量时使用。 Use when this capability is needed.
metadata:
  author: aquarius-wing
---

# 代码质量检查

## 使用场景

- 完成代码修改后，提交前进行质量检查
- 用户要求检查代码、格式化代码、或修复类型错误时
- 代码审查时验证代码质量

## 检查流程

按以下顺序执行检查：

```
检查进度：
- [ ] Step 1: TypeScript 类型检查
- [ ] Step 2: Prettier 代码格式化
- [ ] Step 3: 验证修复结果
```

---

## Step 1: TypeScript 类型检查

运行 TypeScript 编译器检查语法和类型错误：

```bash
# 检查整个项目
pnpm build

# 检查特定包
pnpm --filter <package-name> build
```

**常见包名：**
- `@repo/db` - 数据库包
- `@repo/web` - Web 应用
- `@repo/worker` - Worker 应用

### 处理类型错误

1. 阅读错误信息，定位问题文件和行号
2. 修复类型定义或代码逻辑
3. 重新运行类型检查直到通过

**常见错误及修复：**

| 错误类型 | 示例 | 修复方式 |
|---------|------|---------|
| 类型不匹配 | `Type 'string' is not assignable to 'number'` | 检查变量类型声明 |
| 缺少属性 | `Property 'x' is missing` | 补充必需属性或设为可选 |
| 未定义变量 | `Cannot find name 'x'` | 导入或声明变量 |

---

## Step 2: Prettier 代码格式化

运行 Prettier 自动格式化代码：

```bash
# 格式化所有文件
pnpm prettier --write .

# 格式化特定目录
pnpm prettier --write "apps/web/src/**/*.{ts,tsx}"
pnpm prettier --write "packages/db/src/**/*.ts"

# 仅检查格式（不修改文件）
pnpm prettier --check .
```

### Prettier 配置

项目使用根目录的 Prettier 配置。如需修改，编辑以下文件：
- `.prettierrc` - 格式化规则
- `.prettierignore` - 忽略文件

---

## Step 3: 验证修复结果

完成修复后，再次运行所有检查确认通过：

```bash
# 完整检查流程
pnpm build && pnpm prettier --check .
```

**检查通过标准：**
- ✅ TypeScript 编译无错误
- ✅ Prettier 检查无格式问题

---

## 快速检查命令

一键运行所有检查：

```bash
pnpm build && pnpm prettier --check .
```

一键修复格式问题：

```bash
pnpm prettier --write .
```

---

## 常见问题排查

### Prettier 与项目代码冲突

如果 Prettier 格式化后导致其他问题：
1. 检查 `.prettierignore` 是否需要排除特定文件
2. 确认编辑器是否启用了保存时自动格式化

### TypeScript 错误太多无法定位

```bash
# 只检查特定文件
pnpm tsc --noEmit path/to/file.ts

# 查看详细错误信息
pnpm build 2>&1 | head -50
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aquarius-wing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
