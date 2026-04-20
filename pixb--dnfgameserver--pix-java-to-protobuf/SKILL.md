---
name: pix-java-to-protobuf
description: This skill should be used when user asks to convert Java files to protobuf protocol files or reverse engineer protobuf from Java code. Activates with phrases like java to protobuf, convert java to proto, generate protobuf from java, reverse engineer protobuf from java, java protobuf conversion. Provides comprehensive guidance for parsing Java @Protobuf annotations and generating .proto files. Use when this capability is needed.
metadata:
  author: pixb
---

# pix-java-to-protobuf 技能

## 📂 基础信息

| 属性 | 值 |
| :--- | :--- |
| **名称** | pix-java-to-protobuf |
| **版本** | 1.0.0 |
| **类型** | 转换技能 (Conversion Skill) |
| **核心功能** | 从 Java 文件反推 protobuf 协议 |
| **适用环境** | Trae |

## 🎯 核心目标

pix-java-to-protobuf 是一个转换技能，旨在帮助用户从现有的 Java 文件（使用 JProtobuf 或类似框架）反推生成 protobuf 协议文件（.proto）。它提供结构化的指导，从 Java 文件解析到 proto 文件生成的完整流程。

### 解决的问题

| 痛点 | 解决方案 |
| :--- | :--- |
| 手动编写 proto 文件繁琐 | 自动从 Java 文件生成 proto 文件 |
| 字段类型映射复杂 | 提供完整的 Java 到 proto 类型映射表 |
| 注解解析困难 | 详细的 @Protobuf 注解解析指南 |
| 泛型类型处理困难 | 支持 List、Map 等泛型类型的转换 |
| 文件组织混乱 | 标准化的 proto 文件目录结构 |

## 🛠️ 转换流程

当用户请求从 Java 文件生成 proto 文件时，pix-java-to-protobuf 会引导用户完成以下 5 个阶段：

### 阶段 1: 需求分析

**目标**：理解用户的转换需求，确定 Java 文件的结构和目标。

**流程**：
1. 分析 Java 文件的包结构和类定义
2. 识别使用的注解框架（如 JProtobuf、Protobuf 等）
3. 确定需要转换的文件范围
4. 定义 proto 文件的输出目录和命名规范

### 阶段 2: 文件扫描

**目标**：扫描并收集所有需要转换的 Java 文件。

**流程**：
1. 递归扫描 Java 源代码目录
2. 过滤出包含 @Protobuf 注解的文件
3. 识别消息类、枚举类和服务类
4. 建立文件依赖关系图

### 阶段 3: 解析转换

**目标**：解析 Java 文件并转换为 proto 格式。

**流程**：
1. 解析 @Protobuf 注解，提取字段信息
2. 处理字段类型映射（Java 类型 → proto 类型）
3. 处理泛型类型（List、Map 等）
4. 生成 proto 文件内容

### 阶段 4: 文件生成

**目标**：生成并组织 proto 文件。

**流程**：
1. 按模块或包组织 proto 文件
2. 添加必要的 import 语句
3. 生成 buf 配置文件（可选）
4. 使用 buf 工具生成目标语言代码

### 阶段 5: 验证优化

**目标**：验证生成的 proto 文件并进行优化。

**流程**：
1. 使用 buf lint 验证 proto 文件
2. 使用 buf generate 生成目标语言代码
3. 检查生成的代码是否正确
4. 根据需要进行优化调整

## 📋 Java 到 Proto 类型映射

### 基本类型映射

| Java 类型 | Proto 类型 | 说明 |
| :--- | :--- | :--- |
| `Integer` / `int` | `int32` | 32位整数 |
| `Long` / `long` | `int64` | 64位整数 |
| `Float` / `float` | `float` | 32位浮点数 |
| `Double` / `double` | `double` | 64位浮点数 |
| `Boolean` / `boolean` | `bool` | 布尔值 |
| `String` | `string` | 字符串 |
| `byte[]` | `bytes` | 字节数组 |

### JProtobuf FieldType 映射

