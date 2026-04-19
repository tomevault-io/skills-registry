---
name: sci-paper-assistant
description: Automatically generate SCI research papers from project code implementation and problem-solving processes Use when this capability is needed.
metadata:
  author: asdfgtrewq748
---

# SCI论文助手 - 自动化科研论文生成

将项目内的代码实现逻辑和解决问题过程以SCI科研论文格式创作完整论文。

## 核心功能

### 1. 框架图生成 (Google Generative AI)
- 分析项目代码结构
- 使用Google Generative AI (Gemini 2.0 Flash Experimental)生成架构图
- 支持系统架构图、数据流图、系统设计图
- **需要配置API Key才能使用真实API**
- 如果API不可用，会自动降级到模拟模式

### 2. 内容生成 (AI Agent驱动 - 新!)
- 基于Google Generative AI的智能内容生成
- 自动分析项目代码结构和技术栈
- 智能检测项目类型（深度学习/数据科学/Web应用等）
- 根据项目特点生成个性化论文内容
- 支持各章节独立生成，保持整体连贯性

### 3. 框架图生成
- 分析项目代码结构
- 使用Google Generative AI (Gemini 2.0 Flash Experimental)生成架构图
- 支持系统架构图、数据流图、系统设计图
- **需要配置API Key才能使用真实API**
- 如果API不可用，会自动降级到模拟模式

### 4. 格式化输出
- LaTeX格式（可选）
- Markdown格式
- 完整的论文初稿

## 工作流程

### 阶段1：项目分析
```bash
# 分析项目代码结构
python scripts/run.py framework_generator.py analyze --project-path [PROJECT_PATH]
```

### 阶段2：框架图生成（手动模式 - 推荐）

**新功能: 手动生成高质量框架图**

```bash
# 1. 生成框架图提示词
python scripts/framework_prompt_generator.py \
  --project-path [PROJECT_PATH] \
  --output-dir output/framework_prompts/

# 2. 使用Nano Banana生成框架图
#    - 打开 https://nanobanana.com
#    - 复制提示词文件内容
#    - 生成图片并下载

# 3. 将图片保存到 framework/ 目录
#    文件命名: architecture.png, dataflow.png, interaction.png

# 4. 合并框架图到论文
python scripts/merge_framework.py merge \
  --project-path [PROJECT_PATH] \
  --paper output/native_llm_paper.md
```

**流程说明**:
1. 分析项目结构，生成专业提示词
2. 你去Nano Banana生成高质量框架图
3. 上传图片到framework/目录
4. 自动合并到论文中

**提示词输出位置**: `output/framework_prompts/`
- `framework_prompt_architecture.txt` - 系统架构图提示词
- `framework_prompt_dataflow.txt` - 数据流图提示词
- `framework_prompt_interaction.txt` - 模块交互图提示词

**Nano Banana地址**: https://nanobanana.com

### 阶段3：内容生成 (推荐使用Agent模式)

**新功能: AI Agent智能内容生成**

```bash
# 使用AI Agent生成完整论文（推荐）
python scripts/run.py agent_content_generator.py generate \
  --project-path [PROJECT_PATH] \
  --output output/agent_paper_content.md \
  --format markdown
```

**功能特点:**
- 智能分析项目代码，自动检测技术栈和项目类型
- 基于LLM生成个性化论文内容，无模板化占位符
- 各章节独立生成，自动保持连贯性
- 支持标题、摘要、关键词、引言、方法、实验、结果、讨论、结论

**传统模板生成（备选）**
```bash
# 使用模板生成（不推荐，内容质量较低）
python scripts/run.py content_generator.py generate \
  --project-path [PROJECT_PATH] \
  --framework-diagram output/framework.png \
  --output output/paper_content.md
```

### 阶段4：图表生成
```bash
# 生成实验结果图表
python scripts/run.py plot_generator.py generate \
  --project-path [PROJECT_PATH] \
  --output output/plots/

# 指定图表类型
python scripts/run.py plot_generator.py generate \
  --project-path [PROJECT_PATH] \
  --chart-types scatter,heatmap,bar,histogram \
  --output output/plots/
```

