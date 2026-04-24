---
name: tech-analysis-reporter
description: Agent使用的技术报告生成Skill。通过多轮对话收集用户需求，分析项目，生成专业的技术分析报告。 Use when this capability is needed.
metadata:
  author: xdrshjr
---

# 自媒体软件分析报告生成器（Agent Skill）

## 触发条件

当用户说以下任一语句时，启动本Skill：
- "自媒体软件分析报告"
- "生成技术报告"
- "分析这个项目"
- "帮我写个技术分析"
- "了解一下这个开源项目"

## Agent执行流程

### Phase 1: 项目获取（第1轮对话）

**Agent开场白：**
> 我来帮你生成专业的技术分析报告。首先，请告诉我项目来源：
> 1. **GitHub链接**（公开或私有仓库）
> 2. **本地项目路径**（绝对路径）

**收集信息：**
- 等待用户提供项目来源
- 如果是GitHub私有仓库，询问是否需要Token

**验证可用性：**
```bash
# GitHub项目
git clone --depth 1 <url> /tmp/analysis-repo 2>&1

# 本地项目
ls -la <path>
```

**如果遇到私有仓库权限问题：**
> 这个项目似乎是私有的，我无法直接访问。你可以：
> 1. 提供Personal Access Token
> 2. 或者将项目打包发给我

---

### Phase 2: 项目识别与确认（第2轮对话）

**自动检测项目类型：**

读取项目关键文件，判断项目类型：

| 检测依据 | 项目类型 |
|---------|---------|
| package.json + src/pages或routes | Web应用 |
| package.json + bin字段 | CLI工具 |
| Python/Rust + 算法相关目录 | 算法库 |
| 大量配置+插件系统 | 框架/平台 |
| Android/iOS目录 | 移动端App |
| data/db/query相关 | 数据工具 |
| 无法识别 | 通用项目 |

**Agent告知用户检测结果：**
> 我检测到这是一个 **[项目类型]** 项目。
> 
> **关键特征：**
> - 主要语言：[Python/JavaScript/Rust等]
> - 核心模块：[列出主要目录]
> - 技术栈：[从package.json等提取]
> 
> 这个判断准确吗？如果不准确请告诉我实际类型。

**等待用户确认或修正**

---

### Phase 3: 报告配置（第3轮对话）

**询问报告模板：**
> 请选择报告模板：
> 
> **A. 精简概览**（3章，约3000字）
> - 适合：快速了解项目
> - 章节：概述 + 架构 + 亮点
> 
> **B. 标准分析**（5章，约8000字）⭐推荐
> - 适合：常规技术评审
> - 章节：概述 + 架构 + 机制 + 生态 + 技术栈
> 
> **C. 深度剖析**（7章，约12000字）
> - 适合：深度技术审计
> - 章节：标准5章 + 性能 + 安全 + 未来展望
> 
> **D. 自定义**
> - 你指定章节数量和主题

**询问内容风格：**
> 请选择写作风格：
> 
> 1. **学术风** - 幽默但严谨，有比喻，像技术博客
> 2. **技术风** - 精准专业，术语密集，像架构文档
> 3. **科普风** - 通俗易懂，故事化，适合非技术读者
> 4. **商业风** - 价值导向，有数据，适合投资场景

**询问分析深度：**
> 请选择分析深度：
> 
> - **概述**（30%）- 概念级讲解，无代码
> - **标准**（60%）- 适度深入，少量伪代码
> - **专家**（100%）- 深度剖析，真实代码+详细解释

**询问输出格式：**
> 请选择输出格式（可多选）：
> - [ ] Markdown（可编辑源码）
> - [ ] Word文档（.docx，正式报告）
> - [ ] PDF（适合发布分享）

---

### Phase 4: 个性化配置（第4轮对话）

**询问配图需求：**
> 是否需要配图？每章配图数量：
> - 0张（纯文字，适合已有设计图的项目）
> - 1张（1张概念图/章）
> - 2张（架构图+流程图/章）
> - 3张（全方位视觉展示）

**询问特殊关注点（可选）：**
> 有没有特别希望我重点分析的模块或功能？（直接回车跳过）

**询问字体偏好（仅Word输出时需要）：**
> 如果生成Word文档，字体设置：
> - 标题字体：[黑体/微软雅黑/宋体]
> - 正文字体：[仿宋/宋体/微软雅黑]
> 
> （输出格式将在生成完成后确认）

