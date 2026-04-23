---
name: code-cleanup-methodology
description: Systematic approach to clean up and organize Android project code after multiple feature implementations Use when this capability is needed.
metadata:
  author: devonstee
---

# Skill: Code Cleanup Methodology

**Last Verified:** 2026-01-23
**Applicable SDK:** Android 14+ (API 34+)
**Dependencies:** code-quality-audit, best-practice-check

## Purpose

Systematic approach to clean up and organize Android project code after multiple feature implementations.

---

## 何时执行整理 (When to Clean Up)

触发条件（满足任一即应整理 / triggers: any of below）：

- ✅ 完成 3-5 个功能开发 (after shipping 3-5 features)
- ✅ 发现代码中有大量注释掉的旧代码 (large blocks of commented-out code)
- ✅ 文件超过 500 行且包含冗余代码 (file >500 lines with redundancy)
- ✅ 重构后遗留的 legacy 代码注释 (legacy comments left after refactor)
- ✅ 用户明确要求 "项目整理" (explicit user request: project cleanup)

---

## 整理流程 (Cleanup Workflow)

### Phase 1: 代码审查与分类 (Code Audit)

**目标 / Goal：** 识别需要清理的内容 (identify cleanup targets)

#### 1.1 检查注释掉的代码 (Dead Code)

```bash
# 搜索注释掉的代码块
grep -r "^[ ]*// [a-zA-Z]" app/src/main/java --include="*.kt" | grep -v "^[ ]*//" > /tmp/comments.txt
```

**识别标准 / Identification:**

- `// private var xxx -> Moved to ...` - 迁移说明注释 (migration note)
- `// override fun xxx() {} - Removed` - 删除函数注释 (removed function)
- `// stopSecondsTimer() -> Not needed` - 过时调用注释 (obsolete call)
- 连续 3 行以上的注释代码块 (3+ consecutive commented lines)

**处理方式 / Action:**

- ❌ **删除** - 已迁移、已废弃的说明注释 (delete migrated/obsolete)
- ⚠️ **保留** - 架构决策、业务逻辑说明、TODO 注释 (keep rationale/TODO)

#### 1.2 检查空白行和格式 (Whitespace)

```kotlin
// ❌ 不良示例 - 过多空行
fun functionA() { }



fun functionB() { }

// ✅ 良好示例 - 适度空行
fun functionA() { }

fun functionB() { }
```

**规则 / Rules：**

- 函数间：1 空行 (one blank line between functions)
- 类内部分组：2 空行最多 (max 2 blank lines between groups)
- 文件末尾：1 空行 (single trailing newline)

#### 1.3 检查导入语句 (Imports)

**需清理的导入 / Clean up imports:**

- 未使用的导入（IDE 会标灰 / unused imports)
- 重复导入 (duplicates)
- 通配符导入（`import com.example.*` / wildcard)

**工具：**

```bash
# Android Studio: Code > Optimize Imports (Ctrl+Alt+O / Cmd+Opt+O)
./gradlew lintKotlin  # Kotlin lint 检查
```

#### 1.4 检查冗余代码 (Redundant Code)

**识别模式 / Patterns to flag：**

```kotlin
// ❌ 冗余的条件判断
if (enabled) {
    function(enabled = true)
} else {
    function(enabled = false)
}

// ✅ 简化版本
function(enabled = enabled)
```

**常见冗余 / Common cases：**

- 重复的条件分支 (duplicated branches)
- 不必要的临时变量 (unneeded temps)
- 过度封装的单行函数 (over-wrapped one-liners)

---

### Phase 2: 代码结构优化 (Code Structure)

#### 2.1 函数职责检查

**单一职责原则 (SRP)：**

```kotlin
// ❌ 违反 SRP - 函数做了太多事
fun setupUI() {
    initViews()
    loadData()
    setupListeners()
    applyTheme()
    validatePermissions()
}

// ✅ 符合 SRP - 分离关注点
fun setupUI() {
    initViews()
    setupListeners()
}

fun loadInitialData() {
    loadData()
    validatePermissions()
}
```