| JProtobuf FieldType | Proto 类型 |
| :--- | :--- |
| `FieldType.INT32` | `int32` |
| `FieldType.UINT32` | `uint32` |
| `FieldType.INT64` | `int64` |
| `FieldType.UINT64` | `uint64` |
| `FieldType.FLOAT` | `float` |
| `FieldType.DOUBLE` | `double` |
| `FieldType.BOOL` | `bool` |
| `FieldType.STRING` | `string` |
| `FieldType.BYTES` | `bytes` |
| `FieldType.OBJECT` | 消息类型 |
| `FieldType.ENUM` | 枚举类型 |

### 泛型类型处理

| Java 类型 | Proto 类型 | 说明 |
| :--- | :--- | :--- |
| `List<T>` | `repeated T` | 重复字段 |
| `Map<K, V>` | `map<K, V>` | 映射字段 |
| `Optional<T>` | `optional T` | 可选字段 |

## 🏗️ 注解解析指南

### @Protobuf 注解解析

**基本字段注解**：
```java
@Protobuf(
    fieldType = FieldType.INT32,
    order = 1,
    required = false
)
public Integer error;
```

**解析要点**：
- `fieldType`: 字段类型（可选，可通过 Java 类型推断）
- `order`: 字段序号（必需）
- `required`: 是否必需（proto3 中已废弃，但可用于验证）

**List 类型注解**：
```java
@Protobuf(order = 1)
public List<PT_USER_INFO> userList;
```

**解析要点**：
- 没有 `fieldType` 时，从泛型参数推断类型
- `List<T>` 转换为 `repeated T`

**Map 类型注解**：
```java
@Protobuf(order = 1)
public Map<Integer, PT_CREATURE_LEARN_SKILL_INFOS> slotInfos;
```

**解析要点**：
- `Map<K, V>` 转换为 `map<K, V>`
- 需要分别解析键类型和值类型

### @MessageMeta 注解解析

```java
@MessageMeta(
    module = 11011,
    cmd = 1
)
public class RES_START_DUNGEON extends Message {
```

**解析要点**：
- `module`: 模块号（可用于文件分类）
- `cmd`: 命令号（可用于注释说明）

### @ProtobufClass 注解解析

```java
@ProtobufClass
public class PT_USER_INFO {
```

**解析要点**：
- 标识这是一个 protobuf 消息类
- 需要解析其所有字段

## 🚀 使用示例

### 示例 1: 简单消息类转换

**Java 文件**：
```java
@ProtobufClass
public class PT_USER_INFO {
    @Protobuf(fieldType = FieldType.UINT64, order = 1)
    public Long charguid;

    @Protobuf(fieldType = FieldType.STRING, order = 2)
    public String name;

    @Protobuf(fieldType = FieldType.INT32, order = 3)
    public Integer level;
}
```

**生成的 Proto 文件**：
```protobuf
// Generated from Java file: PT_USER_INFO.java
syntax = "proto3";
package dnfm.protobuf;

message PT_USER_INFO {
  uint64 charguid = 1;
  string name = 2;
  int32 level = 3;
}
```

### 示例 2: 包含泛型的消息类转换

**Java 文件**：
```java
@ProtobufClass
public class RES_START_DUNGEON {
    @Protobuf(fieldType = FieldType.INT32, order = 1)
    public Integer error;

    @Protobuf(order = 2)
    public List<PT_USER_INFO> users;

    @Protobuf(order = 3)
    public Map<Integer, PT_ITEM> items;
}
```

**生成的 Proto 文件**：
```protobuf
// Generated from Java file: RES_START_DUNGEON.java
syntax = "proto3";
package dnfm.protobuf;

import "common/pt_user_info.proto";
import "common/pt_item.proto";

message RES_START_DUNGEON {
  int32 error = 1;
  repeated PT_USER_INFO users = 2;
  map<int32, PT_ITEM> items = 3;
}
```

### 示例 3: 完整的 Python 转换脚本

