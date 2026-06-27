---
name: nbcraft
description: Multi-backend Image + Video Generation CLI (nb command). Image: Gemini (pro=gemini-3-pro-image-preview, flash=gemini-3.1-flash-image-preview), DashScope 阿里云百炼 (wan-pro=wan2.7-image-pro, wan=wan2.7-image), Volcengine Ark 火山方舟 字节 Seedream (seedream=doubao-seedream-4-5-251128, seedream-lite=doubao-seedream-5.0-lite, seedream-legacy=doubao-seedream-4-0-250828), OpenAI (gpt=gpt-image-2). Video: Volcengine Ark Seedance 2.0 (seedance=doubao-seedance-2-0-260128, seedance-fast=doubao-seedance-2-0-fast-260128). Use when user wants to generate images or videos, run batch image generation jobs (Gemini only), reverse-analyze images (UI structure / general description, Gemini only), check generation history/stats, or manage API configuration. Triggers on "nb", "nano banana", "生成图片", "出图", "Gemini 生图", "万相", "wan", "百炼出图", "seedream", "字节出图", "豆包出图", "火山方舟", "ark", "gpt-image", "gpt 出图", "openai 出图", "生成视频", "出视频", "seedance", "视频生成", "batch 生成", "分析图片", "逆向", "UI结构", "describe image". Use when this capability is needed.
metadata:
  author: jieyefriic
---

# nbcraft — Multi-backend Image & Video Generation CLI

CLI tool `nb` wraps four image generation backends. Pick the backend via `--model <alias>`:

| Alias | Backend | Model ID | Notes |
|-------|---------|----------|-------|
| `pro` | Gemini | `gemini-3-pro-image-preview` | Default |
| `flash` | Gemini | `gemini-3.1-flash-image-preview` | Faster, cheaper |
| `wan-pro` | DashScope 阿里云 | `wan2.7-image-pro` | 文生图支持 4K；编辑最高 2K；最多 9 张参考图 |
| `wan` | DashScope 阿里云 | `wan2.7-image` | 最高 2K，速度更快；最多 9 张参考图 |
| `seedream` | Volcengine Ark 火山方舟 | `doubao-seedream-4-5-251128` | 字节 Seedream 4.5，4K，最多 14 张参考图，文字渲染极强 |
| `seedream-lite` | Volcengine Ark 火山方舟 | `doubao-seedream-5.0-lite` | 字节 Seedream 5.0 Lite，最新 5.0 系列，更快更便宜 |
| `seedream-legacy` | Volcengine Ark 火山方舟 | `doubao-seedream-4-0-250828` | 字节 Seedream 4.0（已被 4.5 覆盖，留作回退） |
| `gpt` | OpenAI | `gpt-image-2` | OpenAI 2026-04-21 发布，4K，约 99% 字符级文字准确度 |

**Backend-specific caveats:**
- 参考图数量上限：Gemini 14、DashScope `wan*` 9、Seedream 14、OpenAI `gpt` 16
- `nb batch` 只支持 Gemini（其它后端没有对应 Batch API）
- `nb rev` 只支持 Gemini（其它图像模型不做图生文）
- 4K：Gemini 任一、`wan-pro` 文生图、Seedream 全系、`gpt` 都原生支持；`wan` 4K 会被降档到 2K
- `--negative-prompt`：DashScope 原生支持；Seedream / `gpt` 自动折叠进 prompt；Gemini 忽略
- **Volcengine Ark / Seedream 接入门槛**：仅有 API key 不足以调用，必须先在控制台手动开通服务（见 Prerequisites）

## Prerequisites

- Install: `pip install nbcraft` (or `pip install -e .` from a clone)
- Entry point: `nb`
- API keys:
  - Gemini: `nb config set api_key <key>` 或 `export GEMINI_API_KEY=<key>`
  - DashScope 国内: `nb config set dashscope_api_key <key>` 或 `export DASHSCOPE_API_KEY=<key>`
  - Volcengine Ark 火山方舟（Seedream）：`nb config set ark_api_key <key>` 或 `export ARK_API_KEY=<key>`
  - OpenAI: `nb config set openai_api_key <key>` 或 `export OPENAI_API_KEY=<key>`