**确认并总结：**
> 好的，配置完成！让我确认一下：
> 
> | 配置项 | 你的选择 |
> |-------|---------|
> | 项目 | [项目名] |
> | 类型 | [项目类型] |
> | 模板 | [A/B/C/D] |
> | 风格 | [学术/技术/科普/商业] |
> | 深度 | [概述/标准/专家] |
> | 配图 | [0-3张/章] |
> 
> 确认无误后，我将开始生成报告。这个过程大约需要10-15分钟。
> 
> **确认开始？**（回复"开始"或"修改"）

---

### Phase 5: 执行生成

**用户确认后，Agent执行以下步骤：**

#### Step 1: 项目获取
```bash
# GitHub项目
git clone --depth 1 <url> /tmp/analysis-repo

# 本地项目
cp -r <local_path> /tmp/analysis-repo
```

#### Step 2: 项目分析
读取并分析：
- README.md - 项目概述
- package.json / Cargo.toml / pyproject.toml - 技术栈
- src/ 目录结构 - 架构分析
- docs/ - 现有文档

提取关键信息保存到上下文。

#### Step 3: 多Agent协作生成章节

**启动团队任务模式**，PM（你）作为协调者：

```
团队构成：
- PM（你）: 任务分配、进度监控、结果汇总
- Writer-1: 第1章（概述）
- Writer-2: 第2章（架构）
- Writer-3: 第3章（机制）
- Writer-4: 第4章（生态/技术栈，根据模板动态分配）
- QA: 质量验证
```

**共享白板机制**：

创建共享状态文件，所有Agent可读写：

```json
// /tmp/analysis-shared-board.json
{
  "project_info": { /* 项目分析结果 */ },
  "config": {
    "template": "B",
    "style": "学术风",
    "depth": "标准",
    "images_per_chapter": 2
  },
  "chapters": {
    "01-overview": { "status": "pending", "writer": "Writer-1", "content": null },
    "02-architecture": { "status": "pending", "writer": "Writer-2", "content": null },
    "03-mechanism": { "status": "pending", "writer": "Writer-3", "content": null },
    "04-ecosystem": { "status": "pending", "writer": "Writer-4", "content": null },
    "05-techstack": { "status": "pending", "writer": "Writer-4", "content": null }
  },
  "images": {
    "generated": [],
    "assigned": []
  },
  "qa_report": null
}
```

**每个Writer Agent的任务流程**：

1. **读取共享白板** - 获取项目信息和配置
2. **读取分配到的章节Prompt模板**
3. **撰写章节内容**（1000-2000字）
4. **生成配图**（如果配置>0张）
   - 使用 nano-banana-pro 生成2K配图
   - 更新白板：images.generated
5. **保存章节到临时文件**
6. **更新白板**：chapters.[章节].status = "completed"

**PM监控逻辑**：

```python
# 每2分钟检查一次白板进度
while True:
    board = read_shared_board()
    completed = sum(1 for ch in board["chapters"].values() if ch["status"] == "completed")
    total = len(board["chapters"])
    
    if completed == total:
        break
    
    # 向用户汇报进度
    print(f"进度: {completed}/{total} 章节已完成")
    sleep(120)
```

#### Step 4: 生成配图（如果配置 > 0张/章）

**如果用户选择 0 张配图**：
- 跳过此步骤
- 章节中不插入任何图片引用
- 直接继续下一步

**如果用户选择 1-3 张配图**：

每个Writer Agent独立生成配图：

```bash
cd ~/clawd/skills/nano-banana-pro
export $(grep -v '^#' .env | xargs)
uv run scripts/generate_image.py \
  --prompt "[根据章节内容生成的学术风格配图Prompt]" \
  --filename "/tmp/analysis/images/XX-topic.png" \
  --resolution 2K
```

生成后更新共享白板：
```json
"images": {
  "generated": [
    "01-hero.png",
    "02-architecture.png",
    ...
  ]
}
```

#### Step 5: QA质量验证

**启动QA Agent**（并行或依次验证各章节）：

```
QA Agent任务：
1. 读取共享白板，获取所有章节文件路径
2. 逐章验证：
   - 内容准确性（技术描述是否正确）
   - 完整性（是否覆盖要求的深度）
   - 风格符合度（是否符合选择的风格）
   - 流畅度（阅读体验）
   - 配图质量（如果配置了配图）
3. 生成QA报告，更新白板：qa_report
4. 标记各章节：pass / need_fix

如果章节需要修改：
- 返回给对应Writer Agent修改
- 修改后重新验证
- 最多3轮迭代
```

