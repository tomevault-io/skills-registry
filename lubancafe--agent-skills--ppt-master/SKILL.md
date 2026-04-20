---
name: ppt-master
description: AI 驱动的多格式 SVG 内容生成系统 Use when this capability is needed.
metadata:
  author: lubancafe
---

# PPT Master - AI 驱动的多格式 SVG 内容生成系统

## 触发词

/ppt-master, 制作PPT, 生成演示文稿, SVG幻灯片, 做PPT, 幻灯片设计

## 核心能力

PPT Master 是一个通过多角色协作将源文档转化为高质量 SVG 视觉内容的系统，支持导出为 PowerPoint。

**核心功能**：
1. **八项确认流程** - 策略师主导的专业设计规划
2. **三种设计风格** - 通用灵活 / 一般咨询 / 顶级咨询（MBB 级）
3. **SVG 代码生成** - 符合 PPT 兼容性的矢量图形
4. **CRAP 原则优化** - 对比、重复、对齐、亲密性视觉优化
5. **多格式支持** - PPT 16:9、小红书、朋友圈、Story 等

---

## ⚠️ 事故警告（必须严格遵守）

> **核心教训：永远不要假设，永远要验证。源文档就是唯一的真理，任何自己的"经验"和"习惯"都不如源文档准确。**

### 严重错误清单

| 错误类型 | 错误行为 | 正确做法 |
|----------|----------|----------|
| **自作主张添加章节页** | 看到"模块一"、"模块二"就生成章节分隔页 | **源文档的每一页都是内容页，除非明确标注为章节页** |
| **未仔细阅读源文档** | 想当然开始工作，不核对每页内容 | **完整阅读源文档，创建页面清单后再开始** |
| **忽视用户提醒** | 用户说"不对"后继续按错误理解执行 | **用户提醒 = 立即停止，重新理解需求** |

### 强制执行的工作流程

#### 阶段一：理解需求（必须完成）

1. **完整阅读**源文档
2. **创建页面清单**，列出所有页面的具体内容：
   ```
   P1: 封面
   P2: 目录
   P3: xxx内容页 ← 明确标注是内容页！
   P4: xxx内容页 ← 不是章节页！
   ...
   ```
3. **确认页面类型**：封面、目录、内容页、章节页（如有）

#### 阶段二：与用户确认（推荐）

1. 向用户展示页面清单
2. 询问是否需要额外的章节分隔页
3. **得到明确答复后再开始**

#### 阶段三：批量生成

1. 严格按照清单逐页生成
2. **每生成 5-10 页后自查一次**
3. 确保内容与源文档**完全一致**

#### 阶段四：验证交付

1. 检查文件数量是否正确
2. 逐个文件检查页码和标题
3. 确认无遗漏、无多余

### 防范检查清单

在开始任何 PPT 生成任务前，必须完成：

- [ ] 已完整阅读源文档
- [ ] 已创建页面清单（含每页标题和类型）
- [ ] 已确认页面类型（封面/目录/内容/章节）
- [ ] 已与用户确认特殊需求
- [ ] 已准备好所有素材（图片等）

### 具体教训

1. **PPT 不一定有章节分隔页** - 这是设计规范决定的
2. **模块 ≠ 章节页** - "模块"只是内容组织方式，不代表需要分隔页
3. **用户的反馈是最重要的信号** - 第一次提醒就要高度重视

---

## ⚠️ 执行环境（必须遵守）

> **关键信息：所有 Python 命令必须使用 conda 环境，否则会报错！**

### macOS 执行命令（强制使用）

```bash
# ⚠️ 不要使用 python3，必须使用下面的完整路径！

# 后处理（svg_output → svg_final）
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/finalize_svg.py <项目路径>

# 导出 Office 版本
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/svg_to_pptx.py <项目路径> -s final

# 导出 WPS 版本（必须，不是可选！）
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/svg_to_WPS.py <项目路径>/svg_final -o <项目路径>/<项目名>_WPS.pptx
```

