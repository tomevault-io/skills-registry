---
name: edit-slide
description: 编辑幻灯片内容。当用户说「增加一页」、「删掉第X页」、「修改大纲」、「第X页内容改成XX」、「把背景换成蓝色」、「调整结构」时自动触发。 Use when this capability is needed.
metadata:
  author: intelli-train-ai
---

# 编辑幻灯片

## 触发条件

当用户表达以下意图时自动触发：

**大纲编辑：**
- "增加一页关于XX"
- "删掉第3页"
- "把第2页和第3页合并"
- "调整结构"

**描述编辑：**
- "第2页内容太多了"
- "把第3页内容改成XX"
- "第1页加点数据"

**图片编辑：**
- "第3页背景换成蓝色"
- "把字体放大"
- "让整体更专业"

## 执行流程

### Step 1: 确定当前项目

```bash
cd /Users/zouguangyuan/repos/banana-slides-skill/.claude
[ -f "../.env" ] && source ../.env

# 查看项目状态
python3 skills/project-status/project_status.py --project ${PROJECT_ID}
```

### Step 2: 判断编辑类型

| 用户说 | 编辑类型 | 命令 |
|-------|---------|------|
| "增加/删除/调整页面" | 大纲 | `edit-outline` |
| "第X页内容..." | 描述 | `edit-description` |
| "第X页背景/颜色/样式" | 图片 | `edit-page` |

### Step 3: 执行编辑

```bash
# 编辑大纲
python3 skills/edit-slide/edit_slide.py outline "<修改指令>" --project ${PROJECT_ID}

# 编辑页面图片
python3 skills/edit-slide/edit_slide.py page ${PAGE} "<编辑指令>" --project ${PROJECT_ID}
```

### Step 4: 展示结果并询问

```
✅ 已更新！

${展示修改后的内容}

下一步：
- 需要重新生成图片吗？
- 继续修改其他内容？
- 导出？
```

## 编辑后的联动

- 大纲修改 → 需要重新生成描述和图片
- 描述修改 → 需要重新生成该页图片
- 图片修改 → 直接完成

## 批量编辑

对多页应用相同修改：
```bash
python3 skills/edit-slide/edit_slide.py page --pages "1,3,5" "统一背景为深蓝色" --project ${PROJECT_ID}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/intelli-train-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
