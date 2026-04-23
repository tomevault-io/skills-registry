---
name: nano-banana-pro
description: 使用 Google 的 Nano Banana Pro（Gemini 3 Pro Image）生成图像。适用于通过 Gemini API 生成 AI 图像、创建专业视觉效果或构建图像生成功能。触发关键词：Nano Banana Pro, Gemini 3 Pro Image, gemini-3-pro-image-preview, Google image generation, Gemini 图像生成。 Use when this capability is needed.
metadata:
  author: 1837620622
---

# Nano Banana Pro（Gemini 3 Pro Image）

使用 Google 的 Gemini 3 Pro Image 模型生成高质量图像。

## Overview

**Nano Banana Pro** 是 **Gemini 3 Pro Image** (`gemini-3-pro-image-preview`) 的营销名称，Google 基于 Gemini 3 Pro 构建的最先进的图像生成和编辑模型。

## 快速开始

### Get API Key
1. Go to [Google AI Studio](https://aistudio.google.com)
2. Click "Get API Key"
3. Store securely as environment variable

### Basic Image Generation (Python)
```python
from google import genai
from google.genai import types

client = genai.Client(api_key="YOUR_GEMINI_API_KEY")

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="A serene Japanese garden with cherry blossoms and a koi pond",
    config=types.GenerateContentConfig(
        response_modalities=['TEXT', 'IMAGE']
    )
)

# Process response
for part in response.candidates[0].content.parts:
    if hasattr(part, 'text'):
        print(f"Description: {part.text}")
    elif hasattr(part, 'inline_data'):
        # Save image
        image_data = part.inline_data.data  # Base64 encoded
        mime_type = part.inline_data.mime_type  # image/png
        
        import base64
        with open("output.png", "wb") as f:
            f.write(base64.b64decode(image_data))
```

### REST API (cURL)
```bash
curl -s -X POST \
  "https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent" \
  -H "x-goog-api-key: $GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "role": "user",
      "parts": [{"text": "Create a vibrant infographic about photosynthesis"}]
    }],
    "generationConfig": {
      "responseModalities": ["TEXT", "IMAGE"]
    }
  }'
```

### TypeScript/JavaScript
```typescript
const GEMINI_API_KEY = process.env.GEMINI_API_KEY;

async function generateImage(prompt: string) {
  const response = await fetch(
    'https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent',
    {
      method: 'POST',
      headers: {
        'x-goog-api-key': GEMINI_API_KEY!,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        contents: [{ 
          role: 'user', 
          parts: [{ text: prompt }] 
        }],
        generationConfig: {
          responseModalities: ['TEXT', 'IMAGE'],
        },
      }),
    }
  );

  const data = await response.json();
  return data;
}
```

## Configuration Options

### Image Configuration
```python
response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="Professional product photo of a coffee mug",
    config=types.GenerateContentConfig(
        response_modalities=['TEXT', 'IMAGE'],
        image_config=types.ImageConfig(
            aspect_ratio="16:9",  # Options: 1:1, 3:2, 16:9, 9:16, 21:9
            image_size="2K"       # Options: 1K, 2K, 4K
        )
    )
)
```

### With Google Search Grounding
```python
response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="Create an infographic showing today's stock market trends",
    config=types.GenerateContentConfig(
        response_modalities=['TEXT', 'IMAGE'],
        tools=[{"google_search": {}}]  # Enable search grounding
    )
)
```

## Multi-Turn Conversations (Iterative Editing)

```python
# Create a chat session
chat = client.chats.create(
    model="gemini-3-pro-image-preview",
    config=types.GenerateContentConfig(
        response_modalities=['TEXT', 'IMAGE'],
        tools=[{"google_search": {}}]
    )
)

# Initial generation
response1 = chat.send_message(
    "Create a vibrant infographic explaining photosynthesis"
)

# Edit the image
response2 = chat.send_message(
    "Update this infographic to be in Spanish. Keep all other elements the same."
)
```

## Key Capabilities

### 1. Superior Text Rendering
```python
response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents="""Create a professional poster with:
    - Title: "Annual Tech Summit 2025"
    - Date: March 15-17, 2025
    - Location: San Francisco Convention Center
    """,
    config=types.GenerateContentConfig(
        response_modalities=['TEXT', 'IMAGE']
    )
)
```

### 2. Character Consistency (Up to 5 Subjects)
```python
import base64

def load_image(path: str) -> str:
    with open(path, "rb") as f:
        return base64.b64encode(f.read()).decode()

character_ref = load_image("character.png")

response = client.models.generate_content(
    model="gemini-3-pro-image-preview",
    contents=[
        {"text": "Generate an image of this person at a tech conference"},
        {"inline_data": {"mime_type": "image/png", "data": character_ref}}
    ],
    config=types.GenerateContentConfig(
        response_modalities=['TEXT', 'IMAGE']
    )
)
```

## Next.js API Route

```typescript
// app/api/generate-image/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function POST(request: NextRequest) {
  const { prompt, aspectRatio = '1:1', imageSize = '2K' } = await request.json();

  try {
    const response = await fetch(
      'https://generativelanguage.googleapis.com/v1beta/models/gemini-3-pro-image-preview:generateContent',
      {
        method: 'POST',
        headers: {
          'x-goog-api-key': process.env.GEMINI_API_KEY!,
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          contents: [{ role: 'user', parts: [{ text: prompt }] }],
          generationConfig: {
            responseModalities: ['TEXT', 'IMAGE'],
            imageConfig: { aspectRatio, imageSize },
          },
        }),
      }
    );

    const data = await response.json();
    const parts = data.candidates?.[0]?.content?.parts || [];
    const imagePart = parts.find((p: any) => p.inline_data);

    return NextResponse.json({
      image: imagePart ? {
        data: imagePart.inline_data.data,
        mimeType: imagePart.inline_data.mime_type,
        url: `data:${imagePart.inline_data.mime_type};base64,${imagePart.inline_data.data}`,
      } : null,
    });
  } catch (error) {
    return NextResponse.json({ error: 'Generation failed' }, { status: 500 });
  }
}
```

## Model Comparison

| Feature | Nano Banana (2.5 Flash) | Nano Banana Pro (3 Pro Image) |
|---------|-------------------------|-------------------------------|
| Model ID | gemini-2.5-flash-image | gemini-3-pro-image-preview |
| Quality | Good | Best |
| Speed | Faster | Slower |
| Cost | Lower | Higher |
| Best For | Previews, high-volume | Production, professional |

## Resources

- **Documentation**: https://ai.google.dev/gemini-api/docs/image-generation
- **Google AI Studio**: https://aistudio.google.com
- **Prompt Guide**: https://ai.google.dev/gemini-api/docs/prompting-intro

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1837620622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
