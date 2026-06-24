---
name: compiler-dev
description: 编译器后端开发专家，专注于IR、CFG、SSA、数据流分析和优化Pass。 Use when this capability is needed.
metadata:
  author: whtoo
---

# 编译器后端开发

## 🎯 垂直职责
**单一职责**: 编译器中后端技术 - 符号表、类型系统、IR、CFG、SSA、优化

## 📦 核心能力

### 1. 符号表与类型系统 (EP6-EP10)
- **位置**: `ep20/src/main/java/org/teachfx/antlr4/ep20/symtab/`
- **作用域**: `scope/GlobalScope`, `scope/LocalScope`
- **符号**: `symbol/VariableSymbol`, `symbol/MethodSymbol`
- **类型**: `type/Type`, `type/BuiltInType`, `type/StructType`

### 2. 中间表示 (EP11-EP17)
- **位置**: `ep20/src/main/java/org/teachfx/antlr4/ep20/ir/`
- **表达式**: `ir/expr/` (BinExpr, UnaryExpr, ConstVal)
- **语句**: `ir/stmt/` (Assign, Jump, ConditionalJump)
- **构建器**: `CymbolIRBuilder.java`

### 3. 控制流图 (EP16-EP17)
- **位置**: `ep20/src/main/java/org/teachfx/antlr4/ep20/pass/cfg/`
- **核心**: `ControlFlowAnalysis.java`, `CFG.java`
- **基本块**: `BasicBlock<IRNode>`

### 4. SSA与优化 (EP21)
- **位置**: `ep21/src/main/java/org/teachfx/antlr4/ep21/`
- **SSA**: `analysis/ssa/SSAGraph.java`
- **数据流**: `analysis/dataflow/` (LiveVariableAnalysis, ReachingDefinitions)
- **优化**: `pass/cfg/` (ConstantFolding, CSE, DCE)

## 🔗 关系图
→ **ep-navigator** (识别EP范围)
→ **antlr4-dev** (AST → IR转换)
← **vm-dev** (IR → 字节码)

## 🚀 快速开始

### 实现新的优化Pass
```bash
# 1. 创建优化器 (实现IFlowOptimizer<IRNode>)
vim ep21/src/main/java/.../pass/cfg/NewOptimizer.java

# 2. 标准模板
public class NewOptimizer implements IFlowOptimizer<IRNode> {
    @Override
    public void onHandle(CFG<IRNode> cfg) {
        // 遍历基本块
        for (BasicBlock<IRNode> block : cfg) {
            // 优化逻辑
        }
    }
}

# 3. 创建测试
vim ep21/src/test/java/.../pass/cfg/NewOptimizerTest.java

# 4. 运行测试
mvn test -pl ep21 -Dtest="*NewOptimizer*"
```

### SSA转换流程
```bash
# 1. 构建支配树
cfg.computeDominanceFrontier();

# 2. 插入Φ函数
ssa.insertPhiFunctions();

# 3. 变量重命名
ssa.renameVariables();

# 4. 验证
mvn test -pl ep21 -Dtest="*SSATest"
```

## 📊 数据流分析模板

```java
// 标准数据流分析框架
public class MyDataFlowAnalysis extends AbstractDataFlowAnalysis<Set<Var>, Set<Var>> {
    @Override
    public Set<Var> getBoundaryCondition() {
        return new HashSet<>(); // 初始状态
    }

    @Override
    public Set<Var> getInitialFlow() {
        return new HashSet<>(); // 默认状态
    }

    @Override
    public Set<Var> merge(List<Set<Var>> inputs) {
        Set<Var> result = new HashSet<>();
        for (Set<Var> input : inputs) {
            result.addAll(input); // 合并操作
        }
        return result;
    }

    @Override
    public Set<Var> flowFunction(BasicBlock<IRNode> block, Set<Var> input) {
        Set<Var> output = new HashSet<>(input);
        // 传递函数: 根据block内容修改output
        return output;
    }
}
```

## 🛠️ 常用命令

```bash
# 编译器后端
mvn compile -pl ep20                    # 编译EP20
mvn compile -pl ep21                    # 编译EP21

# 测试
mvn test -pl ep20 -Dtest="*IR*"         # 测试IR生成
mvn test -pl ep20 -Dtest="*CFG*"        # 测试CFG
mvn test -pl ep21 -Dtest="*SSA*"        # 测试SSA
mvn test -pl ep21 -Dtest="*Optimizer*"  # 测试优化

# 覆盖率
mvn jacoco:report -pl ep21
open ep21/target/site/jacoco/index.html
```

## 📐 IR节点速查

| 类型 | 类名 | 字段 | 用途 |
|------|------|------|------|
| 常量 | `ConstVal<T>` | `val: T` | 字面量 |
| 变量 | `VarSlot` | `name: String` | 变量引用 |
| 二元运算 | `BinExpr` | `lhs, rhs: VarSlot`, `op: BinaryOpType` | a + b |
| 一元运算 | `UnaryExpr` | `expr: VarSlot`, `op: UnaryOpType` | -a |
| 赋值 | `Assign` | `lhs: VarSlot`, `rhs: Operand` | x = y |
| 标签 | `Label` | `name: String`, `bb: BasicBlock` | 基本块标签 |
| 跳转 | `Jump` | `target: BasicBlock` | goto L |
| 条件跳转 | `CondJump` | `cond: VarSlot`, `true/false: BasicBlock` | if (cond) |

## ⚠️ 常见问题

| 问题 | 原因 | 解决方案 |
|------|------|----------|
| IR类型不匹配 | `Assign.rhs`是`Operand`不是`Expr` | 使用`ConstVal`或`VarSlot` |
| CFG边方向错误 | `getSucceed(id)`返回`Set<Integer>` | 遍历ID再查BasicBlock |
| SSA重命名失败 | 未计算支配边界 | 先调用`computeDominanceFrontier()` |
| 优化Pass未生效 | 未注册到优化管道 | 添加到`OptimizerPipeline` |

---
*版本: v1.0 | 垂直职责: 编译器后端 | 2025-12-23*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/whtoo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
