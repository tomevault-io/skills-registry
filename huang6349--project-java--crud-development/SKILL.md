---
name: crud-development
description: | Use when this capability is needed.
metadata:
  author: huang6349
---

# CRUD 开发技能

## 功能概述

自动生成 CRUD 模块的代码文件，基于项目 DDD 分层架构。

## 交互步骤

### 第一步：收集基础信息

**原则**：每次只问一个问题，按顺序逐一收集。

**🚫 严格禁止**：一次性列出所有问题让用户填写。

**✅ 正确做法**：用户回答一个问题后，再问下一个问题。

| 阶段 | 问题                          | 存储变量      |
|----|-----------------------------|-----------|
| Q1 | 请输入模块名（小驼峰格式，默认提示：example）： | `module`  |
| Q2 | 请输入模块中文名称（默认提示：示例）：         | `name`    |
| Q3 | 请输入包路径：                     | `package` |

---

#### 自动处理规则

**Q1 收集 module 时**：

- `{Module}` = 首字母大写
- 例如：`module` → `Example`
- `{MODULE}` = 全大写加下划线
- 例如：`module` → `EXAMPLE`

**Q2 收集 name 时**：

- 如果用户输入的中文名称以"管理"或"信息"结尾，自动去掉相应后缀
- 例如：输入"示例管理" → 存储为"示例"
- 例如：输入"示例信息" → 存储为"示例"

**Q3 收集 package 时**：

- 直接使用用户输入的包路径，无需转换

**变量将用于模板替换**，详见变量替换速查表

### 第二步：生成代码文件

**自动生成所有文件**（无需选择）：

| 文件                            | 包路径                      | 目录            |
|-------------------------------|--------------------------|---------------|
| `{Module}.java`               | `{package}.domain`       | src/main/java |
| `{Module}Queries.java`        | `{package}.request`      | src/main/java |
| `{Module}BO.java`             | `{package}.request`      | src/main/java |
| `{Module}Service.java`        | `{package}.service`      | src/main/java |
| `{Module}ServiceImpl.java`    | `{package}.service.impl` | src/main/java |
| `{Module}Controller.java`     | `{package}.web`          | src/main/java |
| `{Module}ControllerTest.java` | `{package}.web`          | src/test/java |
| `{Module}Util.java`           | `{package}.request`      | src/test/java |

**生成规则**：

- 模板来源：`.claude/templates/code-patterns.md`
- 文件写入到 `project-web` 模块
- 替换模板中所有变量占位符
- 保持代码格式和注释完整

### 第三步：展示生成报告

代码生成完成后，向用户展示以下格式的完成报告：

```text
✅ {Module} 模块生成完成

📁 src/main/java/{package}/
├── domain/
│   └── {Module}.java          实体类
├── request/
│   ├── {Module}Queries.java   查询参数
│   └── {Module}BO.java        业务对象
├── service/
│   ├── {Module}Service.java   服务接口
│   └── impl/
│       └── {Module}ServiceImpl.java 服务实现
└── web/
    └── {Module}Controller.java      控制器

📁 src/test/java/{package}/
├── request/
│   └── {Module}Util.java      测试工具类
└── web/
    └── {Module}ControllerTest.java  控制器测试类

⚙️  变量说明
   {package}  包路径
   {Module}   类名（Example）
   {module}   模块名（example）
   {MODULE}   表常量（EXAMPLE）
   {name}     中文名（示例）
```

## 变量替换速查表

详见：`.claude/templates/code-patterns.md` → 变量替换速查表

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/huang6349) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
