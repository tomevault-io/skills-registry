---
name: remotion-synced-video
description: Create synchronized videos with Remotion, TTS, and real web images - professional-grade videos with real imagery from Google Images, Bing, and Unsplash, perfect audio sync, rich content support and polished visual design. Use when this capability is needed.
metadata:
  author: xdrshjr
---

# Remotion Synced Video with Real Web Images

创建专业级视频，集成**真实网页图片**（Google Images 爬取）、完美音频同步、丰富内容展示和精美视觉设计。使用 Remotion + TTS + 多源图片搜索，生成具有顶级大厂风格的视频内容。

## 🆕 多源图片搜索（v2.0）

本工具现已集成 **Google Images 网页爬取**，无需 Unsplash API Key 即可获取大量高质量真实图片：

```
🥇 Python/Google Images → 🥈 Bing Images → 🥉 Unsplash → 🎨 渐变占位图
```

| 层级 | 来源 | 说明 | 需要配置 |
|------|------|------|----------|
| 1 | **Google Images** | Python + Playwright 爬取高清原图 | 无需 API Key |
| 2 | **Bing Images** | HTTP 解析，快速备选 | 无需 API Key |
| 3 | **Unsplash** | 高质量摄影图 | 需 `UNSPLASH_ACCESS_KEY` |
| 4 | **占位图** | 自动生成渐变背景 | 无需配置 |

### 图片搜索使用

```bash
# 一键搜索所有场景图片
node scripts/search_images.js scenes.json --output ./public/images

# 输出示例：
# ✅ 总计: 4 个场景
#    Google:    3 个  ← 真实网页图片
#    Bing:      1 个  ← 真实网页图片
#    Unsplash:  0 个
#    占位图:    0 个
```

每个场景将自动获取 1-2 张高清图片，优先使用 Google Images 真实图片。

## ✨ Features

- 🖼️ **融入式图片展示** - 图片作为内容的一部分，支持侧边、浮动、卡片等多种布局
- 📝 **丰富的文字内容** - 支持多段落、要点列表、统计数据、引用等多种内容类型
- 💬 **智能字幕系统** - 逐句字幕与音频精准同步，支持自动时间轴分配
- 🔤 **超大字体设计** - 专为视频优化的字体大小，确保在各种屏幕上清晰可读
- 🎬 **丰富的动画效果** - 打字机、逐行淡入、关键词高亮、数字滚动等动画
- ✅ **完美音视频同步** - 每个场景等待音频播放完成
- 🎙️ **多 TTS 支持** - 支持豆包、Volcano 或任何 TTS 服务，默认使用自然男声
- 📐 **动态时长计算** - 根据音频时长自动计算帧数
- 🔧 **FFmpeg 拼接** - 无缝合并所有场景

## 工作流程

```
脚本 → TTS 音频 → 搜索图片 → 测量时长 → 渲染场景 → 拼接视频
```

### 多源图片搜索（新）

本工具现在支持多源图片搜索，自动按优先级获取高质量图片：

```
Google Images → Bing Images → Unsplash → 渐变占位图
```

- **Google Images**: 首选，爬取高清原图
- **Bing Images**: 备选，无需 API Key
- **Unsplash**: 兜底，高质量摄影图（需 API Key）
- **占位图**: 最终兜底，根据场景主题自动生成渐变色

使用方法：
```bash
node scripts/search_images.js scenes.json --output ./public/images
```

搜索完成后会输出摘要报告，告知每个场景使用的图片来源。

## 前置要求

### 1. 系统依赖

```bash
# macOS
brew install ffmpeg

# Python 3.8+ 和 Playwright（用于 Google Images 爬取）
pip3 install playwright
npx playwright install chromium
```

### 2. Node.js 依赖

```bash
npm install @remotion/cli remotion react react-dom
```

## Unsplash API 配置

### 1. 获取 Unsplash Access Key