**检查清单 / Checklist：**

- [ ] 函数超过 50 行 → 考虑拆分 (function >50 lines → split)
- [ ] 函数名包含 "and" → 可能职责不单一 ("and" in name → likely multi-responsibility)
- [ ] 函数有超过 5 个参数 → 考虑参数对象 (>5 params → wrap into parameter object)

#### 2.2 类职责检查

**文件大小阈值 / File size thresholds：**

- < 300 行 - ✅ 健康 (healthy)
- 300-500 行 - ⚠️ 注意 (watch)
- 500-800 行 - 🔴 需重构 (refactor soon)
- \> 800 行 - 🚨 立即拆分 (split now)

**拆分策略：**

```kotlin
// ❌ 巨型 Activity (800+ 行)
class FullscreenClockActivity : Activity {
    // UI setup
    // Data binding
    // Network calls
    // State management
    // ...
}

// ✅ 拆分为 Controllers
class FullscreenClockActivity : Activity {
    private lateinit var uiController: UIStateController
    private lateinit var dataController: DataController
    private lateinit var networkController: NetworkController
}
```

#### 2.3 命名一致性检查

**命名约定：**

| 类型 | 约定 | 示例 |
| --- | --- | --- |
| 变量 | camelCase | `settingsManager` |
| 常量 | UPPER_SNAKE | `BRIGHTNESS_MAX` |
| 私有变量 | camelCase | `_internalState` (可选前缀) |
| 函数 | camelCase, 动词开头 | `updateTime()`, `applyTheme()` |
| 类 | PascalCase | `SettingsCoordinator` |
| 接口 | PascalCase, 不加 I 前缀 | `SettingsProvider` |

**检查清单 / Checklist：**

- [ ] 变量名有明确含义（避免 `data`, `temp`, `x`）(meaningful names)
- [ ] 布尔变量以 `is/has/should` 开头 (boolean prefix rule)
- [ ] 集合变量使用复数形式 (collections use plural)

---

### Phase 3: 文档和注释优化 (Documentation)

#### 3.1 KDoc 完整性检查

**需要 KDoc 的地方：**

```kotlin
/**
 * 管理 UI 状态和可见性逻辑。
 * 
 * 处理 Zen Mode、秒表显示、交互状态的优先级系统。
 * 
 * @property binding Activity 的 ViewBinding
 * @property viewModel 共享的 ViewModel
 */
class UIStateController(
    private val binding: ActivityMainBinding,
    private val viewModel: FullscreenClockViewModel
) {
    /**
     * 更新秒表的可见性。
     * 
     * 优先级系统：
     * 1. 显示秒表 - 永久可见
     * 2. 显示齿轮和主题切换 - 基于交互状态
     * 3. 灯光按钮 - 当灯光开启时豁免隐藏
     */
    fun updateSecondsVisibility() { }
}
```

**规则 / Rules：**

- ✅ **必须** - Public API、复杂逻辑、非显而易见的行为 (required for public/complex)
- ⚠️ **可选** - 简单的 getter/setter、重写的方法 (optional for trivial overrides)
- ❌ **避免** - 重复代码的注释（`// Set the value` for `setValue()`）(avoid redundant comments)

#### 3.2 行内注释优化

**好的注释 / Good inline comments：**

```kotlin
// Priority: Seconds mode takes absolute precedence - force light off
forceTurnOffLight()

// Decouple physics from layout: Visual size (64dp) != Layout size (96dp for glow)
outerRadius = (visualDiameter / 2f) - (PADDING_DP * density)
```

**坏的注释（应删除）/ Bad comments to delete：**

```kotlin
// Set the manager  ❌ - 重复代码
settingsManager = AppSettingsManager(this)

// Call the function  ❌ - 无意义
updateTime()

// TODO: Fix this later  ❌ - 不明确的 TODO
```

**TODO 注释规范 / TODO style guide：**