### 工具路径说明

| 工具 | 完整路径 |
|------|----------|
| finalize_svg.py | `/Users/liuyuhua/.claude/skills/ppt-master/tools/finalize_svg.py` |
| svg_to_pptx.py | `/Users/liuyuhua/.claude/skills/ppt-master/tools/svg_to_pptx.py` |
| svg_to_WPS.py | `/Users/liuyuhua/.claude/skills/ppt-master/tools/svg_to_WPS.py` |

---

## 工作流程

```
用户提供源文档（PDF/URL/Markdown）
          │
          ▼
【源内容转换】（如需要）
          │
          ▼
【创建项目文件夹】
          │
          ▼
【询问模板选项】A) 使用已有 / B) 不使用 / C) 生成新模板
          │
          ▼
┌─────────────────────────────────────────┐
│ Strategist（策略规划）← 必须，不可跳过    │
│  • 八项确认                              │
│  • 生成《设计规范与内容大纲》             │
└─────────────────────────────────────────┘
          │
          ├─ 模板选项 = C → Template_Designer
          │
          ├─ 图片包含 AI 生成 → Image_Generator
          │
          ▼
┌──────────────────────────────────────────────────┐
│ Executor (General/Consultant/Consultant_Top)     │
│  • 分阶段批量生成 SVG                            │
│  • 批量生成演讲备注                              │
└──────────────────────────────────────────────────┘
          │
          ▼
【后处理】⚠️ 使用 conda 环境执行 finalize_svg.py
          │
          ▼
【导出 Office】⚠️ 使用 conda 环境执行 svg_to_pptx.py
          │
          ▼
【导出 WPS】⚠️ 必须！使用 conda 环境执行 svg_to_WPS.py
          │
          ▼
Optimizer_CRAP (可选优化)
```

---

## 角色定义

### Strategist（策略师）

**核心使命**：接收源文档，进行内容分析与设计规划，输出《设计规范与内容大纲》。

#### 八项确认（强制执行）

在开始分析前，依次确认以下问题并**提供专业建议**：

| # | 确认项 | 说明 |
|---|--------|------|
| a | **画布格式** | PPT 16:9 (1280×720)、小红书 (1242×1660)、朋友圈 (1080×1080) 等 |
| b | **页数范围** | 基于内容量给出具体建议 |
| c | **目标受众与场景** | 给出初步判断 |
| d | **设计风格** | A) 通用灵活 B) 一般咨询 C) 顶级咨询（MBB 级） |
| e | **配色方案** | 主导色、辅助色、强调色的 HEX 色值 |
| f | **图标方式** | A) Emoji B) AI 生成 C) 内置图标库 D) 自定义 |
| g | **图片使用** | A) 不使用 B) 用户提供 C) AI 生成 D) 占位符预留 |
| h | **排版方案** | 字体预设 (P1-P5) + 正文字号基准 (14-20pt) |

#### 配色规则

- **60-30-10 法则**: 主导色 60%、辅助色 30%、强调色 10%
- 文本对比度 ≥ 4.5:1
- 单页面不超过 4 种主要颜色

#### 字体预设

| 场景 | 预设 | 标题 | 正文 |
|------|------|------|------|
| 现代商务、科技 | P1 | 微软雅黑/Arial | 微软雅黑/Calibri |
| 政务公文、报告 | P2 | 黑体 | 宋体/Times |
| 文化艺术、人文 | P3 | 楷体/Georgia | 微软雅黑 |
| 传统稳重风格 | P4 | 宋体 | 微软雅黑/Arial |
| 英文为主 | P5 | Arial/Impact | Calibri/Georgia |

#### 字号层级

以正文字号为基准 (1×)：

| 用途 | 比例 | 14pt基准 | 18pt基准 |
|------|------|----------|----------|
| 封面标题 | 2.5-3× | 35-42pt | 45-54pt |
| 内容标题 | 1.5-2× | 21-28pt | 27-36pt |
| **正文** | **1×** | **14pt** | **18pt** |
| 注释 | 0.75× | 11pt | 14pt |