**QA通过后，继续下一步。**

#### Step 6: 合并与转换

**合并Markdown：**
```bash
cat 01-*.md 02-*.md ... > complete.md
```

**格式转换（根据Phase 4用户选择）：**

询问用户确认输出格式：
> 报告内容已生成并通过QA验证。> 
> 请选择最终输出格式（可多选）：
> - [ ] Markdown（可编辑源码）
> - [ ] Word文档（.docx，正式报告）
> - [ ] PDF（适合发布分享）
> 
> 如果选择多个格式，我会打包成zip发送。

等待用户确认后执行转换：

**Markdown**: 直接使用 complete.md

**Word（如果选择）:**
```bash
pandoc complete.md -o report.docx --toc --toc-depth=2
python3 adjust_fonts.py report.docx "[标题字体]" "[正文字体]"
```

**PDF（如果选择）:**
```bash
pandoc complete.md -o report.pdf --pdf-engine=xelatex
```

**多文件打包（如果选择2种以上格式）:**
```bash
zip 技术报告-完整版.zip complete.md report.docx report.pdf images/
```

#### Step 6: 交付

> ✅ 报告生成完成！
> 
> **文件清单：**
> - 📄 完整报告.md（可编辑源码）
> - 📘 技术报告.docx（Word文档，黑体标题+仿宋正文）
> - 📕 技术报告.pdf（PDF格式）
> - 🖼️ 配图文件夹（X张2K分辨率图片）
> 
> 正在发送文件...

发送所有生成的文件给用户。

---

## 特殊情况处理

### 情况1：项目克隆失败
> 克隆项目时遇到问题：[错误信息]
> 
> 可能原因：
> 1. 私有仓库需要Token
> 2. 网络问题
> 3. 仓库不存在
> 
> 建议：[具体解决方案]

### 情况2：项目无法识别类型
> 我无法自动识别这个项目类型。请告诉我：
> - 这是什么类型的项目？（Web应用/CLI工具/算法库/框架/其他）
> - 主要功能是什么？

### 情况3：用户要求修改
> 收到修改意见。请告诉我具体修改点：
> 1. 哪个章节需要修改？
> 2. 修改内容是什么？
> 3. 是否需要调整配图？

然后重新生成对应章节。

### 情况4：生成时间过长
> 报告生成中，已完成 [X/Y] 个章节...
> 
> 预计剩余时间：约 Z 分钟
> 
> 你可以先预览已完成的章节：[文件]

### 情况5：共享白板访问冲突
如果多个Writer Agent同时读写白板：
1. 使用文件锁机制（lockfile）
2. 或者使用Git进行版本控制
3. 或者通过PM中转（所有写操作发给PM，PM更新白板）

**推荐方案**：通过PM中转
```
Writer -> 向PM汇报进度 -> PM更新白板 -> 广播给其他Agent
```

### 情况6：QA验证不通过
如果QA发现章节质量问题：
1. QA在共享白板标记：chapters.[章节].status = "need_fix"
2. QA在qa_report中详细说明问题
3. PM通知对应Writer Agent修改
4. Writer修改后重新提交QA
5. 最多3轮迭代，仍不通过则标记为"warning"继续

---

## 章节Prompt模板

### 学术风Prompt模板

```
你是一个技术文档撰写专家。请基于以下项目信息，撰写[章节名称]。

## 项目信息
{project_analysis_result}

## 写作要求
- 风格：幽默但严谨，像顶级会议论文
- 深度：[概述/标准/专家]
- 字数：[根据深度调整]
- 配图：如果需要，在适当位置插入 ![图片描述](./images/XX-name.png)

## 章节大纲
[根据章节类型提供的大纲]

请直接输出完整的Markdown格式章节内容。
```

### 技术风Prompt模板

```
你是一个技术架构师。请基于以下项目信息，撰写[章节名称]。

## 项目信息
{project_analysis_result}

## 写作要求
- 风格：精准专业，术语准确
- 深度：[概述/标准/专家]
- 包含：架构图描述、关键代码片段、性能数据
- 配图：如果需要，在适当位置插入

请直接输出完整的Markdown格式章节内容。
```

（科普风、商业风类似...）

---

## 依赖检查

执行前检查以下依赖是否可用：

```bash
# 检查pandoc
which pandoc || echo "pandoc未安装"

# 检查python-docx
python3 -c "import docx" 2>&1 || echo "python-docx未安装"

# 检查nano-banana-pro
ls ~/clawd/skills/nano-banana-pro/scripts/generate_image.py

# 检查Git
which git
```

