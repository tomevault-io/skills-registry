---
name: js-agents-entropy-scan
description: | Use when this capability is needed.
metadata:
  author: mksteady
---

# JS Agents 代码降熵扫描

针对纯 JS + JSDoc 微内核架构的代码质量扫描工具。

## 使用方式

```bash
/js-agents-entropy-scan              # 执行完整扫描
/js-agents-entropy-scan --fix        # 扫描并修复
/js-agents-entropy-scan --check 1,3  # 仅执行指定检查项
```

## 检查项

| # | 检查项 | 风险 | 说明 |
|---|--------|------|------|
| 1 | JSDoc 覆盖率 | 中 | 导出函数/类缺少类型注解 |
| 2 | TODO/FIXME 清理 | 低 | 未完成的技术债务标记 |
| 3 | Console 残留 | 中 | 生产代码中的调试输出 |
| 4 | 错误处理规范 | 高 | catch 块吞没错误或重新抛出时丢失上下文 |
| 5 | 导出一致性 | 中 | index.js 未导出模块内公开 API |
| 6 | 死代码检测 | 低 | 未使用的导出或内部函数 |
| 7 | 异步错误处理 | 高 | async 函数缺少 try-catch 或未处理 rejection |
| 8 | 模块循环依赖 | 高 | 模块间循环 import 导致运行时问题 |

## 扫描命令

### 1. JSDoc 覆盖率

```bash
# 找出缺少 JSDoc 的导出函数
rg "^export (async )?function \w+\(" js/agents --glob '*.js' -B2 | \
  grep -v "@param\|@returns\|@typedef"
```

**修复策略**：为导出函数添加 `@param`、`@returns` 注解。

### 2. TODO/FIXME 清理

```bash
rg "TODO|FIXME|HACK|XXX" js/agents --glob '*.js' -n
```

**修复策略**：评估每个标记，完成或创建 Issue 追踪。

### 3. Console 残留

```bash
# 排除 cli/、examples/、test 文件
rg "console\.(log|warn|error|debug)" js/agents --glob '*.js' \
  --glob '!cli/*' --glob '!**/examples/*' --glob '!*test*.js' -n
```

**修复策略**：
- 替换为 `createLogger()`
- 或使用 `if (DEBUG)` 条件包裹

### 4. 错误处理规范

```bash
# 检查空 catch 块
rg "catch\s*\([^)]*\)\s*\{\s*\}" js/agents --glob '*.js' -n

# 检查 catch 后直接 throw err（丢失堆栈）
rg "catch.*\{[^}]*throw\s+\w+\s*;?\s*\}" js/agents --glob '*.js' -U -n
```

**修复策略**：
- 空 catch：添加日志或重新抛出
- 直接 throw：使用 `throw new Error('context', { cause: err })`

### 5. 导出一致性

```bash
# 检查 index.js 是否导出了目录下的所有模块
for dir in js/agents/*/; do
  if [ -f "${dir}index.js" ]; then
    echo "=== $dir ==="
    # 列出目录下的 .js 文件
    ls "${dir}"*.js 2>/dev/null | grep -v index.js | while read f; do
      base=$(basename "$f" .js)
      if ! grep -q "from.*['\"]\./${base}" "${dir}index.js"; then
        echo "Missing export: $base"
      fi
    done
  fi
done
```

### 6. 死代码检测

```bash
# 查找未被引用的导出
for f in $(rg -l "^export " js/agents --glob '*.js'); do
  exports=$(rg "^export (const|function|class) (\w+)" "$f" -or '$2')
  for exp in $exports; do
    # 搜索其他文件是否引用
    count=$(rg "import.*\b${exp}\b|from.*${exp}" js/agents --glob '*.js' -c 2>/dev/null | wc -l)
    if [ "$count" -eq 0 ]; then
      echo "Unused export: $exp in $f"
    fi
  done
done
```

### 7. 异步错误处理

```bash
# 查找没有 try-catch 的 async 函数（简化检测）
rg "async function \w+\([^)]*\)\s*\{" js/agents --glob '*.js' -A10 | \
  grep -B10 "async function" | grep -v "try\s*{"
```

### 8. 模块循环依赖

使用 madge 或手动分析：

```bash
# 简化检测：查找相互 import 的文件对
rg "^import.*from ['\"]\./" js/agents --glob '*.js' -n | \
  awk -F: '{print $1, $2}' | sort | uniq -d
```

## 执行流程

1. **扫描阶段**：依次执行 8 项检查，收集问题清单
2. **报告阶段**：生成结构化报告，按风险分级
3. **确认阶段**：询问用户是否修复
4. **修复阶段**：按优先级自动修复
5. **验证阶段**：运行 `npm test` 确保无回归

## 报告格式

```
📊 JS Agents 代码降熵扫描报告

扫描范围：js/agents/**/*.js
文件数量：393 个
扫描时间：2025-01-12 14:30

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

【汇总】

检查项             | 发现 | 风险
-------------------|------|------
JSDoc 覆盖率       | 45   | 中
TODO/FIXME 清理    | 52   | 低
Console 残留       | 12   | 中
错误处理规范       | 8    | 高
导出一致性         | 5    | 中
死代码检测         | 3    | 低
异步错误处理       | 15   | 高
模块循环依赖       | 2    | 高

总计：142 处技术债务

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

【详细清单】

🔴 高优先级（25 处）

1. 错误处理规范（8 处）
   - js/agents/vfs/vfs.storage.js:45 - 空 catch 块
   - js/agents/llm/rate-limit.js:78 - catch 后直接 throw

2. 异步错误处理（15 处）
   - js/agents/mcp/mcp-client.js:120 - async 函数无 try-catch
   ...

3. 模块循环依赖（2 处）
   - js/agents/core/kernel.js ↔ js/agents/core/plugin.js
   ...

[更多详情...]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

是否需要修复？
1. 全部修复
2. 仅修复高优先级
3. 自定义选择
4. 不修复
```

## 修复约束

- 修复后必须通过 `npm test`
- JSDoc 修复保守：仅添加明显的类型注解
- Console 替换为项目内的 `createLogger()`
- 错误处理使用 `{ cause }` 保留原始错误
- 不删除可能被动态引用的导出

## 项目特定规范

### 日志规范

```javascript
// ❌ 错误
console.log('debug:', data);

// ✅ 正确
import { createLogger } from '../shared/utils/logger.js';
const logger = createLogger('module-name');
logger.debug('message', { data });
```

### 错误处理规范

```javascript
// ❌ 错误
try { ... } catch (e) { throw e; }

// ✅ 正确
try { ... } catch (e) {
  throw new Error(`Context: ${e.message}`, { cause: e });
}
```

### JSDoc 规范

```javascript
// ✅ 导出函数必须有类型注解
/**
 * @param {string} name
 * @param {Record<string, unknown>} [options]
 * @returns {Promise<Result>}
 */
export async function doSomething(name, options = {}) { ... }
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mksteady) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