---

### Executor（执行师）

**核心使命**：严格遵循《设计规范与内容大纲》，将规划好的内容转化为高质量 SVG 代码。

#### 三种风格

| 风格 | 适用场景 | 特点 |
|------|----------|------|
| **General（通用）** | 一般商业演示、培训 | 灵活布局，视觉丰富 |
| **Consultant（咨询）** | 商务报告、项目汇报 | 简洁专业，数据驱动 |
| **Consultant_Top（顶级咨询）** | 战略报告、董事会演示 | MBB 级，金字塔原则 |

#### 执行准则

- **分阶段批量生成**（推荐）:
  1. **视觉构建阶段**: 连续生成所有 SVG，确保风格一致
  2. **逻辑构建阶段**: 批量生成演讲备注，确保叙事连贯

#### 顶级咨询核心技巧（MBB 级）

1. **数据情境化**：永不孤立展示数据，必须有对比（时间/基准/竞品）
2. **SCQA 框架**：Situation → Complication → Question → Answer
3. **金字塔原则**：结论先行，论据跟进
4. **颜色战略性使用**：聚焦注意力，降低认知负荷
5. **图表 vs 表格选择**：根据数据特点选择最佳形式

#### 文件命名规范

```
中文项目：01_封面.svg, 02_目录.svg, 03_核心优势.svg
英文项目：01_cover.svg, 02_agenda.svg, 03_key_benefits.svg
```

#### 演讲备注格式

```markdown
[过渡] 接下来我们来看...

讲稿正文，2-5 句自然语言。
[停顿] 关键点后留白，[数据] 口语化表达。

要点：①要点一 ②要点二 ③要点三
时长：X 分钟
```

---

### Template_Designer（模板设计师）

**触发条件**：用户选择「C) 生成新模板」

生成 4 个核心模板：
- `01_cover.svg` - 封面
- `02_chapter.svg` - 章节页
- `03_content.svg` - 内容页（页眉页脚框架，内容区灵活）
- `04_ending.svg` - 结束页

可选：`02_toc.svg` - 目录页

---

### Image_Generator（图片生成师）

**触发条件**：图片方式包含「C) AI 生成」

**提示词结构**：
```
[主体描述], [风格指令], [色彩指令], [构图指令], [质量指令], [负面提示]
```

**输出**：
1. 提示词文档：`images/image_prompts.md`
2. 生成的图片：`images/` 目录

---

### Optimizer_CRAP（优化师）

**核心原则**（必须遵循）：

| 原则 | 诊断要点 | 优化方法 |
|------|----------|----------|
| **对齐** | 元素是否随意放置 | 创建强大的视觉连接线 |
| **对比** | 视觉层次是否足够 | 让不同元素截然不同 |
| **重复** | 同类元素风格是否统一 | 有意识重复视觉元素 |
| **亲密性** | 相关内容是否靠近 | 空间上聚合关联内容 |

---

## 技术约束

### SVG 黑名单（必须禁用）

| 分类 | 禁用项 | 说明 |
|------|--------|------|
| 裁剪/遮罩 | `clipPath`, `mask` | PPT 不支持 |
| 特效 | `filter`（所有 `fe*`） | 无法导出 |
| 样式系统 | `<style>`, `class`, `id` 选择器 | 使用内联属性 |
| 结构/嵌套 | `<foreignObject>` | 用 `<tspan>` 换行 |
| 文本/字体 | `textPath`, Web 字体 | 使用系统字体 |
| 动画/交互 | `<animate*>`, JS/事件 | 静态输出 |
| 标记/箭头 | `marker`, `marker-end` | 用 `<polygon>` 替代 |

> **记忆口诀**：PPT 只认基础形状 + 内联样式 + 系统字体

### PPT 兼容性规则

