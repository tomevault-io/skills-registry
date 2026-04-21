---
name: create-slide
description: 创建幻灯片演示文稿。当用户说「做个PPT」、「生成幻灯片」、「创建演示文稿」、「帮我做一个关于XX的PPT」、「介绍XX的演示」时自动触发。 Use when this capability is needed.
metadata:
  author: intelli-train-ai
---

# 创建幻灯片

这是幻灯片创建的入口 skill，协调以下子 skill 完成整个流程。

## 触发条件

当用户表达以下意图时自动触发：
- "做个PPT介绍XX"
- "帮我生成一个关于XX的幻灯片"
- "创建演示文稿"
- "做一个XX的演示"
- "规划一个PPT"

## 工作流程

```
┌─────────────────────────────────────────────────────────┐
│                    create-slide (入口)                   │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Step 1: ask-template                                   │
│  询问模板偏好（可选，用户可跳过使用默认）                    │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Step 2: create-outline                                 │
│  生成大纲结构，等待用户确认                                │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Step 3: generate-content                               │
│  生成每页详细内容描述                                     │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Step 4: generate-images                                │
│  生成每页幻灯片图片                                       │
└─────────────────────────────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Step 5: export-slide                                   │
│  导出为 PPTX/PDF                                         │
└─────────────────────────────────────────────────────────┘
```

## 子 Skill 说明

| 子 Skill | 功能 | 独立脚本 |
|----------|------|---------|
| `ask-template` | 询问模板偏好 | `skills/ask-template/ask_template.py` |
| `create-outline` | 生成大纲 | `skills/create-outline/create_outline.py` |
| `generate-content` | 生成每页内容 | `skills/generate-content/generate_content.py` |
| `generate-images` | 生成图片 | `skills/generate-images/generate_images.py` |
| `export-slide` | 导出文件 | `skills/export-slide/export_slide.py` |

## 执行流程

### Step 1: 检查环境

```bash
cd /Users/zouguangyuan/repos/banana-slides-skill/.claude
[ -f "../.env" ] && source ../.env
echo "API Provider: ${API_PROVIDER:-google}"
```

### Step 2: 询问模板偏好

参见 `ask-template` skill。如果用户已指定模板或说"继续"，使用默认模板。

### Step 3: 生成大纲

参见 `create-outline` skill。

```bash
python3 skills/create-outline/create_outline.py "<用户主题>" --template ${TEMPLATE}
```

展示大纲并等待确认。

### Step 4: 生成每页内容

参见 `generate-content` skill。

```bash
python3 skills/generate-content/generate_content.py --project ${PROJECT_ID} --parallel
```

### Step 5: 生成图片

参见 `generate-images` skill。

```bash
python3 skills/generate-images/generate_images.py --project ${PROJECT_ID} --template ${TEMPLATE}
```

### Step 6: 导出

参见 `export-slide` skill。

```bash
python3 skills/export-slide/export_slide.py --project ${PROJECT_ID} --format pptx
```

## 进度反馈格式

```
🎨 选择模板: ${TEMPLATE_NAME}

📑 大纲规划完成！「${标题}」- 共 ${N} 页

📝 正在生成内容描述...
✓ 第 1/6 页完成
...

🎨 正在生成图片...
🎨 第 1/6 页: 封面... ✓
...

✅ 幻灯片创建完成！
要导出为 PPTX 吗？
```

## 上下文保持

- 记住 PROJECT_ID
- 记住使用的模板
- 后续编辑保持风格一致

## 模板列表

```bash
python3 skills/ask-template/ask_template.py --list
```

## 自定义模板

用户可以提供自己的参考图片：

1. 将参考图片放入 `.claude/templates/` 目录
2. 在 `templates.json` 中添加模板配置

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/intelli-train-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