### 阶段5：论文组装
```bash
# 组装完整论文
python scripts/run.py paper_assembler.py assemble \
  --content output/agent_paper_content.md \
  --framework output/framework.png \
  --plots output/plots/ \
  --output output/final_paper.md \
  --format latex
```

### 一键生成（推荐）
```bash
# 从项目到论文的一站式生成（使用AI Agent）
python scripts/run.py paper_assembler.py full_generate \
  --project-path [PROJECT_PATH] \
  --output output/my_paper \
  --format latex
```

### 阶段5：论文组装
```bash
# 组装完整论文
python scripts/run.py paper_assembler.py assemble \
  --content output/paper_content.md \
  --framework output/framework.png \
  --plots output/plots/ \
  --output output/final_paper.md \
  --format latex
```

### 一键生成（推荐）
```bash
# 从项目到论文的一站式生成
python scripts/run.py paper_assembler.py full_generate \
  --project-path [PROJECT_PATH] \
  --output output/ \
  --format latex
```

## SCI论文结构

生成的论文严格遵循标准SCI结构：

### 1. Title (标题)
- 准确反映研究内容
- 简洁明了

### 2. Abstract (摘要)
- 研究背景和目标
- 主要方法
- 关键结果
- 主要结论

### 3. Keywords (关键词)
- 3-5个关键词

### 4. Introduction (引言)
- 研究背景
- 问题陈述
- 研究意义
- 主要贡献
- 论文结构

### 5. Related Work (相关工作)
- 领域现状
- 现有方法的局限
- 本研究的创新点

### 6. Methodology (方法论)
- 系统架构
- 关键算法
- 实现细节
- 技术路线

### 7. Experiments (实验设计)
- 实验设置
- 数据集描述
- 评估指标
- 实验环境

### 8. Results (结果)
- 定量结果
- 定性分析
- 图表展示

### 9. Discussion (讨论)
- 结果分析
- 优势与局限
- 未来工作

### 10. Conclusion (结论)
- 工作总结
- 主要贡献

### 11. References (参考文献)
- 相关研究

## 配置选项

### 环境变量 (.env)
```env
# Google Generative AI API配置
NANOBANANA_API_KEY=your_api_key_here  # 必填：Google API Key
NANOBANANA_MODEL=gemini-2.0-flash-exp  # 可选：模型名称

# 论文配置
LANGUAGE=zh  # 语言: zh/en
PAPER_TYPE=conference  # 论文类型: conference/journal
TARGET_JOURNAL=  # 目标期刊（如需要）

# 图表样式
PLOT_STYLE=seaborn-v0_8-whitegrid
FIGURE_DPI=300
FIGURE_FORMAT=png
```

