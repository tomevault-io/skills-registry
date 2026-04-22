---
name: prompt-xray
description: 提示词X光透视 - 从优秀提示词中逆向提取"如何做X"的知识，让黑盒变透明 Use when this capability is needed.
metadata:
  author: ttmouse
---

# Prompt Xray - 提示词逆向工程系统

**设计哲学**: 拆解黑盒，让模糊变清晰
**核心能力**: 回答"如何做X"的问题

---

## 🎯 解决的问题

**问题**: 提示词是黑盒 → 不知道：
- 如何控制颜色？
- 如何控制空间布局？
- 如何添加标志性符号？
- 如何调整排版？
- 如何控制材质？
- 如何控制光影？

**解决**: 从N个优秀提示词中提取规律 → 生成知识库

---

## 📋 使用方式

### 方式1：提取单一维度知识

```
从已分析的提示词中，提取"如何控制颜色"的知识
```

### 方式2：提取所有维度知识

```
从已分析的提示词中，构建完整知识库
```

### 方式3：指定范围

```
分析moss_terrarium系列，提取配色知识
```

---

## 🔄 执行流程

当用户请求提取知识时，你需要：

### Step 1: 读取数据
使用工具读取 `extracted_results/` 下的所有 `*_extracted.json` 文件：
```python
from xray_helper import load_prompts
prompts = load_prompts(pattern="*_extracted.json")
```

### Step 2: 按维度分析
根据用户请求的维度，分析对应模块：

#### 如果用户要"颜色"知识：
- 提取所有 `color_scheme` 模块
- 分析配色公式、关键词、技巧
- 按下面的模板生成Markdown

#### 如果用户要"布局"知识：
- 提取所有 `composition` 模块
- 分析视角、构图规则、定位方法
- 按模板生成Markdown

#### 如果用户要"符号"知识：
- 提取 `constraints` 和 `detail_enhancers` 模块
- 分析文字语法、Logo添加方法
- 按模板生成Markdown

#### 如果用户要"材质"知识：
- 提取 `detail_enhancers` 和相关描述
- 分析表面特征、物理属性、质感关键词
- 按模板生成Markdown

#### 如果用户要"光影"知识：
- 提取 `technical_parameters.lighting` 和 `mood_atmosphere`
- 分析光源类型、布光方案、氛围效果
- 按模板生成Markdown

#### 如果用户要"排版"知识（设计类）：
- 提取 `composition` 和 `visual_style`
- 分析栅格系统、视觉层级、对齐规则
- 按模板生成Markdown

### Step 3: 生成知识卡片
使用工具保存结果：
```python
from xray_helper import save_knowledge_card
save_knowledge_card(dimension="color", content=markdown_content)
```

---

## 📝 输出模板

### 模板1: 如何控制颜色？

```markdown
# 如何控制颜色？

**分析时间**: {当前时间}
**样本数量**: {分析了多少个提示词}
**数据来源**: {哪些提示词}

---

## 🎨 配色公式

### 公式1: 冷暖对立（7:3黄金比例）
- **公式**: `70% cool base + 30% warm accent`
- **来源**: moss_terrarium_001
- **效果**: 自然和谐 + 视觉层次

### 公式2: ...

---

## 📚 颜色关键词库

### 冷色系
- `rich forest greens`
- `deep ocean blues`
- `ice whites`

### 暖色系
- `warm amber wood tones`
- `sunset orange`
- `golden hour light`

### 中性色
- `grayscale`
- `pristine whites`

---

## 🛠️ 配色技巧

1. **温度对比** - 冷色环境 + 暖色焦点 = 视觉层次
2. **7:3比例** - 主色70%，焦点色30%
3. **单色调+焦点色** - 极简风格

---

## 💡 应用案例

### 案例1: moss_terrarium_001
**配色方案**: rich forest greens (70%) + warm amber wood (30%)
**效果**: Natural harmony, clear focal point
**适用场景**: 自然场景、植物摄影

### 案例2: ...

```

### 模板2: 如何控制空间布局？

