---
name: pix-class-to-protobuf
description: This skill should be used when user asks to generate protobuf protocol files from Java .class files or reverse engineer protobuf from compiled bytecode. Activates with phrases like class to protobuf, convert class to proto, generate protobuf from class, reverse engineer protobuf from class, class protobuf conversion, bytecode to protobuf. Provides comprehensive guidance for parsing Java @Protobuf annotations from .class files using javap and generating .proto files. Use when this capability is needed.
metadata:
  author: pixb
---

# pix-class-to-protobuf 技能

## 📂 基础信息

| 属性 | 值 |
| :--- | :--- |
| **名称** | pix-class-to-protobuf |
| **版本** | 1.2.0 |
| **类型** | 代码生成技能 |
| **核心功能** | 从 Java .class 文件生成 Protobuf 协议文件 |
| **适用环境** | Trae |

## 🎯 核心目标

### 解决的问题

| 痛点 | 解决方案 |
| :--- | :--- |
| Java 源文件缺失，只有 .class 文件 | 使用 javap 解析字节码提取信息 |
| 手动编写 protobuf 文件耗时且容易出错 | 自动从 .class 文件生成 .proto 文件 |
| 字段类型映射复杂 | 提供完整的类型映射表 |
| 注解信息提取困难 | 解析 @Protobuf 注解提取字段信息 |
| import 语句生成繁琐 | 自动收集和生成 import 语句 |

### 适用场景

1. **逆向工程**：从编译后的 Java 代码恢复 protobuf 协议定义
2. **协议迁移**：将现有的 Java protobuf 代码迁移到标准 protobuf 格式
3. **文档生成**：从 .class 文件生成协议文档
4. **代码审查**：验证 .class 文件中的 protobuf 定义是否正确

## 🚀 使用方法

### 激活方式

当用户询问以下类型的问题时，pix-class-to-protobuf 会自动激活：

- "从 class 文件生成 protobuf"
- "将 class 转换为 proto"
- "生成 protobuf 协议文件"
- "从字节码生成 proto"
- "反编译 class 文件生成 protobuf"
- "class 到 protobuf 转换"

### 预期输出

pix-class-to-protobuf 会提供：
1. 完整的 .proto 文件生成流程
2. 字段信息提取和类型映射
3. import 语句自动生成
4. 常见问题的解决方案
5. 调试技巧和最佳实践

## 📚 文档结构

### 核心文档

| 文档 | 描述 | 路径 |
| :--- | :--- | :--- |
| **实现细节** | 完整的生成脚本实现和核心流程 | [docs/01-implementation.md](docs/01-implementation.md) |
| **关键技术点** | 常量池解析、注解处理、类型映射等关键技术 | [docs/02-key-techniques.md](docs/02-key-techniques.md) |
| **最佳实践** | 调试技巧、错误处理、性能优化等最佳实践 | [docs/03-best-practices.md](docs/03-best-practices.md) |
| **常见问题** | 常见问题的解决方案和调试技巧 | [docs/04-faq.md](docs/04-faq.md) |

### 脚本文档

| 文档 | 描述 | 路径 |
| :--- | :--- | :--- |
| **脚本使用指南** | 脚本的使用方法、参数说明和示例 | [scripts/README.md](scripts/README.md) |

### 示例代码

| 文档 | 描述 | 路径 |
| :--- | :--- | :--- |
| **示例文件** | 示例 .class 和 .proto 文件 | [references/examples/](references/examples/) |

## 🛠️ 快速开始

### 1. 准备工作

```bash
# 创建 .class 文件目录结构
mkdir -p protobuf-class-to-proto/classes/com/dnfm/mina/protobuf

# 复制 .class 文件到目标目录
cp -r path/to/classes/*.class protobuf-class-to-proto/classes/com/dnfm/mina/protobuf/
```

### 2. 运行脚本

```bash
# 生成所有 proto 文件
python generate_proto_from_class.py

# 使用分批调试（推荐用于大量文件）
python generate_proto_from_class_batch.py --batch batch_1.txt
```

### 3. 验证结果

```bash
# 使用 buf lint 验证 proto 文件
cd proto/generated/batch_1_test
buf lint

# 使用 buf generate 生成 Java 代码
buf generate
```

