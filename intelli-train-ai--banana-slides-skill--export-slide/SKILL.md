---
name: export-slide
description: 导出幻灯片。当用户说「导出」、「下载」、「保存」、「导出PPTX」、「导出PDF」、「要PPT文件」时自动触发。 Use when this capability is needed.
metadata:
  author: intelli-train-ai
---

# 导出幻灯片

## 触发条件

当用户表达以下意图时自动触发：
- "导出"
- "下载"
- "保存为PPT"
- "导出PPTX"
- "导出PDF"
- "都要"（同时导出两种格式）

## 前置条件

- 项目有已生成的图片
- 有当前活动的 PROJECT_ID

## 执行流程

### Step 1: 确认项目状态

```bash
cd /Users/zouguangyuan/repos/banana-slides-skill/.claude
[ -f "../.env" ] && source ../.env

python3 skills/project-status/project_status.py --project ${PROJECT_ID}
```

### Step 2: 判断导出格式

| 用户说 | 格式 |
|-------|------|
| "导出" / "下载" | pptx |
| "导出PDF" | pdf |
| "都要" / "两种格式" | both |

### Step 3: 执行导出

```bash
# 导出 PPTX
python3 skills/export-slide/export_slide.py --project ${PROJECT_ID} --format pptx

# 导出 PDF
python3 skills/export-slide/export_slide.py --project ${PROJECT_ID} --format pdf

# 同时导出两种格式
python3 skills/export-slide/export_slide.py --project ${PROJECT_ID} --format both
```

### Step 4: 展示结果

```
✅ 导出成功！

📁 PPTX: slides_output/${PROJECT_ID}/presentation.pptx
📁 PDF: slides_output/${PROJECT_ID}/presentation.pdf

打开文件：
open slides_output/${PROJECT_ID}/presentation.pptx
```

## 格式说明

| 格式 | 说明 |
|-----|------|
| PPTX | PowerPoint 格式，16:9 比例，可编辑 |
| PDF | 适合分享和打印，保持原始质量 |

## 自定义输出路径

```bash
python3 skills/export-slide/export_slide.py --project ${PROJECT_ID} --format pptx --output ~/Desktop/presentation.pptx
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/intelli-train-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
