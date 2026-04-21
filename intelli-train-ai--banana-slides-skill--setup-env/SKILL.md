---
name: setup-env
description: 检查幻灯片生成环境。当用户说「检查环境」、「配置API」、「环境准备」或首次使用幻灯片功能时自动触发。 Use when this capability is needed.
metadata:
  author: intelli-train-ai
---

# 环境准备

## 触发条件

- 用户说"检查环境"、"配置API"
- 首次使用幻灯片功能时自动检查
- API 调用失败时建议运行

## 执行流程

### Step 1: 检查 Python 依赖

```bash
pip3 install google-genai Pillow python-pptx img2pdf openai --quiet 2>/dev/null
echo "✓ Python 依赖已安装"
```

### Step 2: 检查 API 配置

**使用独立脚本（推荐）：**
```bash
cd /Users/zouguangyuan/repos/banana-slides-skill/.claude
python skills/setup-env/setup_env.py --check
```

**或使用 Shell 命令：**
```bash
cd /Users/zouguangyuan/repos/banana-slides-skill/.claude

# 加载 .env
if [ -f "../.env" ]; then
  source ../.env
  echo "✓ 已加载 .env 配置"
fi

# 检查 API Provider
echo "API Provider: ${API_PROVIDER:-google}"

# 检查对应的 API Key
if [ "$API_PROVIDER" = "openrouter" ]; then
  if [ -n "$OPENROUTER_API_KEY" ]; then
    echo "✓ OpenRouter API Key: 已配置"
  else
    echo "✗ OpenRouter API Key: 未配置"
  fi
else
  if [ -n "$GOOGLE_API_KEY" ]; then
    echo "✓ Google API Key: 已配置"
  else
    echo "✗ Google API Key: 未配置"
  fi
fi
```

### Step 3: 输出结果

**环境就绪：**
```
✅ 环境检查完成

- Python 依赖: 已安装
- API Provider: google/openrouter
- API Key: 已配置

可以开始创建幻灯片了！
试试说「帮我做一个关于XX的PPT」
```

**需要配置：**
```
⚠️ 需要配置 API Key

方式1: 使用 Google Gemini API
export GOOGLE_API_KEY="your-google-api-key"

方式2: 使用 OpenRouter
export API_PROVIDER=openrouter
export OPENROUTER_API_KEY="your-openrouter-key"

或在 .env 文件中配置。
```

## 配置文件示例

`.env` 文件：
```bash
# Google Gemini (默认)
GOOGLE_API_KEY="your-key"

# 或 OpenRouter
API_PROVIDER=openrouter
OPENROUTER_API_KEY="your-key"

# 可选：自定义模型
TEXT_MODEL=gemini-2.0-flash
IMAGE_MODEL=gemini-2.0-flash-exp-image-generation
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/intelli-train-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
