---
name: test-dev
description: 测试开发专家，基于JUnit 5 + AssertJ + Mockito + JaCoCo。 Use when this capability is needed.
metadata:
  author: whtoo
---

# 测试开发

## 🎯 垂直职责
**单一职责**: 标准化测试框架 - JUnit 5 + AssertJ + Mockito + JaCoCo

## 📦 测试框架

### 依赖版本
```xml
<junit.version>5.11.3</junit.version>
<assertj.version>3.27.0</assertj.version>
<mockito.version>5.8.0</mockito.version>
<jacoco.version>0.8.12</jacoco.version>
```

### 覆盖率要求
| 模块 | 行覆盖率 | 分支覆盖率 |
|------|----------|------------|
| 核心引擎 | ≥90% | ≥85% |
| 指令实现 | ≥95% | ≥90% |
| **总体** | **≥85%** | **≥80%** |

## 📝 测试编写规范

### 命名规则
**格式**: `test{场景}_{期望结果}_when{条件}`

```java
// ✅ 好示例
void testIAddReturnsSum_whenStackHasTwoIntegers() { }
void testTypeCheckerRejectsInvalidBinaryOp_whenOperandsIncompatible() { }

// ❌ 差示例
void test1() { }
void testMethod() { }
```

### 测试结构 (Given-When-Then)
```java
@Test
@DisplayName("应正确执行 IADD 指令")
void testIAddInstruction() {
    // Given - 准备测试数据
    OperandStack stack = new OperandStack(10);
    stack.push(10);
    stack.push(20);

    // When - 执行被测操作
    instruction.execute(context);

    // Then - 验证结果
    assertThat(stack.pop()).isEqualTo(30);
}
```

### AssertJ断言
```java
// ✅ 推荐: AssertJ流畅断言
assertThat(result)
    .isNotNull()
    .isEqualTo(expected);

assertThat(list)
    .isNotEmpty()
    .hasSize(3);

// ❌ 避免: JUnit旧式断言
assertEquals(expected, result);  // 顺序易错
```

## 🎯 测试类型

### 单元测试
```java
@Tag("unit")
class InstructionTest {
    @Test
    @DisplayName("加法指令应正确计算")
    void testAdd() {
        // Given
        AddInstruction add = new AddInstruction();

        // When
        add.execute(context);

        // Then
        assertThat(result).isEqualTo(8);
    }
}
```

### 集成测试
```java
@Tag("integration")
class CompilerPipelineTest {
    @Test
    @DisplayName("完整编译流程应处理循环")
    void testFullCompilation() {
        // Given
        String source = loadTestProgram("fibonacci.cymbol");

        // When
        CompilationResult result = compiler.compile(source);

        // Then
        assertThat(result.isSuccess()).isTrue();
    }
}
```

### 参数化测试
```java
@ParameterizedTest
@ValueSource(ints = {0, 1, 10, 100})
@DisplayName("数据栈应处理各种值")
void testStackPushPop(int value) {
    stack.push(value);
    assertThat(stack.pop()).isEqualTo(value);
}
```

## 🛠️ 常用命令

```bash
# 运行测试
mvn test                                    # 全部测试
mvn test -pl ep21                           # 特定模块
mvn test -Dtest="*Optimizer*"              # 特定测试

# 覆盖率
mvn jacoco:report                          # 生成报告
open ep21/target/site/jacoco/index.html   # 查看报告
mvn jacoco:check                           # 检查覆盖率
```

## ⚠️ 常见问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| 测试独立失败 | 共享静态状态 | 使用@BeforeEach初始化 |
| 测试有逻辑 | if/else分支 | 拆分成多个测试 |
| 覆盖率不足 | 缺少边界测试 | 添加边界条件测试 |

---
*版本: v1.0 | 垂直职责: 测试开发 | 2025-12-23*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whtoo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