| 禁止写法 | 正确替代 |
|----------|----------|
| `fill="rgba(255,255,255,0.1)"` | `fill="#FFFFFF" fill-opacity="0.1"` |
| `<g opacity="0.2">...</g>` | 每个子元素单独设置透明度 |
| `<image opacity="0.3"/>` | 图片后加遮罩层 |
| `marker-end="url(#arrow)"` | 用 `<line>` + `<polygon>` 组合 |

> **记忆口诀**：PPT 不认 rgba、不认组透明、不认图片透明、不认 marker

### 📌 数据来源规范（强制执行）

**如果 PPT 内容引用了外部资料、数据或链接，必须在页面左下角展示数据来源和链接。**

#### 页脚布局规范

```
┌────────────────────────────────────────────────────────────┐
│                        内容区域                             │
│                                                            │
├────────────────────────────────────────────────────────────┤
│ Source: [来源名称] [链接]    │  CONFIDENTIAL  │   页码    │
│ ←─────左下角─────────────→  │   ←─中间─→     │  ←右下→  │
└────────────────────────────────────────────────────────────┘
```

#### SVG 代码示例

```xml
<!-- 页脚区域 -->
<line x1="40" y1="660" x2="1240" y2="660" stroke="#E2E8F0" stroke-width="1"/>

<!-- 左下角：数据来源（必须展示） -->
<text x="60" y="688" fill="#94A3B8" font-family="Arial, sans-serif" font-size="11">
  Source: 艾瑞咨询《2024年中国XX行业报告》
</text>
<text x="60" y="702" fill="#6366F1" font-family="Arial, sans-serif" font-size="10">
  https://www.iresearch.com.cn/report/xxx
</text>

<!-- 中间：机密标识（如需要） -->
<text x="640" y="688" text-anchor="middle" fill="#D4AF37" font-size="10">
  CONFIDENTIAL
</text>

<!-- 右下角：页码 -->
<text x="1220" y="688" text-anchor="end" fill="#94A3B8" font-size="11">
  5
</text>
```

#### 多来源处理

如果单页引用多个来源，使用编号标注：

```xml
<text x="60" y="688" fill="#94A3B8" font-size="10">
  Sources: [1] 艾瑞咨询 2024 | [2] 国家统计局 | [3] 公司内部数据
</text>
```

#### 来源类型标注

| 来源类型 | 标注格式 |
|----------|----------|
| 研究报告 | `Source: [机构名]《报告名》[年份]` |
| 政府数据 | `Source: [部门名] [数据集名]` |
| 公司内部 | `Source: 公司内部数据` 或 `Source: Internal` |
| 网页链接 | `Source: [网站名] [URL]` |
| 访谈/调研 | `Source: [调研方法] [样本量] [时间]` |

### 箭头绘制方案

```xml
<!-- 正确：使用 line + polygon -->
<line x1="100" y1="200" x2="195" y2="200" stroke="#6366F1" stroke-width="2"/>
<polygon points="195,194 210,200 195,206" fill="#6366F1"/>
```

---

## 画布格式速查

| 格式 | 尺寸 | viewBox | 适用场景 |
|------|------|---------|----------|
| PPT 16:9 | 1280×720 | `0 0 1280 720` | 商业演示、会议汇报 |
| PPT 4:3 | 1024×768 | `0 0 1024 768` | 传统投影、学术演讲 |
| 小红书 | 1242×1660 | `0 0 1242 1660` | 图文知识分享 |
| 朋友圈 | 1080×1080 | `0 0 1080 1080` | 社交媒体海报 |
| Story | 1080×1920 | `0 0 1080 1920` | 抖音/Instagram |
| 公众号头图 | 900×383 | `0 0 900 383` | 微信文章配图 |

---

## 布局参考

### PPT 16:9 (1280×720)

| 布局 | 坐标 |
|------|------|
| 双栏 | 左 x=40,w=580 / 右 x=660,w=580 |
| 三栏 | x=40,450,860 各 w=380 |
| KPI 卡片 | 4 卡片 280×180，间距 30 |