```python
import re
import os
from pathlib import Path

# Java 类型到 proto 类型的映射
FIELD_TYPE_MAP = {
    'INT32': 'int32',
    'UINT32': 'uint32',
    'INT64': 'int64',
    'UINT64': 'uint64',
    'FLOAT': 'float',
    'DOUBLE': 'double',
    'BOOL': 'bool',
    'STRING': 'string',
    'BYTES': 'bytes',
}

def parse_java_file(java_file_path):
    """解析 Java 文件，提取消息和字段信息"""
    with open(java_file_path, 'r', encoding='utf-8') as f:
        content = f.read()

    # 提取类名
    class_match = re.search(r'public\s+class\s+(\w+)', content)
    if not class_match:
        return None
    class_name = class_match.group(1)

    # 提取字段
    fields = []
    field_pattern = r'@Protobuf\s*\([^)]*order\s*=\s*(\d+)[^)]*\)\s+public\s+(\w+)(?:<([^>]+)>)?\s+(\w+);'

    for match in re.finditer(field_pattern, content, re.DOTALL):
        order = int(match.group(1))
        java_type = match.group(2)
        generic_full = match.group(3)
        field_name = match.group(4)

        # 判断是否是 repeated 类型
        is_repeated = java_type == 'List' and generic_full
        is_map = java_type == 'Map' and generic_full

        # 映射 proto 类型
        proto_type = None

        if is_map:
            parts = [p.strip() for p in generic_full.split(',')]
            if len(parts) == 2:
                key_type = parts[0]
                value_type = parts[1]
                key_proto_type = FIELD_TYPE_MAP.get(key_type.upper(), key_type.lower())
                proto_type = f'map<{key_proto_type}, {value_type}>'
        elif is_repeated:
            if generic_full:
                proto_type = generic_full
        elif generic_full:
            proto_type = generic_full
        elif java_type:
            proto_type = java_type

        fields.append({
            'name': field_name,
            'type': proto_type,
            'order': order,
            'repeated': is_repeated,
            'is_map': is_map
        })

    return {
        'class_name': class_name,
        'fields': fields
    }

def generate_proto_file(java_info, output_path):
    """生成 proto 文件"""
    class_name = java_info['class_name']
    fields = java_info['fields']

    # 收集 import 类型
    imported_types = set()
    for field in fields:
        field_type = field['type']
        if field_type not in ['int32', 'uint32', 'int64', 'uint64', 'bool', 'float', 'double', 'string', 'bytes']:
            if field_type.startswith('repeated '):
                field_type = field_type[9:].strip()
            elif field_type.startswith('map<'):
                parts = field_type[4:-1].split(',')
                if len(parts) == 2:
                    field_type = parts[1].strip()

            if field_type and field_type[0].isupper() and field_type != class_name:
                imported_types.add(field_type)

    # 生成 import 语句
    import_lines = []
    for imported_type in imported_types:
        imported_proto_filename = imported_type.lower() + '.proto'
        if imported_type.startswith('PT_') or imported_type.startswith('ENUM_'):
            imported_subdir = 'common'
        else:
            imported_subdir = 'common'
        import_lines.append(f'import "{imported_subdir}/{imported_proto_filename}";')

    # 生成 proto 内容
    proto_content = f'''// Generated from Java file: {class_name}.java
syntax = "proto3";
package dnfm.protobuf;

'''
    for import_line in import_lines:
        proto_content += import_line + '\n'

    proto_content += f'''
message {class_name} {{
'''
    for field in fields:
        field_type = field['type']
        field_name = field['name']
        order = field['order']
        is_map = field.get('is_map', False)

        if is_map:
            proto_content += f'  {field_type} {field_name} = {order};\n'
        elif field['repeated']:
            proto_content += f'  repeated {field_type} {field_name} = {order};\n'
        else:
            proto_content += f'  {field_type} {field_name} = {order};\n'

    proto_content += '}\n'

    # 写入文件
    os.makedirs(os.path.dirname(output_path), exist_ok=True)
    with open(output_path, 'w', encoding='utf-8') as f:
        f.write(proto_content)

# 使用示例
java_files = list(Path('src/main/java').rglob('*.java'))
for java_file in java_files:
    java_info = parse_java_file(java_file)
    if java_info:
        proto_filename = java_info['class_name'].lower() + '.proto'
        proto_path = os.path.join('proto/generated', 'common', proto_filename)
        generate_proto_file(java_info, proto_path)
        print(f'Generated: {proto_path}')
```

## 📋 最佳实践

### 文件组织

- **按模块分类**：根据 Java 包结构组织 proto 文件
- **统一命名**：使用小写文件名（如 `pt_user_info.proto`）
- **集中管理**：将所有 proto 文件放在 `proto/generated` 目录下

### 注解处理