- Data stored at `~/.nbcraft/` (config.json, history.json) — legacy `~/.nano_banana/` is auto-detected for backward compatibility

### Volcengine Ark 接入前置（必做）

只配置 `ark_api_key` 不够。在 [火山方舟控制台](https://console.volcengine.com/ark) 完成：

1. **开通服务**：系统管理 → 开通管理 → 视觉大模型 → 找到 Doubao-Seedream（4.0 / 4.5 / 5.0-lite）→ 开通
2. **(可选) 创建推理接入点**：在线推理 → 创建推理接入点 → 选已开通的 Seedream 模型，得到 `ep-xxx` ID。复制后 `nb config set ark_endpoint_id ep-xxx`（设了就用 endpoint，不设就直接走 model name）
3. **API Key**：API Key 管理 → 创建 → `nb config set ark_api_key <key>`

### OpenAI 可选项

如需走代理或自定义 base URL：`nb config set openai_base_url https://your-proxy/v1`（默认 `https://api.openai.com/v1`）。

## Commands

### 1. Generate — `nb gen`

```bash
# Basic (Gemini pro)
nb gen -p "描述"

# Gemini with reference image + aspect ratio + resolution
nb gen -p "描述" -i ref.png --ar 3:4 --res 2K -o output.png

# 万相 wan2.7-image-pro 4K 文生图
nb gen -p "描述" --model wan-pro --ar 16:9 --res 4K

# 万相带参考图做图像编辑（最多 9 张）
nb gen -p "把这只狗换成宇航员" -i dog.png --model wan --ar 1:1 --res 1K

# Seedream 4.5 文字渲染（海报/PPT/电商主图）
nb gen -p "中文海报，主标题『春日上新』，副标题小字" --model seedream --ar 3:4 --res 4K

# Seedream 多参考图融合（最多 14 张）
nb gen -p "保留参考图1的服装，融合参考图2的姿势" -i a.png -i b.png --model seedream

# Seedream 5.0 Lite，更快更便宜
nb gen -p "概念图" --model seedream-lite --ar 16:9 --res 2K

# GPT Image 2 文字渲染 + agentic 推理
nb gen -p "极简 logo 草图，标语 'Stay Curious'" --model gpt --ar 1:1 --res 2K

# GPT Image 2 编辑（自动走 /v1/images/edits）
nb gen -p "把背景换成森林" -i photo.png --model gpt

# DashScope 负向提示词
nb gen -p "描述" --model wan --negative-prompt "低分辨率, 畸形, 模糊"

# Prompt from file
nb gen -p @prompt.txt

# Repeat same prompt N times (1-8), outputs auto-numbered
nb gen -p "描述" --repeat 4 -o out.png
# → out_1.png, out_2.png, out_3.png, out_4.png

# Multiple reference images
nb gen -p "描述" -i a.png -i b.png -i c.png
```

**Options:**
| Flag | Description |
|------|-------------|
| `-p, --prompt` | Required. Text or `@file.txt` |
| `-o, --output` | Output path (default: auto timestamp) |
| `-i, --input` | Reference image, repeatable (上限：Gemini 14, wan 系列 9, Seedream 14, gpt 16) |
| `--ar` | Aspect ratio: `1:1, 2:3, 3:2, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9, 21:9` |
| `--res` | Resolution: `1K, 2K, 4K` (`wan` 4K 会降档到 2K；其它后端 4K 原生) |
| `--repeat` | Generate N copies, 1-8 |
| `--model` | `pro, flash, wan-pro, wan, seedream, seedream-lite, seedream-legacy, gpt` (default from config or pro) |
| `--negative-prompt` | Negative prompt（DashScope 原生；Seedream/gpt 折叠进 prompt；Gemini 忽略） |

**像素尺寸换算**：DashScope 和 Seedream 都把 `--ar` × `--res` 自动映射到像素 size（如 `--ar 16:9 --res 4K` → `4096*2304` / `4096x2304`），按模型能力封顶。OpenAI gpt-image-2 同理映射到 `WxH` 字符串。

### 2. Batch API — `nb batch` (Gemini only)

```bash
# Submit and wait for results
nb batch submit -f tasks.jsonl --poll -o ./output/

# Submit without waiting; pick model
nb batch submit -f tasks.jsonl --name "my-job" --model flash

# Check status
nb batch status <job_name>

# Download completed results
nb batch download <job_name> -o ./output/

# List all jobs
nb batch list
```

**JSONL simple format** (auto-converted to Gemini format):
```jsonl
{"key": "img1", "prompt": "描述", "images": ["ref.png"], "ar": "3:4", "res": "2K"}
{"key": "img2", "prompt": "另一个描述", "ar": "1:1"}
```

Also accepts raw Gemini batch format (auto-detected by presence of `"request"` key).

`--model` on `batch submit` only accepts Gemini aliases (`pro`, `flash`); 其它后端目前没有对应 Batch API。

### 3. Reverse Analysis — `nb rev` (Gemini only)

#### Structure analysis — `nb rev struct`

给 UI 截图/结构图，逆向推测 HTML div 结构、布局方式、组件识别。

```bash
# 分析 UI 截图的 HTML 结构
nb rev struct -i screenshot.png

# 保存结果到文件
nb rev struct -i screenshot.png -o structure.md

# 追加额外指令
nb rev struct -i screenshot.png --extra "重点分析导航栏部分"
```

**Options:**
| Flag | Description |
|------|-------------|
| `-i, --input` | Required. Image to analyze |
| `-o, --output` | Save result to file |
| `--extra` | Additional instructions to append |
| `--model` | `pro, flash`（其它后端会被拒绝） |

#### General description — `nb rev desc`

通用图片分析/描述，支持自定义 prompt。

```bash
# 默认详细描述（中文）
nb rev desc -i photo.png

# 自定义分析 prompt
nb rev desc -i photo.png -p "分析这张图的配色方案"

# 保存结果
nb rev desc -i photo.png -o analysis.txt
```

**Options:**
| Flag | Description |
|------|-------------|
| `-i, --input` | Required. Image to analyze |
| `-p, --prompt` | Custom prompt (default: 详细描述图片内容) |
| `-o, --output` | Save result to file |
| `--model` | `pro, flash`（其它后端会被拒绝） |

### 4. Video — `nb video` (Seedance 2.0 on Volcengine Ark)

**Video models:**
| Alias | Model ID | 定位 |
|---|---|---|
| `seedance` | `doubao-seedance-2-0-260128` | 字节 Seedance 2.0 标准版（高质量），约 2-3 分钟出片 |
| `seedance-fast` | `doubao-seedance-2-0-fast-260128` | Fast 版，便宜约 36%，30-60 秒出片 |

复用 `ark_api_key` —— 但需在控制台**额外开通 Seedance 视频服务**（开通管理 → 视频生成）。

```bash
# 文生视频（默认 seedance、720p、5s、无声）
nb video gen -p "一只柴犬在樱花树下慢镜头转身" --ar 16:9

# 图生视频（首帧/参考图）
nb video gen -p "镜头从产品平移到背景" -i product.png --duration 8

# 高质量 1080p + 同步音频 + 自动下载
nb video gen -p "电影级人物对白镜头" --model seedance --res 1080p --duration 10 --audio -o out.mp4

# Fast 版快速迭代
nb video gen -p "概念草图" --model seedance-fast --duration 4

# 异步：只提交，不等
nb video gen -p "..." --no-poll

# 查询任务状态
nb video status <task_id>

# 单独下载已完成的视频（task_id 必须 status=succeeded）
nb video download <task_id> -o out.mp4
```

**Options (`nb video gen`)：**
| Flag | Description |
|------|-------------|
| `-p, --prompt` | Required. Text or `@file.txt` |
| `-o, --output` | Output MP4 path (default: auto timestamp) |
| `-i, --input` | Reference image (repeatable，本地文件自动 base64) |
| `--ar` | `16:9, 9:16, 4:3, 3:4, 1:1, 21:9, adaptive` |
| `--res` | `480p, 720p, 1080p, 2K` (默认 720p) |
| `--duration` | 4-15 秒（默认 5） |
| `--audio / --no-audio` | 是否生成原生同步音频（默认关） |
| `--seed` | 随机种子 |
| `--model` | `seedance, seedance-fast`（默认 seedance） |
| `--poll / --no-poll` | 默认轮询并自动下载；`--no-poll` 仅提交 |

**重要约束**：生成的视频 URL **24 小时后过期**。`--poll` 模式会立即下载到本地。如果用 `--no-poll`，请尽快执行 `nb video download <task_id>`。

### 5. History — `nb history`

```bash
nb history                    # Recent 20 records
nb history -n 50              # Last 50
nb history -s "keyword"       # Search by prompt
nb history <record_id>        # Full detail (JSON)
```

History records include a `backend` field (`gemini` / `dashscope` / `volcengine_ark` / `openai`) alongside the resolved `model` id.

### 6. Config — `nb config`

```bash
nb config show
nb config set api_key <key>            # Gemini
nb config set dashscope_api_key <key>  # DashScope 国内
nb config set ark_api_key <key>        # Volcengine Ark 火山方舟
nb config set ark_endpoint_id ep-xxx   # 可选，传了就用 endpoint
nb config set openai_api_key <key>     # OpenAI
nb config set openai_base_url <url>    # 可选，自定义 base URL / 代理
nb config set default_ar 3:4
nb config set default_res 2K
nb config set default_model flash       # pro | flash | wan-pro | wan | seedream | seedream-lite | seedream-legacy | gpt
nb config set poll_interval 20          # Batch poll seconds
nb config set output_dir ./out          # Default output directory
```

API key priority:
- Gemini: config `api_key` > `GEMINI_API_KEY` env var
- DashScope: config `dashscope_api_key` > `DASHSCOPE_API_KEY` env var
- Volcengine Ark: config `ark_api_key` > `ARK_API_KEY` env var
- OpenAI: config `openai_api_key` > `OPENAI_API_KEY` env var

### 7. Stats — `nb stats`

```bash
nb stats    # Total calls, success/fail, direct/batch, monthly/daily breakdown
```

## When helping the user

- **默认模型**：用户说"出一张图" / "生成图片"，默认 Gemini `pro`
- **国内场景 / 不能翻墙**：用 DashScope (`wan` / `wan-pro`) 或 Volcengine Ark (`seedream*`)
- **中文文字渲染 / 海报 / PPT / 电商主图**：首选 `seedream`（Seedream 4.5 文字最强），其次 `gpt`
- **多参考图融合（>9 张）**：只能用 Gemini (≤14)、`seedream` (≤14)、`gpt` (≤16)
- **4K 输出**：Gemini 任一、`wan-pro` 文生图、Seedream 全系、`gpt` 都原生；其它会降档
- **追新 / 想试 5.0**：用 `seedream-lite`（5.0 系列目前公开只有 lite 版）
- **agentic 推理 / 复杂结构图**：用 `gpt`（gpt-image-2 是首个 agentic 图像模型，会先 reasoning 再生图）
- **Volcengine 报错 InvalidEndpointOrModel.NotFound**：99% 是没在控制台开通对应模型服务，去开通管理里加一下
- **OpenAI 走代理**：先 `nb config set openai_base_url <url>` 再 gen
- 多 prompt 批量建议走 `nb batch`（仅 Gemini）；其它后端用 `--repeat`
- 相同 prompt 要多张用 `--repeat`
- Always confirm aspect ratio and resolution before generating
- For xhs-generator workflow, default to `--ar 3:4 --res 2K`
- 分析/逆向图片（`nb rev`）目前仅 Gemini，别给其它后端
- `nb rev` results are recorded in history with `mode="rev"`
- **视频生成**：用户说"出视频" / "生成视频"时走 `nb video gen`（Seedance 2.0），默认 `seedance` + 720p + 5s + 无音频
- 视频迭代/草稿用 `seedance-fast`，最终成片用 `seedance`
- 视频结果 24 小时过期，默认 `--poll` 会自动下载；`--no-poll` 时务必提醒用户尽快 `nb video download`

---
> Source: [jieyefriic/nbcraft](https://github.com/jieyefriic/nbcraft) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