### 小红书 (1242×1660)

单栏堆叠 x=60,w=1122 | 双栏卡片 x=60/641,w=541

### 朋友圈 (1080×1080)

中心聚焦 x=140,y=140,w=800 | 四象限 480×480

---

## 图标使用

### 方式选择

| 方式 | 语法 | 适用场景 |
|------|------|----------|
| Emoji | `<text>🚀 增长</text>` | 轻松活泼 |
| 内置库占位符 | `<use data-icon="rocket" x="100" y="200" width="48" height="48" fill="#0076A8"/>` | 专业场景 |

### 常用图标

**数据图表**：`chart-bar`, `chart-line`, `chart-pie`, `arrow-trend-up`, `database`
**状态反馈**：`circle-checkmark`, `circle-x`, `triangle-exclamation`, `circle-info`
**用户组织**：`user`, `users`, `building`, `group`
**商务金融**：`dollar`, `wallet`, `credit-card`, `shopping-cart`
**工具操作**：`cog`, `pencil`, `magnifying-glass`, `trash`
**创意灵感**：`lightbulb`, `rocket`, `sparkles`, `star`, `target`

### Emoji 速查表（零体积替代方案）

| 分类 | Emoji |
|------|-------|
| 数据图表 | 📊 📈 📉 💹 🗂️ |
| 状态反馈 | ✅ ❌ ⚠️ ℹ️ ❓ |
| 用户组织 | 👤 👥 🏢 👨‍💼 🏛️ |
| 导航箭头 | ⬆️ ⬇️ ⬅️ ➡️ ↗️ |
| 商务金融 | 💰 💳 🛒 📦 💵 |
| 工具操作 | ⚙️ ✏️ 🔍 🗑️ 🔧 |
| 时间日程 | 🕐 📅 ⏱️ ⏰ 📆 |
| 文件文档 | 📄 📁 📋 📝 🗃️ |
| 目标安全 | 🎯 🚩 🛡️ 🔒 🔐 |
| 创意灵感 | 💡 🚀 ✨ ⭐ 🌟 |

---

## 常用命令

### 源文档转换

```bash
# PDF 转 Markdown
python3 tools/pdf_to_md.py <PDF文件>

# 网页转 Markdown
python3 tools/web_to_md.py <URL>
# 或（微信/高防站点）
node tools/web_to_md.cjs <URL>
```

### 项目管理

```bash
# 初始化项目
python3 tools/project_manager.py init <名称> --format ppt169

# SVG 质量检查
python3 tools/svg_quality_checker.py <路径>
```

### 后处理与导出

```bash
# 后处理（修正路径、嵌入图标）
python3 tools/finalize_svg.py <项目路径>

# 导出 PPT for Office（默认嵌入演讲备注）
python3 tools/svg_to_pptx.py <项目路径> -s final

# 导出但不嵌入备注
python3 tools/svg_to_pptx.py <项目路径> -s final --no-notes

# 导出 WPS 兼容版本（原生可编辑形状）
python3 tools/svg_to_WPS.py <项目路径>/svg_final -o <项目路径>/<项目名>_WPS.pptx
```

### macOS conda 环境

```bash
# 后处理
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/finalize_svg.py <项目路径>

# 导出 PPT for Office
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/svg_to_pptx.py <项目路径> -s final

# 导出 WPS 兼容版本
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/svg_to_WPS.py <项目路径>/svg_final -o <项目路径>/<项目名>_WPS.pptx
```

### 预览

```bash
# 预览最终版本
python3 -m http.server -d <路径>/svg_final 8000
```

---

## 项目目录结构

```
project/
├── svg_output/    # 原始版本
├── svg_final/     # 最终版本（后处理完成）
├── images/        # 图片资源
├── templates/     # 项目模板（如有）
├── notes/         # 演讲备注
│   ├── 01_封面.md
│   └── ...
└── *.pptx         # 导出的 PPT 文件
```

---

## 质量检查清单