如有缺失，告知用户安装方法。

---

## 输出文件结构

```
/tmp/analysis-output/
├── 01-overview.md
├── 02-architecture.md
├── 03-mechanism.md
├── 04-ecosystem.md
├── 05-techstack.md
├── images/
│   ├── 01-hero.png
│   ├── 02-architecture.png
│   └── ...
├── complete.md              # 合并后的完整Markdown
├── 技术报告.docx            # Word文档（如选择）
└── 技术报告.pdf             # PDF文档（如选择）
```

---

## 共享白板使用示例

### 白板初始化（PM执行）

```python
import json

board = {
    "project_info": {
        "name": "OpenClaw",
        "type": "framework",
        "language": "TypeScript",
        "tech_stack": ["Node.js", "WebSocket", "TypeBox"],
        "description": "个人AI助手平台"
    },
    "config": {
        "template": "B",
        "style": "学术风",
        "depth": "标准",
        "images_per_chapter": 2,
        "title_font": "黑体",
        "body_font": "仿宋"
    },
    "chapters": {
        "01-overview": {
            "status": "pending",
            "writer": "Writer-1",
            "content_path": None,
            "word_count": 0
        },
        "02-architecture": {
            "status": "pending", 
            "writer": "Writer-2",
            "content_path": None,
            "word_count": 0
        },
        "03-mechanism": {
            "status": "pending",
            "writer": "Writer-3", 
            "content_path": None,
            "word_count": 0
        },
        "04-ecosystem": {
            "status": "pending",
            "writer": "Writer-4",
            "content_path": None,
            "word_count": 0
        },
        "05-techstack": {
            "status": "pending",
            "writer": "Writer-4",
            "content_path": None,
            "word_count": 0
        }
    },
    "images": {
        "generated": [],
        "failed": []
    },
    "qa_report": {
        "status": "pending",
        "results": {}
    }
}

with open("/tmp/analysis-shared-board.json", "w") as f:
    json.dump(board, f, indent=2)
```

### Writer Agent读取白板

```python
import json

def read_board():
    with open("/tmp/analysis-shared-board.json", "r") as f:
        return json.load(f)

def update_board(updates):
    """更新白板（通过PM中转）"""
    board = read_board()
    board.update(updates)
    with open("/tmp/analysis-shared-board.json", "w") as f:
        json.dump(board, f, indent=2)

# Writer Agent使用示例
board = read_board()
my_chapter = "01-overview"
config = board["config"]
project_info = board["project_info"]

# 撰写章节...
# 完成后更新白板
update_board({
    "chapters": {
        my_chapter: {
            "status": "completed",
            "content_path": "/tmp/analysis/01-overview.md",
            "word_count": 1850
        }
    }
})
```

### PM监控进度

```python
def check_progress():
    board = read_board()
    total = len(board["chapters"])
    completed = sum(1 for ch in board["chapters"].values() 
                    if ch["status"] == "completed")
    
    return {
        "completed": completed,
        "total": total,
        "percentage": (completed / total) * 100
    }

# 每2分钟检查一次
while True:
    progress = check_progress()
    print(f"进度: {progress['completed']}/{progress['total']} "
          f"({progress['percentage']:.0f}%)")
    
    if progress["completed"] == progress["total"]:
        break
    
    time.sleep(120)
```

---

## 示例对话流程

**用户：** 自媒体软件分析报告

**Agent：** 我来帮你生成专业的技术分析报告。首先，请告诉我项目来源：
1. GitHub链接（公开或私有仓库）
2. 本地项目路径（绝对路径）

**用户：** https://github.com/openclaw/openclaw

**Agent：** [克隆项目并分析...]

我检测到这是一个 **框架/平台** 项目。

关键特征：
- 主要语言：TypeScript
- 核心模块：Gateway、Agent、Channels、Skills
- 技术栈：Node.js、WebSocket、TypeBox

这个判断准确吗？

**用户：** 准确

**Agent：** 请选择报告模板：
A. 精简概览（3章） B. 标准分析（5章） C. 深度剖析（7章） D. 自定义

**用户：** B

**Agent：** 请选择写作风格：
1. 学术风 2. 技术风 3. 科普风 4. 商业风

**用户：** 1

...（继续后续对话）

---

*Skill版本: 2.0 | Agent使用指南 | 更新: 2026-02-03*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xdrshjr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