**获取API Key**：
1. 访问 [Google AI Studio](https://aistudio.google.com/app/apikey)
2. 创建新的API Key
3. 将API Key复制到`.env`文件中

**注意**：
- 如果未配置API Key，系统会自动降级到模拟模式
- 模拟模式会生成简单的占位图，不是真实的架构图
- 建议配置API Key以获得更好的效果

### 内容自定义配置 (config.json)
```json
{
  "paper_settings": {
    "title": "基于[技术]的[应用领域]研究",
    "author": ["作者1", "作者2"],
    "affiliation": "机构名称",
    "keywords": ["关键词1", "关键词2", "关键词3"]
  },
  "generation_options": {
    "include_related_work": true,
    "include_experiments": true,
    "include_bibliography": true,
    "word_count": 6000
  },
  "framework_options": {
    "style": "modern",
    "color_scheme": "professional",
    "detail_level": "high"
  },
  "plot_options": {
    "style": "seaborn-v0_8-whitegrid",
    "palette": "husl",
    "font_scale": 1.2
  }
}
```

## 使用示例

### 示例1：简单项目论文
```bash
# 完整流程
python scripts/run.py paper_assembler.py full_generate \
  --project-path /path/to/my-project \
  --title "基于深度学习的图像分类方法研究" \
  --authors "张三,李四" \
  --output output/my_paper.md
```

### 示例2：复杂多模块项目
```bash
# 自定义流程
# 1. 生成框架图
python scripts/run.py framework_generator.py generate \
  --project-path /path/to/project \
  --views architecture,data_flow,module_interaction \
  --output output/frameworks/

# 2. 生成内容
python scripts/run.py content_generator.py generate \
  --project-path /path/to/project \
  --framework-dir output/frameworks/ \
  --output output/content.md

# 3. 生成图表
python scripts/run.py plot_generator.py generate \
  --project-path /path/to/project \
  --chart-types all \
  --output output/plots/

# 4. 组装论文
python scripts/run.py paper_assembler.py assemble \
  --content output/content.md \
  --framework-dir output/frameworks/ \
  --plots output/plots/ \
  --output output/final_paper.tex
```

### 示例3：指定目标期刊
```bash
python scripts/run.py paper_assembler.py full_generate \
  --project-path /path/to/project \
  --target-journal IEEE-TMI \
  --format latex \
  --output output/ieee_submission/
```

## 输出格式

### Markdown格式
```bash
--format markdown
```
- 可直接预览
- 适合GitHub/博客发布

### LaTeX格式
```bash
--format latex
```
- 期刊投稿标准
- 包含所有宏包和样式
- 可直接编译为PDF

### Word格式
```bash
--format docx
```
- 方便编辑
- 适合团队协作

## 高级功能

### 1. 自定义模板
在 `templates/` 目录下创建自定义模板：
```
templates/
├── ieee_template.tex
├── acm_template.tex
└── custom_template.md
```

使用：
```bash
--template templates/custom_template.tex
```

### 2. 迭代优化
```bash
# 生成初稿后继续优化
python scripts/run.py content_generator.py refine \
  --input output/paper_content.md \
  --focus introduction,methodology \
  --output output/refined_content.md
```

### 3. 批量生成多个版本
```bash
python scripts/run.py paper_assembler.py batch_generate \
  --project-path /path/to/project \
  --versions markdown,latex,docx \
  --output output/versions/
```

## 技术栈

- **架构图生成**: Nano Banana (Gemini 3 Pro)
- **内容生成**: 大语言模型
- **数据分析**: Pandas, NumPy
- **图表绘制**: Matplotlib, Seaborn
- **文档格式**: LaTeX, Markdown, python-docx

## 最佳实践

1. **代码质量**: 确保项目代码有良好的注释和文档
2. **数据准备**: 准备好实验数据（CSV, JSON等格式）
3. **配置优化**: 根据目标期刊调整配置
4. **多轮迭代**: 初稿生成后进行多次优化
5. **人工校对**: AI生成后务必人工校对和修改

## 局限性

- 需要项目代码有良好的可读性和注释
- 高度专业化的领域可能需要额外知识库
- 生成的图表需要人工调整美化
- 参考文献需要人工补充和验证

## 故障排除

| 问题 | 解决方案 |
|------|----------|
| Nano Banana API失败 | 检查API Key和网络连接 |
| 图表生成失败 | 检查数据格式是否正确 |
| LaTeX编译错误 | 检查宏包依赖和语法 |
| 内容质量不佳 | 调整生成参数或提供更详细的项目文档 |

## 数据存储

所有输出存储在 `output/` 目录：
- `frameworks/` - 框架图
- `plots/` - 实验图表
- `content/` - 论文内容
- `final/` - 最终论文

## 贡献指南

如需扩展功能，请在 `scripts/` 目录下添加新脚本：
1. 创建Python脚本
2. 使用 `run.py` 包装器调用
3. 更新 `SKILL.md` 文档

## 更新日志

### v1.0.0
- 初始版本
- 支持完整的论文生成流程
- 集成Nano Banana架构图生成
- 支持多种输出格式

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/asdfgtrewq748) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