- **支持多行注解**：使用 `re.DOTALL` 标志匹配跨行注解
- **处理可选字段**：`fieldType` 是可选的，需要从 Java 类型推断
- **处理泛型**：正确解析 `List<T>` 和 `Map<K, V>` 等泛型类型

### 类型映射

- **优先使用 fieldType**：如果有 `fieldType` 注解，优先使用它
- **回退到 Java 类型**：如果没有 `fieldType`，从 Java 类型推断
- **处理嵌套类型**：正确处理嵌套的消息类型和枚举类型

### Import 管理

- **自动收集依赖**：自动识别并添加必要的 import 语句
- **避免循环依赖**：检查并避免循环 import
- **按包组织**：将相同包下的类型放在同一目录

### 验证测试

- **使用 buf lint**：验证 proto 文件的语法正确性
- **使用 buf generate**：生成目标语言代码并验证
- **对比测试**：对比生成的代码和原始 Java 代码的一致性

## 🔧 Buf 配置

### buf.yaml 配置

```yaml
version: v2
deps:
  - buf.build/googleapis/googleapis

lint:
  use:
    - BASIC
    - ENUM_ZERO_VALUE_SUFFIX
    - FIELD_LOWER_SNAKE_CASE
    - MESSAGE_NAMES
    - SERVICE_NAMES
    - PACKAGE_SAME_DIRECTORY
  except:
    - ENUM_VALUE_PREFIX
    - FIELD_NOT_REQUIRED
    - PACKAGE_DIRECTORY_MATCH
    - PACKAGE_NO_IMPORT_CYCLE
    - PACKAGE_VERSION_SUFFIX
    - ENUM_ZERO_VALUE_SUFFIX
  disallow_comment_ignores: true
  ignore:
    - generated

breaking:
  use:
    - FILE
  except:
    - EXTENSION_NO_DELETE
    - FIELD_SAME_DEFAULT
```

### buf.gen.yaml 配置

```yaml
version: v2
managed:
  enabled: true
  disable:
    - file_option: go_package
      module: buf.build/googleapis/googleapis
  override:
    - file_option: go_package_prefix
      value: gen

plugins:
  # Java 代码生成
  - remote: buf.build/protocolbuffers/java
    out: gen/java

  # gRPC Java 代码生成
  - remote: buf.build/grpc/java
    out: gen/java
```

## 🚀 使用指南

### 激活方式

当你询问以下类型的问题时，pix-java-to-protobuf 会自动激活：

- "如何从 Java 文件生成 proto 文件"
- "Java 到 protobuf 的转换"
- "反推 protobuf 协议"
- "解析 @Protobuf 注解"
- "生成 proto 文件"

### 预期输出

pix-java-to-protobuf 会提供：
1. 完整的转换流程指导
2. 详细的类型映射表
3. 注解解析指南
4. 实用的 Python 转换脚本
5. Buf 配置示例
6. 最佳实践和注意事项

## 📚 参考资源

### 相关工具

- **JProtobuf**: 百度开源的 Java Protobuf 框架
- **Protocol Buffers**: Google 的序列化框架
- **Buf**: Protocol Buffers 的现代化工具链

### 文档链接

- [Protocol Buffers 官方文档](https://developers.google.com/protocol-buffers)
- [Buf 官方文档](https://buf.build/docs)
- [JProtobuf GitHub](https://github.com/jhunters/jprotobuf)

## 🎓 总结

pix-java-to-protobuf 是一个强大的转换技能，旨在帮助用户从现有的 Java 文件快速生成 protobuf 协议文件。通过遵循本指南，你可以：

1. **自动化转换**：使用 Python 脚本自动生成 proto 文件
2. **类型安全**：确保类型映射的正确性
3. **结构规范**：生成符合 protobuf 规范的文件
4. **易于维护**：保持 proto 文件和 Java 文件的同步

记住，一个好的转换工具应该：
1. **准确解析**：正确解析 Java 注解和类型
2. **完整映射**：覆盖所有常用的 Java 和 proto 类型
3. **灵活处理**：支持各种复杂的类型和注解组合
4. **易于使用**：提供清晰的文档和示例
5. **可扩展性**：支持自定义类型映射和转换规则

现在，你已经准备好从 Java 文件生成 proto 文件了！

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pixb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