```markdown
# 如何控制空间布局？

**分析时间**: {当前时间}
**样本数量**: {分析了多少个提示词}

---

## 🎥 视角选择

### `slight top-down angle`
**适用场景**: 微缩场景、产品摄影、生态瓶
**关键词**: `top-down view`, `bird's eye view`, `overhead angle`
**案例**: moss_terrarium_001, moss_terrarium_002
**效果**: 展示全貌，适合平铺布局

### `isometric view`
**适用场景**: 3D产品、游戏场景、建筑
**关键词**: `isometric`, `45-degree angle`
**效果**: 保持平行线，无透视变形

---

## 📐 构图规则

### Golden Ratio（黄金比例）
**关键词**: `golden ratio composition`, `phi grid`
**效果**: 经典和谐比例，视觉平衡
**使用频率**: 3次

### Rule of Thirds（三分法）
**关键词**: `rule of thirds`, `thirds grid`
**效果**: 动态平衡，引导视线

### Centered Symmetry（中心对称）
**关键词**: `centered`, `perfectly symmetrical`
**效果**: 稳定、庄重感

---

## 🎯 定位方法

### 相对位置描述
- `iPhone placed next to notebook`
- `floating in 3D space`
- `bottom-left quadrant`

### 精确坐标（高级）
- `Subject A [X: 20-40, Y: 60-100] (Bottom-Left)`
- `Subject B [X: 60-80, Y: 0-40] (Top-Right)`

---

## 💡 应用案例

### 案例1: moss_terrarium_001
**视角**: slight top-down angle
**构图**: centered in frame, golden ratio
**定位**: terrarium centered, cottage as focal point

```

### 模板3: 如何添加标志性符号？

```markdown
# 如何添加标志性符号？

---

## ✍️ 文字添加语法

### 基础语法
```
text "HELLO" in bold serif
large bold sans-serif text "SALE" in red
neon red cursive script "OPEN"
```

### 位置控制
- `lower left corner`
- `centered at top`
- `floating in 3D space`

---

## 🏷️ Logo/水印添加

### 正确示例
```
small square watermark in lower left corner
simple logo in top-right, 10% opacity
brand symbol integrated into design
```

### ❌ 反面案例（避免）
- `exactly 10x10 pixels` → AI无法保证像素精度
- `Gothic font AND Arial font` → 矛盾指令
- `mandatory mandatory mandatory` → 重复无效

---

## 🎨 符号风格

### 材质效果
- `neon` - 霓虹灯效果
- `embossed` - 浮雕效果
- `metallic sheen` - 金属光泽
- `glowing` - 发光效果

### 字体风格
- `bold serif` - 粗体衬线
- `sans-serif` - 无衬线
- `cursive script` - 草书
- `calligraphy` - 书法体

```

### 模板4: 如何控制材质？

```markdown
# 如何控制材质？

---

## 🔍 表面特征

### 金属材质
- `brushed titanium` - 拉丝钛金属
- `polished chrome` - 抛光镀铬
- `metallic sheen` - 金属光泽

### 有机材质
- `living green textures` - 生机勃勃的绿色质感
- `natural wood grain` - 天然木纹
- `rough bark` - 粗糙树皮

### 玻璃/透明
- `under glass` - 玻璃下
- `translucent` - 半透明
- `crystal clear` - 晶莹剔透

---

## ⚙️ 物理属性

- `glossy` / `matte` - 光泽/哑光
- `reflective` / `absorptive` - 反射/吸收
- `weathered` / `pristine` - 风化/原始
- `soft` / `rigid` - 柔软/坚硬

---

## ✨ 光学效果

- `morning dew droplets` - 晨露水珠
- `soft sunlight reflections on glass` - 玻璃上的柔和阳光反射
- `condensation` - 冷凝水汽
- `refraction` - 折射

```

### 模板5: 如何控制光影？

```markdown
# 如何控制光影？

---

## 💡 光源类型

### 自然光
- `soft diffused daylight` - 柔和漫射日光
- `golden hour light` - 黄金时段光线
- `morning sunlight` - 晨光
- `harsh noon sun` - 正午强光

### 人工光
- `studio lighting` - 影棚灯光
- `neon lights` - 霓虹灯
- `rim light` - 轮廓光
- `softbox overhead` - 头顶柔光箱

---

## 🎬 布光方案

