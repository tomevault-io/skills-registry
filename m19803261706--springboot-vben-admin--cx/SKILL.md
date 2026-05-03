---
name: cx
description: CX Skill 管理 - 创建/更新/查看项目级 Skills Use when this capability is needed.
metadata:
  author: m19803261706
---

# CX Skill - Skills 管理

创建、更新或查看项目级 Skills。

## 使用方法

```bash
/cx:skill                    # 列出所有项目级 Skills
/cx:skill spring-boot-4      # 查看指定 Skill
/cx:skill create sa-token    # 创建新 Skill
/cx:skill update vben-admin  # 更新现有 Skill
```

**参数**: $ARGUMENTS

## 执行流程

### 模式 1: 列出所有 Skills（无参数）

```bash
echo "📚 项目级 Skills"
echo ""
echo "位置: .claude/skills/"
echo ""

# 列出所有 Skills
for skill_dir in .claude/skills/*/; do
  skill_name=$(basename "$skill_dir")
  skill_file="$skill_dir/SKILL.md"

  if [ -f "$skill_file" ]; then
    # 读取 description
    desc=$(grep -A1 "^description:" "$skill_file" | tail -1)
    echo "  📖 $skill_name"
    echo "     $desc"
    echo ""
  fi
done

echo "---"
echo "创建新 Skill: /cx:skill create {name}"
echo "查看 Skill: /cx:skill {name}"
```

### 模式 2: 查看指定 Skill

```bash
skill_name="$ARGUMENTS"
skill_file=".claude/skills/$skill_name/SKILL.md"

if [ -f "$skill_file" ]; then
  echo "📖 Skill: $skill_name"
  echo ""
  cat "$skill_file"
else
  echo "❌ Skill '$skill_name' 不存在"
  echo ""
  echo "可用的 Skills:"
  ls -1 .claude/skills/
  echo ""
  echo "创建新 Skill: /cx:skill create $skill_name"
fi
```

### 模式 3: 创建新 Skill（create）

```bash
skill_name=$(echo "$ARGUMENTS" | sed 's/create //')

echo "🆕 创建 Skill: $skill_name"
echo ""

# Step 1: 询问技术类型
questions = [{
  "question": "这个 Skill 是什么类型的技术？",
  "header": "技术类型",
  "options": [
    {"label": "后端框架", "description": "如 Spring Boot, FastAPI"},
    {"label": "前端框架", "description": "如 Vue, React, Next.js"},
    {"label": "数据库/存储", "description": "如 MySQL, Redis, MinIO"},
    {"label": "工具库", "description": "如 PDFBox, ZXing"},
    {"label": "认证/安全", "description": "如 Sa-Token, Spring Security"}
  ]
}]

# Step 2: 使用 Context7 查询文档
result = mcp__context7__resolve-library-id(skill_name, "技术文档")
docs = mcp__context7__query-docs(library_id, "core concepts API usage examples")

# Step 3: 生成 SKILL.md
mkdir -p ".claude/skills/$skill_name"

# SKILL.md 模板
cat > ".claude/skills/$skill_name/SKILL.md" << 'EOF'
---
name: {skill_name}
description: {从 Context7 获取的描述}。当进行 {相关任务} 时自动使用。
---

# {Skill Name}

{从 Context7 获取的概述}

## 核心概念

{从 Context7 文档提取}

## 代码规范

### 目录结构

```
{根据技术类型生成}
```

### 命名规范

{根据技术类型生成}

### 代码模板

{从 Context7 文档提取示例}

## 常用 API

{从 Context7 文档提取}

## 最佳实践

{从 Context7 文档提取}

---
> Context7 Library: {library_id}
> 创建时间: {datetime}
EOF

echo "✅ Skill 创建完成"
echo ""
echo "文件: .claude/skills/$skill_name/SKILL.md"
echo ""
echo "查看: /cx:skill $skill_name"
```

### 模式 4: 更新现有 Skill（update）

```bash
skill_name=$(echo "$ARGUMENTS" | sed 's/update //')
skill_file=".claude/skills/$skill_name/SKILL.md"

if [ ! -f "$skill_file" ]; then
  echo "❌ Skill '$skill_name' 不存在"
  exit 1
fi

echo "🔄 更新 Skill: $skill_name"
echo ""

# 询问更新内容
questions = [{
  "question": "要更新哪些内容？",
  "header": "更新内容",
  "multiSelect": true,
  "options": [
    {"label": "重新获取文档", "description": "从 Context7 获取最新文档"},
    {"label": "添加代码模板", "description": "添加新的代码示例"},
    {"label": "更新最佳实践", "description": "添加/修改最佳实践"}
  ]
}]

# 根据选择更新
# ...

echo "✅ Skill 更新完成"
```

## SKILL.md 模板

### 后端框架模板 (Spring Boot 4)

```yaml
---
name: spring-boot-4
description: Spring Boot 4 后端开发规范。当创建 Controller、Service、Entity、Repository 时自动使用。
---

# Spring Boot 4 开发规范

## 目录结构

```
src/main/java/com/{company}/{project}/
├── config/           # 配置类
├── controller/       # 控制器
├── service/          # 服务层
│   └── impl/
├── repository/       # 数据访问层
├── entity/           # 实体类
├── dto/              # 数据传输对象
├── vo/               # 视图对象
└── common/           # 通用类
```

## 代码模板

### Controller
```java
/**
 * {功能}控制器
 */
@RestController
@RequestMapping("/api/{module}")
@Tag(name = "{功能}")
public class {Name}Controller {
    // ...
}
```

...
```

### 前端框架模板 (Vben Admin)

```yaml
---
name: vben-admin
description: Vben Admin 5.x 前端开发规范。当创建页面、组件、API 接口时自动使用。
---

# Vben Admin 开发规范

## 目录结构

```
apps/web-antd/src/
├── api/              # API 接口
├── views/            # 页面
├── components/       # 组件
├── stores/           # 状态管理
└── router/           # 路由
```

...
```

## 输出示例

### 列出 Skills

```
📚 项目级 Skills

位置: .claude/skills/

  📖 spring-boot-4
     Spring Boot 4 后端开发规范

  📖 vben-admin
     Vben Admin 5.x 前端开发规范

  📖 sa-token
     Sa-Token 登录认证规范

---
创建新 Skill: /cx:skill create {name}
查看 Skill: /cx:skill {name}
```

### 创建 Skill

```
🆕 创建 Skill: pdfbox

正在查询 Context7...
✓ 找到: /apache/pdfbox
✓ 获取文档完成

生成 SKILL.md...
✓ 文件: .claude/skills/pdfbox/SKILL.md

✅ Skill 创建完成

预览:
---
name: pdfbox
description: Apache PDFBox PDF 处理库。当进行 PDF 读取、修改、盖章时自动使用。
---
...
```

## 注意事项

1. Skills 存放在项目 `.claude/skills/` 目录
2. 每个 Skill 是一个目录，包含 `SKILL.md`
3. Context7 用于获取技术文档
4. Skill 会被 `/cx:do` 自动加载

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/m19803261706) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