### 生成前

- [ ] 已完成八项确认
- [ ] 设计规范保存到项目文件夹
- [ ] 图片资源已就绪（如需要）

### 生成后

- [ ] viewBox 与画布尺寸一致
- [ ] 无 `<foreignObject>` / `clipPath` / `mask` / `filter`
- [ ] 使用 `<tspan>` 换行
- [ ] 无 `rgba()` / `<g opacity>` / 图片直接透明度
- [ ] 颜色符合设计规范
- [ ] 元素沿网格线对齐
- [ ] 同类元素风格一致
- [ ] 相关内容空间聚合

---

## ⚠️ 交付检查清单（必须完成）

> **在向用户报告"完成"之前，必须逐项确认以下内容！**

### 必须输出的文件

| 序号 | 文件类型 | 位置 | 状态 |
|------|----------|------|------|
| 1 | SVG 原始文件 | `<项目>/svg_output/*.svg` | ☐ |
| 2 | SVG 最终文件 | `<项目>/svg_final/*.svg` | ☐ |
| 3 | **Office 版 PPTX** | `<项目>/<项目名>.pptx` | ☐ |
| 4 | **WPS 版 PPTX** | `<项目>/<项目名>_WPS.pptx` | ☐ |

### 执行步骤检查

```
□ Step 1: 生成所有 SVG 到 svg_output/
□ Step 2: 运行 finalize_svg.py（使用 conda 环境！）
□ Step 3: 导出 Office 版（使用 conda 环境！）
□ Step 4: 导出 WPS 版（使用 conda 环境！）← 不要忘记！
□ Step 5: 向用户报告完成
```

### 常见遗忘项

| 遗忘项 | 原因 | 防范措施 |
|--------|------|----------|
| **忘记使用 conda 环境** | context compaction 丢失信息 | 每次执行前回顾"执行环境"部分 |
| **忘记 WPS 导出** | 误以为"可选" | WPS 是默认必须，不是可选！ |
| **自己写脚本** | 以为工具不存在 | 永远使用现有 tools/ 目录的工具 |

---

## 角色切换协议

### 显式切换标记（必须输出）

```markdown
---
## 【角色切换：[角色名称]】

📖 **当前角色定义**: [角色职责简述]
📋 **当前任务**: [简述本阶段要完成的任务]
---
```

### 阶段检查点（必须输出）

**Strategist 完成**：
```markdown
## ✅ Strategist 阶段完成
- [x] 已完成八项确认
- [x] 已生成《设计规范与内容大纲》
- [ ] **下一步**: [Template_Designer / Image_Generator / Executor]
```

**Executor 完成**：
```markdown
## ✅ Executor 阶段完成
- [x] 所有 SVG 已生成到 svg_output/
- [x] 演讲备注已生成到 notes/
- [ ] **下一步**: 执行后处理与导出
```

---

## 环境配置

### Python 依赖

```bash
# 核心工具（必装）
pip install python-pptx svglib reportlab

# 完整安装
pip install python-pptx svglib reportlab PyMuPDF Pillow requests beautifulsoup4
```

### macOS conda 环境（用户配置）

如果使用 conda 环境，直接使用完整路径避免 activate 问题：

```bash
# 后处理
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/finalize_svg.py <项目路径>

# 导出 PPT for Office
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/svg_to_pptx.py <项目路径> -s final

# 导出 WPS 兼容版本（原生可编辑形状）
/Users/liuyuhua/miniconda3/envs/ppt-master/bin/python3 tools/svg_to_WPS.py <项目路径>/svg_final -o <项目路径>/<项目名>_WPS.pptx
```

---

## 参考资料

更多详细信息请参考：
- `reference/canvas_formats.md` - 完整画布格式规范
- `reference/svg_constraints.md` - SVG 技术约束详解
- `reference/commands.md` - 命令速查
- `templates/icons_core.md` - 精选图标代码
- `templates/charts_core.md` - 核心图表模板

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lubancafe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
