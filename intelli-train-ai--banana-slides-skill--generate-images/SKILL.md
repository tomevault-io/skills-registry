---
name: generate-images
description: 生成幻灯片图片。当用户说「生成图片」、「开始生成」、「继续」（在描述生成后）、「生成幻灯片」时自动触发。 Use when this capability is needed.
metadata:
  author: intelli-train-ai
---

# 生成幻灯片图片

## 触发条件

当用户表达以下意图时自动触发：
- "生成图片"
- "开始生成幻灯片"
- "继续"（在描述生成完成后）
- "生成第X页"

## 前置条件

- 项目状态为 `descriptions_generated` 或更高
- 有当前活动的 PROJECT_ID

## 执行流程

### Step 1: 确认项目状态

```bash
cd /Users/zouguangyuan/repos/banana-slides-skill/.claude
[ -f "../.env" ] && source ../.env

python3 skills/project-status/project_status.py --project ${PROJECT_ID}
```

### Step 2: 确认模板

如果项目已有模板，使用项目的模板。否则可以询问用户：

```bash
# 查看可用模板
python3 skills/ask-template/ask_template.py --list
```

### Step 3: 执行图片生成

```bash
# 默认模板
python3 skills/generate-images/generate_images.py --project ${PROJECT_ID}

# 指定模板
python3 skills/generate-images/generate_images.py --project ${PROJECT_ID} --template business

# 指定页面
python3 skills/generate-images/generate_images.py --project ${PROJECT_ID} --pages "1,3,5"
```

### Step 4: 进度反馈

```
使用模板: 商务
  ✓ 已加载参考图片

🎨 正在生成幻灯片图片...

🎨 第 1/6 页: 封面... ✓
🎨 第 2/6 页: AI起源... ✓
🎨 第 3/6 页: 发展历程... ✓
🎨 第 4/6 页: 深度学习... ✓
🎨 第 5/6 页: 当前应用... ✓
🎨 第 6/6 页: 未来展望... ✓

✅ 图片生成完成！

项目ID: ${PROJECT_ID}
图片目录: slides_output/${PROJECT_ID}/images/

下一步：
- 说「导出」获取 PPTX/PDF
- 说「第X页换个背景」编辑特定页
```

## 模板参考图片

模板可以提供参考图片，生成时会参考其风格：
- 如果模板有参考图片，会显示「✓ 已加载参考图片」
- 参考图片影响配色、布局、整体风格

## 更换模板

重新生成时可以更换模板：
```bash
# 使用新模板重新生成所有图片
python3 skills/generate-images/generate_images.py --project ${PROJECT_ID} --template dark
```

## 断点续做

如果生成中断，重新执行会自动跳过已完成的页面。

## 时间估算

- 每页约 10-30 秒
- 6 页演示约 1-3 分钟

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/intelli-train-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