1. 访问 [Unsplash Developers](https://unsplash.com/developers)
2. 注册并创建新应用
3. 获取 **Access Key**

### 2. 设置环境变量

```bash
# 添加到 ~/.zshrc 或 ~/.bashrc
export UNSPLASH_ACCESS_KEY="your_access_key_here"

# 立即生效
source ~/.zshrc
```

## 项目结构

```
my-video/
├── src/
│   ├── index.tsx              # 注册所有 compositions
│   ├── scenes/
│   │   └── SceneTemplate.tsx  # 专业风格模板
│   └── components/
│       ├── Typography.tsx     # 文字组件
│       ├── ImageCard.tsx      # 图片卡片组件
│       ├── Animations.tsx     # 动画组件
│       ├── GradientOverlay.tsx # 渐变遮罩
│       └── UnsplashImage.tsx  # 图片展示
├── scripts/
│   ├── search_images.js       # 🔥 多源图片搜索主脚本
│   ├── generate_placeholder.js # 渐变占位图生成
│   └── lib/                   # 爬取库
│       ├── crawl_google_images.py  # Python Google 图片爬取
│       └── download_images.py      # 图片下载器
├── public/
│   ├── audio/                 # TTS 音频文件
│   └── images/                # 下载的真实图片
├── scenes.json                # 场景配置
├── image-map.json             # 图片路径映射
└── package.json
```

## 场景配置 (scenes.json)

### 基础场景

```json
{
  "id": "intro",
  "searchQuery": "artificial intelligence technology abstract blue",
  "title": "人工智能革命",
  "subtitle": "探索改变世界的技术力量",
  "caption": "Introduction",
  "variant": "hero"
}
```

### 丰富内容场景

```json
{
  "id": "solution",
  "searchQuery": "neural network ai brain technology",
  "title": "智能解决方案",
  "caption": "Our Solution",
  "variant": "content-rich",
  "layout": {
    "imageLayout": "side-right",
    "imageStyle": "card",
    "imageAnimation": "float",
    "textAlign": "left",
    "accentColor": "#10b981"
  },
  "paragraphs": [
    "我们的 AI 平台能够自动处理海量数据，从中提取有价值的洞察。",
    "通过深度学习算法，系统可以识别复杂的模式和趋势。"
  ],
  "bulletPoints": [
    "实时数据处理，毫秒级响应",
    "准确率高达 99.7%",
    "自动生成可视化报告"
  ],
  "stat": {
    "value": "300",
    "label": "效率提升",
    "suffix": "%"
  },
  "highlightKeywords": ["AI", "深度学习"]
}
```

## 配置选项详解

### Variant 变体

| 变体 | 描述 | 图片布局 |
|------|------|----------|
| `hero` | 全屏背景，居中标题 | `background` |
| `centered` | 居中布局，暗角遮罩 | `background` |
| `split` | 左右分割布局 | `side-right` |
| `minimal` | 简洁底部对齐 | `background` |
| `content-rich` | 丰富内容展示 | `side-right` 或 `side-left` |

### Layout 配置

```json
{
  "layout": {
    "imageLayout": "side-right",    // 图片位置
    "imageStyle": "card",            // 图片样式
    "imageAnimation": "float",       // 图片动画
    "textAlign": "left",             // 文字对齐
    "accentColor": "#3b82f6"         // 强调色
  }
}
```

#### imageLayout 选项

- `background` - 全屏背景（传统方式）
- `side-left` - 图片在左侧，占 42% 宽度
- `side-right` - 图片在右侧，占 42% 宽度
- `floating` - 图片浮动在右侧上方
- `inline` - 图片内嵌在文字内容中

#### imageStyle 选项

- `none` - 无边框无阴影
- `rounded` - 圆角（24px）
- `card` - 卡片样式（圆角 + 阴影 + 边框）
- `polaroid` - 拍立得样式（白色边框）
- `circle` - 圆形

#### imageAnimation 选项

- `none` - 无动画
- `zoom` - 缓慢放大（Ken Burns 效果）
- `fade` - 淡入
- `slide` - 滑入
- `float` - 悬浮浮动

### 内容字段

| 字段 | 类型 | 描述 |
|------|------|------|
| `title` | string | 主标题（必填） |
| `subtitle` | string | 副标题 |
| `caption` | string | 章节标签（大写字母，如 "INTRODUCTION"） |
| `paragraphs` | string[] | 段落文字数组 |
| `bulletPoints` | string[] | 要点列表 |
| `quote` | {text, author?} | 引用文字和作者 |
| `stat` | {value, label, suffix?} | 统计数据展示 |
| `highlightKeywords` | string[] | 需要高亮的关键词 |
| `subtitles` | object | 逐句字幕配置（见下方） |

### 逐句字幕配置

通过 `subtitles` 字段配置与音频同步的字幕：

```json
{
  "id": "intro",
  "title": "AGI 早已实现",
  "subtitles": {
    "sentences": [
      "Nature 重磅评论：",
      "AGI 早已实现，人类却不敢承认。",
      "就在五年前，我们还没有 AGI；",
      "而今天，我们已经拥有它。"
    ],
    "mode": "word-count",
    "style": {
      "position": "bottom",
      "bgOpacity": 0.8,
      "fontSize": 52,
      "maxWidth": "90%",
      "bottomOffset": 100
    }
  }
}
```

**字幕配置选项：**

| 字段 | 类型 | 描述 |
|------|------|------|
| `sentences` | string[] | 字幕句子数组，每句会按时间显示 |
| `mode` | string | 时间分配模式：`word-count`(按字数) / `equal`(平均) |
| `style.position` | string | 位置：`bottom`(底部) / `middle`(中间) |
| `style.bgOpacity` | number | 背景透明度，0-1 |
| `style.fontSize` | number | 字体大小(px) |
| `style.maxWidth` | string | 最大宽度，如 `"90%"` |
| `style.bottomOffset` | number | 距底部距离(px) |

**时间分配模式：**

- **`word-count`** (默认)：按句子字数比例分配音频时长，长句显示更久
- **`equal`**：平均分配音频时长给每句话

> 💡 **提示**：按字数分配通常更自然，因为长句需要更多时间阅读。

## 字体大小规格

专为 1920x1080 视频优化：

| 元素 | 大小 | 用途 |
|------|------|------|
| **主标题 (xl)** | 160px | 重要章节标题 |
| **主标题 (lg)** | 120px | 标准章节标题 |
| **主标题 (md)** | 90px | 次要标题 |
| **副标题** | 64px | 副标题描述 |
| **段落文字** | 44px | 正文内容 |
| **要点列表** | 40px | 列表项 |
| **统计数据值** | 220px | 大数字展示 |
| **统计标签** | 44px | 数字说明 |
| **引用文字** | 56px | 引用内容 |
| **说明标签** | 28px | 章节标识 |

## 动画效果

### 文字动画

```typescript
import { TypewriterText, StaggerContainer, HighlightText } from './components';

// 打字机效果
<TypewriterText text="逐字出现的文字" speed={2} />

// 逐行淡入
<StaggerContainer delay={20} staggerDelay={15}>
  <Paragraph>第一行</Paragraph>
  <Paragraph>第二行</Paragraph>
</StaggerContainer>

// 关键词高亮
<HighlightText 
  text="这是AI驱动的深度学习解决方案"
  keywords={["AI", "深度学习"]}
  highlightColor="#3b82f6"
/>
```

### 数字滚动

```typescript
import { AnimatedCounter } from './components';

<AnimatedCounter 
  value={300} 
  suffix="%"
  duration={60}
/>
```

## TTS 语音配置

### 方案一：豆包 TTS（推荐 ⭐）

创建 `voice.json`：

```json
{
  "provider": "doubao",
  "voice": "zh_male_jieshuoxiaoming_moon_bigtts",
  "speed": 1.0,
  "volume": 1.0,
  "pitch": 0
}
```

**环境变量设置**：
```bash
export VOLCANO_TTS_APPID="your_app_id"
export VOLCANO_TTS_ACCESS_TOKEN="your_access_token"
export VOLCANO_TTS_SECRET_KEY="your_secret_key"
```

### 方案二：系统 TTS（macOS）

如果暂时无法使用豆包，可用系统自带 TTS：
```bash
say -v "Ting-Ting" "你的文案" -o output.aiff
# 转换为 mp3
ffmpeg -i output.aiff output.mp3
```

> ⚠️ 系统 TTS 音质较差，建议优先使用豆包 TTS

### 推荐男声音色

**新闻/科普类视频推荐**：
- `zh_male_jieshuoxiaoming_moon_bigtts` - 解说小明 ⭐ **最推荐，无需权限**
- `zh_male_xinwenxiaozhi_mars_bigtts` - 新闻小志（需开通权限）
- `zh_male_jingdianxiaoming_mars_bigtts` - 经典小明（需开通权限）

**情感类/故事类视频**：
- `zh_male_xiaomo_mars_bigtts` - 小莫，温暖友好
- `zh_male_xudong_conversation_wvae_bigtts` - 开心小东，阳光开朗

## 完整示例场景

```json
[
  {
    "id": "intro",
    "searchQuery": "artificial intelligence technology",
    "title": "AI 革命",
    "subtitle": "正在改变我们的世界",
    "variant": "hero",
    "layout": {
      "imageLayout": "background",
      "imageAnimation": "zoom",
      "accentColor": "#3b82f6"
    }
  },
  {
    "id": "challenge",
    "searchQuery": "complex data visualization",
    "title": "数据挑战",
    "caption": "THE CHALLENGE",
    "variant": "content-rich",
    "layout": {
      "imageLayout": "side-right",
      "imageStyle": "card",
      "accentColor": "#ef4444"
    },
    "paragraphs": [
      "数据爆炸时代已经来临，企业面临着前所未有的挑战。",
      "传统方法已无法满足现代数据处理需求。"
    ],
    "bulletPoints": [
      "数据量年增长 300%",
      "人工处理效率低下",
      "决策成本急剧上升"
    ],
    "highlightKeywords": ["数据爆炸", "挑战"]
  },
  {
    "id": "result",
    "searchQuery": "success business growth",
    "title": "显著成效",
    "caption": "RESULTS",
    "variant": "content-rich",
    "layout": {
      "imageLayout": "side-left",
      "imageStyle": "rounded",
      "accentColor": "#10b981"
    },
    "stat": {
      "value": "500",
      "label": "客户增长率",
      "suffix": "%"
    },
    "quote": {
      "text": "这是我们做过的最明智的投资",
      "author": "某知名企业 CEO"
    }
  }
]
```

## 快速开始

```bash
# 1. 创建项目
mkdir my-video && cd my-video
npm init -y
npm install @remotion/cli remotion react react-dom axios

# 2. 复制 skill 文件
cp -r ~/clawd/skills/remotion-synced-video/src .
cp ~/clawd/skills/remotion-synced-video/scripts .
cp ~/clawd/skills/remotion-synced-video/scenes.json .

# 3. 可选：设置 Unsplash API Key（用于兜底）
export UNSPLASH_ACCESS_KEY="your_key_here"

# 4. 搜索图片（自动多源搜索）
node scripts/search_images.js scenes.json --output ./public/images

# 5. 预览
npx remotion preview src/index.tsx

# 6. 渲染
npx remotion render src/index.tsx Scene-intro out/intro.mp4
```

## 最佳实践

### 🎙️ TTS 音色选择

**强烈推荐使用豆包 TTS**，比系统 TTS 自然很多：

```bash
# 安装豆包 TTS skill
cd ~/clawd/skills/doubao-open-tts
pip install -r requirements.txt

# 设置环境变量
export VOLCANO_TTS_APPID="your_app_id"
export VOLCANO_TTS_ACCESS_TOKEN="your_access_token"
export VOLCANO_TTS_SECRET_KEY="your_secret_key"
```

**推荐的中文男声音色**（新闻/科普类视频）：

| 音色 | Voice Type | 特点 | 备注 |
|------|------------|------|------|
| **解说小明** | `zh_male_jieshuoxiaoming_moon_bigtts` | 新闻播报风格，清晰专业 | ⭐ 推荐，无需额外权限 |
| 新闻小志 | `zh_male_xinwenxiaozhi_mars_bigtts` | 标准新闻播报男声 | 需开通权限 |
| 经典小明 | `zh_male_jingdianxiaoming_mars_bigtts` | 经典纪录片男声 | 需开通权限 |
| 开心小东 | `zh_male_xudong_conversation_wvae_bigtts` | 阳光开朗，自然亲切 | 情感类音色 |

> ⚠️ **注意**：部分音色需要在火山引擎控制台申请开通权限，建议先用解说小明测试。

### 📦 视频文件大小管理

**Telegram 限制 16MB**，需要压缩：

```bash
# 压缩至 16MB 以内（1080p 保持可接受画质）
ffmpeg -i input.mp4 \
  -vcodec h264 -acodec aac \
  -b:v 1.5M -b:a 128k \
  -movflags +faststart \
  output_compressed.mp4

# 如需更小文件，降低视频码率
ffmpeg -i input.mp4 -b:v 800k -b:a 96k output.mp4
```

**码率参考**：
- 高质量：`-b:v 3M` (~25MB/分钟)
- 标准质量：`-b:v 1.5M` (~12MB/分钟) ⭐ 推荐
- 低质量：`-b:v 800k` (~6MB/分钟)

### 🔧 完整生成流程

```bash
# 1. 创建项目
mkdir my-video && cd my-video
npm init -y
npm install @remotion/cli remotion react react-dom axios

# 2. 复制 skill 文件
cp -r ~/clawd/skills/remotion-synced-video/src .
cp ~/clawd/skills/remotion-synced-video/scenes.json .

# 3. 设置环境变量
export UNSPLASH_ACCESS_KEY="your_key_here"
export VOLCANO_TTS_APPID="your_app_id"
export VOLCANO_TTS_ACCESS_TOKEN="your_token"
export VOLCANO_TTS_SECRET_KEY="your_secret"

# 4. 生成 TTS 音频（使用豆包）
python ~/clawd/skills/doubao-open-tts/scripts/tts.py "你的文案" \
  -v zh_male_jieshuoxiaoming_moon_bigtts \
  -o public/audio/scene1.mp3

# 5. 搜索图片
node src/../scripts/search_images.js scenes.json public/images

# 6. 测量音频时长并生成配置文件（关键步骤！）
# 这确保每个场景的时长与音频完全同步
echo "{"> audio-durations.json
for file in public/audio/*.mp3; do
  filename=$(basename "$file" .mp3)
  # 获取音频时长并加2秒缓冲
  duration=$(ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 "$file" | cut -d. -f1)
  duration=$((duration + 2))
  echo "  \"$filename\": $duration," >> audio-durations.json
done
# 删除最后一个逗号并关闭 JSON
truncate -s-2 audio-durations.json
echo "" >> audio-durations.json
echo "}" >> audio-durations.json

# 7. 预览
npx remotion preview src/index.tsx

# 8. 渲染所有场景
npx remotion render src/index.tsx Scene-intro out/intro.mp4
npx remotion render src/index.tsx Scene-history out/history.mp4
# ... 渲染其他场景

# 9. 拼接视频
ffmpeg -f concat -i filelist.txt -c copy output/final.mp4

# 10. 压缩（如需）
ffmpeg -i output/final.mp4 -b:v 1.5M -b:a 128k output/final_compressed.mp4
```

### 💡 内容创作建议

1. **脚本长度**：每个场景控制在 50-80 字，对应 5-10 秒音频
2. **总时长**：短视频建议 30-60 秒，4-6 个场景
3. **图片搜索词**：使用英文关键词，如 `"artificial intelligence technology"`、`"neural network"`
4. **highlightKeywords**：在关键概念上使用高亮，增强记忆点

### 🐛 常见问题排查

**问题：TTS 生成失败**
```
Solution: 检查 VOLCANO_TTS_* 环境变量是否设置正确
```

**问题：音色报权限错误**
```
Solution: 更换为解说小明 (zh_male_jieshuoxiaoming_moon_bigtts) 或其他无需权限的音色
```

**问题：视频文件太大**
```
Solution: 使用 FFmpeg 压缩，降低 -b:v 码率参数
```

**问题：语音还没说完画面就切换了**
```
原因：场景时长固定，没有根据实际音频长度调整
Solution: 
1. 使用 ffprobe 测量每个音频的实际时长
2. 生成 audio-durations.json 配置文件
3. 在 index.tsx 中读取配置并动态计算帧数

示例代码：
// index.tsx
import audioDurations from '../audio-durations.json';

function getAudioDuration(sceneId: string): number {
  return (audioDurations as Record<string, number>)[sceneId] || 5;
}

// 在 Composition 中使用
<Composition
  durationInFrames={getAudioDuration(scene.id) * 30} // 30fps
  ...
/>
```

---

## 🎯 音频时长同步最佳实践

### 为什么需要同步？

当视频由多个场景拼接而成时，每个场景的**画面时长必须与音频时长完全匹配**，否则会出现：
- ❌ 语音还没说完，画面就切到下一个场景
- ❌ 语音已经结束，画面还停留在当前场景

### 解决方案：音频时长配置

**步骤 1：创建音频时长测量脚本**

创建 `scripts/measure-audio.sh`：
```bash
#!/bin/bash
AUDIO_DIR="public/audio"
OUTPUT_FILE="audio-durations.json"

echo "{"
first=true
for file in "$AUDIO_DIR"/*.mp3; do
  filename=$(basename "$file" .mp3)
  duration=$(ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1:nokey=1 "$file" | cut -d. -f1)
  if [ -z "$duration" ]; then duration=5; fi
  duration=$((duration + 2))  # 加2秒缓冲
  
  if [ "$first" = true ]; then first=false; else echo ","; fi
  echo "  \"$filename\": $duration"
done
echo ""
echo "}"
```

**步骤 2：更新 index.tsx 读取时长**

```typescript
import {Composition, registerRoot} from 'remotion';
import {SceneTemplate} from './scenes/SceneTemplate';
import audioDurations from '../audio-durations.json'; // 导入配置

const FPS = 30;

// 从配置文件获取音频时长
function getAudioDuration(sceneId: string): number {
  return (audioDurations as Record<string, number>)[sceneId] || 5;
}

// 计算帧数
function calculateFrames(durationInSeconds: number): number {
  return Math.ceil(durationInSeconds * FPS);
}

export const RemotionRoot: React.FC = () => {
  return (
    <>
      {scenes.map((scene) => (
        <Composition
          key={scene.id}
          id={`Scene-${scene.id}`}
          component={SceneTemplate}
          durationInFrames={calculateFrames(getAudioDuration(scene.id))}
          fps={FPS}
          width={1920}
          height={1080}
          defaultProps={{...}}
        />
      ))}
    </>
  );
};

registerRoot(RemotionRoot);
```

**步骤 3：在渲染流程中执行测量**

```bash
# 生成 TTS 音频后，必须执行时长测量
./scripts/measure-audio.sh > audio-durations.json

# 然后再渲染
./scripts/render.sh
```

### 时长配置示例

生成的 `audio-durations.json` 格式：
```json
{
  "intro": 7,
  "gpt4o": 16,
  "xai": 15,
  "google": 15,
  "moltbot": 11,
  "datacenter": 16,
  "outro": 8
}
```

> 💡 **提示**：数值单位为秒，已包含 +2 秒缓冲时间，确保语音完整播放。

---

## 常见问题

### Q: 字体在不同分辨率下如何适配？
A: 字体大小基于 1920x1080 设计，在 4K 播放时会自动缩放。如需调整，使用 `textScale` 属性。

### Q: 图片布局如何选择？
A: 简短有力的标题用 `background`，需要展示大量内容时用 `side-right` 或 `side-left`。

### Q: 如何自定义动画速度？
A: 大多数组件支持 `delay` 和 `duration` 参数，单位为帧（30fps = 1秒）。

### Q: 统计数据支持小数吗？
A: 支持，使用字符串格式 `value: "99.7"` 或配合 `formatNumber: false` 显示小数。

---

**Pro Tip**: 使用 `highlightKeywords` 可以让核心概念在视频中更加醒目，增强观众记忆！

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xdrshjr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