```kotlin
// ✅ 良好的 TODO
// TODO(username, 2026-01-22): 当 Android 15 发布后，迁移到新的 Permission API
// See: https://developer.android.com/reference/...

// ❌ 不良的 TODO
// TODO: fix
```

---

### Phase 4: 依赖和导入整理 (Dependencies)

#### 4.1 Gradle 依赖清理

**检查未使用的依赖 / Detect unused deps：**

```bash
./gradlew app:dependencies > dependencies.txt
# 手动审查是否有未使用的库
```

**常见冗余依赖 / Common redundancies：**

- 重复的版本声明 (duplicate version declarations)
- 已被 AndroidX 替代的库 (deprecated vs AndroidX)
- 测试依赖放在 `implementation` 而非 `testImplementation` (test libs in wrong config)

#### 4.2 导入优化

**Kotlin 导入顺序 / Import order：**

1. Android framework imports
2. Third-party library imports
3. Project imports
4. Java/Kotlin standard library

**使用 Android Studio：**

```text
Settings > Editor > Code Style > Kotlin > Imports
- ✅ Use single name import
- ✅ Sort imports alphabetically
```

---

### Phase 5: 性能和内存优化 (Performance)

#### 5.1 内存泄漏检查

**常见泄漏模式 / Typical leaks：**

```kotlin
// ❌ 未取消的 Timer
private var lightTimer: CountDownTimer? = null

// ✅ 在 onDestroy 中清理
override fun onDestroy() {
    lightTimer?.cancel()
    lightTimer = null
}
```

**检查清单 / Checklist：**

- [ ] Listeners 在 `onDestroy` 中置空 (null listeners in onDestroy)
- [ ] Coroutines 使用 `viewModelScope` / `lifecycleScope` (use scoped coroutines)
- [ ] 动画在 `onPause` 中停止 (stop animations onPause)
- [ ] 定时器在组件销毁时取消 (cancel timers on destroy)

#### 5.2 冗余分配检查

**onDraw 中的零分配：**

```kotlin
// ❌ 在 onDraw 中创建对象
override fun onDraw(canvas: Canvas) {
    val paint = Paint()  // 每帧分配！
    canvas.drawCircle(x, y, r, paint)
}

// ✅ 重用对象
private val paint = Paint()
override fun onDraw(canvas: Canvas) {
    canvas.drawCircle(x, y, r, paint)
}
```

参考：[Android High-Performance Custom View Skill](../android-highperf-customview/SKILL.md)

---

### Phase 6: 测试和验证 (Testing)

#### 6.1 构建验证

```bash
# 完整构建
./gradlew clean build

# 仅编译检查
./gradlew assembleDebug

# Lint 检查
./gradlew lintDebug
```

#### 6.2 Git 提交策略

**整理提交最佳实践：**

```bash
# 分离功能和整理
git add -p  # 交互式选择

# 提交信息模板
refactor: cleanup FullscreenClockActivity

- Remove dead code and legacy comments
- Organize imports
- Extract helper methods for better readability

Affected files:
- FullscreenClockActivity.kt (-50 lines)
- UIStateController.kt (formatting)
```

**提交粒度 / Commit granularity：**

- ✅ **分离** - 功能开发 vs 代码整理 (separate feature vs cleanup)
- ✅ **分离** - 不同文件的整理（便于 review）(separate per file group)
- ❌ **避免** - 混合功能和整理在一个提交 (avoid mixed commits)

---

## 整理检查清单 (Cleanup Checklist)

使用此清单确保全面整理：

### 代码层面

- [ ] 删除所有注释掉的代码 (remove commented-out code)
- [ ] 移除未使用的导入 (remove unused imports)
- [ ] 统一空白行使用（函数间 1 行，分组间 1-2 行）(consistent whitespace)
- [ ] 检查函数长度（< 50 行为佳）(functions <50 lines)
- [ ] 检查文件长度（< 500 行为佳）(files <500 lines)
- [ ] 统一命名风格（camelCase, PascalCase）(consistent naming)
- [ ] 移除 `println` / `Log.d` 调试语句 (remove debug logs)

### 架构层面