### Rembrandt Lighting（伦勃朗布光）
**效果**: 戏剧性，适合人像
**关键词**: `Rembrandt light`, `triangle highlight`, `dramatic shadows`

### Soft Diffused Light（柔和漫射光）
**效果**: 自然、清新、无硬影
**关键词**: `soft diffused`, `natural ambient`, `no harsh shadows`

### Rim Light（轮廓光）
**效果**: 勾勒边缘，分离主体和背景
**关键词**: `rim lighting`, `backlight`, `edge highlight`

---

## 🌤️ 光线+氛围公式

### 清新宁静
```
soft diffused daylight + morning dew = fresh, peaceful atmosphere
```

### 戏剧张力
```
Rembrandt light + rim light = dramatic portrait with depth
```

### 科技未来
```
neon accent lights + volumetric fog = cyberpunk atmosphere
```

---

## 🌫️ 大气效果

- `volumetric fog` - 体积雾
- `misty` - 雾蒙蒙
- `hazy` - 朦胧
- `clear crisp air` - 清澈空气

```

---

## 🛠️ 工具函数

你需要使用 `xray_helper.py` 中的工具函数：

### 读取提示词
```python
from xray_helper import load_prompts

# 加载所有提示词
all_prompts = load_prompts()

# 加载特定范围
moss_prompts = load_prompts(pattern="moss_terrarium*")
```

### 保存知识卡片
```python
from xray_helper import save_knowledge_card

save_knowledge_card(
    dimension="color",
    content=markdown_content,
    metadata={
        'samples': 10,
        'source': 'moss_terrarium + ethereal_deity'
    }
)
```

---

## 💡 关键原则

### 1. 寻找规律，不是罗列
❌ 错误：只列出所有颜色关键词
✅ 正确：发现配色公式（如：70% cool + 30% warm）

### 2. 提取技巧，不是描述
❌ 错误："这个提示词用了森林绿"
✅ 正确："冷色环境 + 暖色焦点 = 视觉层次"

### 3. 给出案例，可直接复用
❌ 错误：模糊描述"使用对比色"
✅ 正确：具体案例 `rich forest greens + warm amber wood`

### 4. 学习优秀，也学习错误
- 从A级提示词学习最佳实践
- 从D级提示词（如pencil_sketch_idol）学习反面案例

---

## 📊 分析步骤（详细）

### 当用户说："提取如何控制颜色的知识"

**Step 1**: 加载数据
```python
prompts = load_prompts()
```

**Step 2**: 遍历所有提示词，提取 color_scheme 模块
```python
color_data = []
for prompt in prompts:
    if 'color_scheme' in prompt['modules']:
        color_data.append({
            'id': prompt['prompt_id'],
            'scheme': prompt['modules']['color_scheme']
        })
```

**Step 3**: 分析配色公式
- 查找 `primary_palette` 字段
- 检查 `temperature` 描述（如："balanced - cool greens + warm wood"）
- 识别比例关系（70%/30%）
- 提取 `concept`（如："Cold Shell, Warm Heart"）

**Step 4**: 分类关键词
- 遍历所有颜色描述
- 分为冷色/暖色/中性色
- 去重，排序

**Step 5**: 提取技巧
- 温度对比？
- 高对比/低对比？
- 单色调+焦点色？

**Step 6**: 创建案例
- 选择最佳3-5个案例
- 包含：配色方案、效果、适用场景

**Step 7**: 生成Markdown
- 按模板填充内容
- 使用 `save_knowledge_card()` 保存

---

## 🎯 输出位置

所有知识卡片保存到：
```
knowledge_base/
├── how_to_control_color.md
├── how_to_control_layout.md
├── how_to_add_symbols.md
├── how_to_control_materials.md
├── how_to_control_lighting.md
└── how_to_control_typography.md (针对设计类)
```

---

## ✅ 验收标准

生成的知识卡片应该：
1. ✅ 回答"如何做X"的问题
2. ✅ 包含具体的关键词和公式
3. ✅ 有3+个真实案例
4. ✅ 可以直接复用到新提示词中
5. ✅ 既有正面案例，也有反面教训

---

**Skill状态**: ✅ 设计完成
**最后更新**: 2026-01-04
**使用工具**: xray_helper.py

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ttmouse) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