## 🌟 核心功能

### 1. 字段信息提取

从 Java .class 文件中提取字段信息，包括：
- 字段名称
- 字段类型
- 字段序号
- 是否必需

### 2. 类型映射

自动将 Java 类型映射到 Protobuf 类型：
- 基本类型：int32, int64, string, bool 等
- 复杂类型：List, Map 等
- 枚举类型：ENUM 类型
- 自定义类型：消息类型

### 3. import 语句生成

自动收集和生成 import 语句：
- 识别自定义类型
- 生成正确的 import 路径
- 处理依赖关系

### 4. 特殊字符处理

自动处理 Java 类名中的特殊字符：
- 替换 `$` 为 `_`
- 处理泛型类型
- 确保符合 protobuf 语法规范

## 📋 分批调试

当需要处理大量 .class 文件时，建议使用分批调试策略：

### 分批测试策略

1. **第 1 批**：选择 10 个简单类型文件，测试基本功能
2. **第 2 批**：选择 15 个复杂类型文件，测试 ENUM、repeated、map 类型
3. **第 3 批**：选择 20 个有依赖关系的文件，测试 import 语句
4. **第 4 批**：选择 30 个文件，测试批量处理性能
5. **第 5 批**：生成所有文件，验证完整性

### 创建批次文件

```bash
# 创建批次文件，每行一个 .class 文件名
echo "RES_VERIFICATION_AUTH.class" > batch_1.txt
echo "REQ_VERIFICATION_AUTH.class" >> batch_1.txt

# 清理批次文件，移除注释行
Get-Content test_batch.txt | Where-Object { $_ -notlike '#*' -and $_ -notlike '' } | Set-Content test_batch_clean.txt
```

## 🚨 常见问题

### Q1: 为什么生成的 proto 文件有语法错误？

**原因**：类型名包含特殊字符（如 `$`）、字段名包含特殊字符、重复的字段标签

**解决方案**：替换特殊字符为 `_`、检查并跳过重复标签、使用 `buf lint` 验证生成的 proto 文件

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q1-为什么生成的-proto-文件有语法错误)

### Q2: 为什么某些字段没有被提取？

**原因**：字段没有 `@Protobuf` 注解、字段是 private 或 protected、正则表达式没有匹配到字段

**解决方案**：检查字段是否有 `@Protobuf` 注解、使用 `javap -p -v` 查看字段信息、调整正则表达式

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q2-为什么某些字段没有被提取)

### Q3: 为什么 import 语句没有生成？

**原因**：自定义类型没有被正确识别、类型名不满足 import 条件

**解决方案**：检查类型名是否以大写字母开头、确保类型名不等于当前类名、调试 `imported_types` 集合

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q3-为什么-import-语句没有生成)

### Q4: 为什么 ENUM 类型没有正确处理？

**原因**：`fieldType` 为 `ENUM`，但没有从 Java 类型中提取枚举类型名

**解决方案**：检查 Java 类型是否包含 `ENUM_`、从完整类名中提取枚举类型名、替换 `$` 为 `_`

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q4-为什么-enum-类型没有正确处理)

### Q5: 为什么 import 文件缺失？

**原因**：生成的 proto 文件包含 import 语句，但 import 的文件不在当前目录、import 的文件还没有生成

**解决方案**：创建 common 子目录、将 import 的文件复制到 common 目录中、对于缺失的文件，创建占位符文件

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q5-为什么-import-文件缺失)

### Q6: 为什么枚举类型字段生成错误？

**原因**：`FIELD_TYPE_MAP` 中包含了 `'ENUM': 'enum'`，导致枚举类型被错误映射、生成的字段类型为 `enum`，但 `enum` 是 protobuf 的保留关键字

**解决方案**：从 `FIELD_TYPE_MAP` 中移除 `'ENUM': 'enum'`、将 ENUM 类型检查移到 FIELD_TYPE_MAP 检查之前、从 Java 类型中提取枚举类型名

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q6-为什么枚举类型字段生成错误)

### Q7: 为什么文件不存在错误？

**原因**：批次文件中指定的 .class 文件不存在、文件路径错误或文件被删除