- [ ] 确认单一职责原则 (single responsibility)
- [ ] 检查循环依赖 (no cyclic deps)
- [ ] 验证 Controller/Manager 职责清晰 (clear roles)
- [ ] 确认数据流向单向 (one-way data flow)

### 文档层面

- [ ] Public API 有 KDoc (public APIs documented)
- [ ] 复杂逻辑有解释注释 (complex logic explained)
- [ ] TODO 注释有明确的负责人和时间 (TODO owner + date)
- [ ] README / AGENTS.md 是最新的 (docs up to date)

### 性能层面

- [ ] onDestroy 中清理资源 (cleanup resources onDestroy)
- [ ] onDraw 中无对象分配 (zero allocation in onDraw)
- [ ] 无内存泄漏（Timer、Listener 已清理）(no leaks timers/listeners)

### 验证层面

- [ ] `./gradlew assembleDebug` 成功 (build passes)
- [ ] `./gradlew lintDebug` 无严重警告 (lint clean)
- [ ] 代码 review 通过（如果是团队项目）(code review passed)

---

## 工具和脚本 (Tools)

### 自动化检查脚本

```bash
#!/bin/bash
# cleanup-check.sh - 代码整理检查脚本

echo "🔍 检查注释掉的代码..."
grep -r "^[ ]*//.*->" app/src/main/java --include="*.kt" | wc -l

echo "🔍 检查过长的文件..."
find app/src/main/java -name "*.kt" -exec wc -l {} \; | awk '$1 > 500 {print $2 " (" $1 " lines)"}'

echo "🔍 检查过长的函数..."
# 需要更复杂的 AST 解析，建议使用 detekt

echo "✅ 运行 Lint..."
./gradlew lintDebug

echo "✅ 构建检查..."
./gradlew assembleDebug
```

### Detekt 配置（可选）

在 `app/build.gradle.kts` 添加：

```kotlin
plugins {
    id("io.gitlab.arturbosch.detekt") version "1.23.0"
}

detekt {
    config.setFrom(files("$rootDir/config/detekt/detekt.yml"))
}
```

---

## 整理频率建议 (Recommended Frequency)

| 项目阶段 | 整理频率 | 耗时估算 |
| --- | --- | --- |
| 快速开发期 | 每 5 个功能 | 30-60 分钟 |
| 稳定迭代期 | 每个 Sprint | 1-2 小时 |
| 维护期 | 按需 | 15-30 分钟 |

---

## 常见问题 (FAQ)

**Q: 整理时应该删除所有注释吗？**
A: 不应该。保留以下注释：

- 架构决策（为什么这样设计）
- 业务逻辑（为什么需要这个检查）
- 性能优化（为什么用这个算法）
- 已知问题的 Workaround

**Q: 如何判断代码是否应该删除？**
A: 遵循 3 个月规则：

- 如果代码被注释超过 3 个月且无人提及 → 删除
- 如果是临时 workaround 且已有正式方案 → 删除
- 如果 Git 历史中可以找到 → 删除

**Q: 整理会不会引入新 bug？**
A: 降低风险的方法：

- ✅ 只删除注释，不修改逻辑
- ✅ 每次整理后运行完整测试
- ✅ 分小批次提交，便于回滚
- ✅ 使用 IDE 的重构功能（而非手动编辑）

**Q: 团队协作时如何协调整理？**
A: 最佳实践：

- 📅 在 Sprint 结束时集中整理
- 👥 由熟悉代码的人主导整理
- 📝 整理前先在团队会议中讨论
- 🔄 整理 PR 应快速 review 和合并

---

## 参考资源 (References)

- [Clean Code (Robert C. Martin)](https://www.oreilly.com/library/view/clean-code-a/9780136083238/)
- [Android Best Practices](https://developer.android.com/guide)
- [Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html)
- [Detekt - Static Code Analysis](https://detekt.dev/)

---

**最后提醒：** 代码整理是持续的过程，不是一次性任务。保持定期整理的习惯，项目会更健康、更易维护！

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devonstee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