**解决方案**：检查文件路径是否正确、确保文件存在于指定目录中、从批次文件中移除不存在的文件

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q7-为什么文件不存在错误)

### Q8: 为什么嵌套类型引用错误？

**原因**：生成的 proto 文件中引用了其他 proto 文件，但被引用的文件还没有生成、被引用的文件不在当前目录中

**解决方案**：按照依赖关系顺序生成 proto 文件、将被引用的文件复制到 common 目录中、对于缺失的文件，创建占位符文件

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q8-为什么嵌套类型引用错误)

### Q9: 为什么批次文件注释处理错误？

**原因**：批次文件中包含注释行，脚本会将这些注释行当作文件名处理、注释行以 `#` 开头，被认为是有效的文件名

**解决方案**：修改批次文件，移除注释行，只保留实际的文件名、或者修改脚本，添加对注释行的支持

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q9-为什么批次文件注释处理错误)

### Q10: 为什么批量处理性能问题？

**原因**：处理大量文件时，脚本执行速度较慢、每次处理文件都需要调用 javap 命令，开销较大

**解决方案**：分批处理文件，避免一次处理太多文件、考虑使用并行处理，提高处理速度、缓存 javap 输出，避免重复执行相同的命令

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q10-为什么批量处理性能问题)

### Q11: 为什么泛型类型提取错误？

**原因**：Java 类型中包含泛型类型，如 `java.util.List<com.dnfm.mina.protobuf.ENUM_IDIP_PROHIBIT_TYPE$T>`、脚本在提取泛型类型时，没有正确处理 `>` 符号、生成的类型名包含 `>` 符号，导致 protobuf 语法错误

**解决方案**：修改脚本中的泛型类型提取逻辑，正确处理 `>` 符号、从 Java 类型中提取泛型类型时，应该移除 `>` 符号、添加对特殊字符的处理，确保生成的类型名符合 protobuf 语法规范

**详细解答**：参见 [docs/04-faq.md](docs/04-faq.md#q11-为什么泛型类型提取错误)

## 📚 参考资源

### 相关文档

- [Protobuf 官方文档](https://developers.google.com/protocol-buffers)
- [Java 字节码规范](https://docs.oracle.com/javase/specs/jvms/se8/html/)
- [javap 命令文档](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/javap.html)

### 示例代码

- `scripts/generate_proto_from_class.py` - 完整的生成脚本
- `scripts/generate_proto_from_class_batch.py` - 支持分批调试的生成脚本
- `references/examples/` - 示例 .class 和 .proto 文件

### 相关技能

- `pix-java-to-protobuf` - 从 Java 源文件生成 protobuf
- `pix-skill-creator` - 创建 agent skill

## 🎓 总结

pix-class-to-protobuf 是一个强大的技能，能够从 Java .class 文件自动生成 Protobuf 协议文件。通过使用 javap 解析字节码，提取字段信息和注解，我们可以准确地重建 protobuf 协议定义。

**核心优势**：
1. **自动化**：无需手动编写 protobuf 文件
2. **准确性**：直接从字节码提取信息，避免人为错误
3. **完整性**：自动生成 import 语句，处理复杂类型
4. **可扩展**：支持自定义类型映射和注解解析
5. **可调试性**：支持分批调试，快速定位和解决问题

**适用场景**：
- 逆向工程：从编译后的代码恢复协议定义
- 协议迁移：将现有代码迁移到标准 protobuf 格式
- 文档生成：从 .class 文件生成协议文档
- 代码审查：验证 protobuf 定义是否正确

**文档结构**：
- [实现细节](docs/01-implementation.md) - 完整的生成脚本实现和核心流程
- [关键技术点](docs/02-key-techniques.md) - 常量池解析、注解处理、类型映射等关键技术
- [最佳实践](docs/03-best-practices.md) - 调试技巧、错误处理、性能优化等最佳实践
- [常见问题](docs/04-faq.md) - 常见问题的解决方案和调试技巧
- [脚本使用指南](scripts/README.md) - 脚本的使用方法、参数说明和示例

现在，你已经准备好使用 pix-class-to-protobuf 技能了！

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pixb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
