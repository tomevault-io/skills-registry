---
name: fal-ai
description: 使用 fal.ai 无服务器 AI 生成图像、视频和音频。适用于构建 AI 图像生成、视频生成、图像编辑或实时 AI 功能。触发关键词：fal.ai, fal, AI image generation, Flux, SDXL, real-time AI, serverless AI, AI 图像生成。 Use when this capability is needed.
metadata:
  author: 1837620622
---

# fal.ai - 无服务器 AI

使用 fal.ai 的无服务器 AI 平台生成图像、视频和音频。

## Quick Start

```bash
npm install @fal-ai/serverless-client
```

```typescript
import * as fal from '@fal-ai/serverless-client';

fal.config({
  credentials: process.env.FAL_KEY,
});

// Generate image with Flux
const result = await fal.subscribe('fal-ai/flux/dev', {
  input: {
    prompt: 'A serene Japanese garden with cherry blossoms',
    image_size: 'landscape_16_9',
    num_images: 1,
  },
});

console.log(result.images[0].url);
```

## Authentication

```typescript
// Option 1: Environment variable (recommended)
// Set FAL_KEY in .env
fal.config({ credentials: process.env.FAL_KEY });

// Option 2: Direct config
fal.config({ credentials: 'your-api-key' });

// Option 3: Proxy (for client-side apps)
// Use fal.config({ proxyUrl: '/api/fal/proxy' }) on client
```

## Image Generation Models

### Flux (Fastest, High Quality)
```typescript
// Flux Dev - Best quality
const result = await fal.subscribe('fal-ai/flux/dev', {
  input: {
    prompt: 'Professional headshot of a business executive',
    image_size: 'square_hd',  // 1024x1024
    num_inference_steps: 28,
    guidance_scale: 3.5,
    num_images: 1,
    enable_safety_checker: true,
  },
});

// Flux Schnell - Ultra fast (~0.5s)
const fast = await fal.subscribe('fal-ai/flux/schnell', {
  input: {
    prompt: 'A cute robot',
    image_size: 'square',
    num_inference_steps: 4,  // Schnell needs fewer steps
  },
});

// Flux Pro - Highest quality
const pro = await fal.subscribe('fal-ai/flux-pro', {
  input: {
    prompt: 'Hyperrealistic portrait',
    image_size: 'portrait_4_3',
    safety_tolerance: '2',
  },
});
```

### Image Sizes
```typescript
type ImageSize = 
  | 'square_hd'      // 1024x1024
  | 'square'         // 512x512
  | 'portrait_4_3'   // 768x1024
  | 'portrait_16_9'  // 576x1024
  | 'landscape_4_3'  // 1024x768
  | 'landscape_16_9' // 1024x576
  | { width: number; height: number };  // Custom
```

### SDXL & Stable Diffusion
```typescript
// SDXL
const sdxl = await fal.subscribe('fal-ai/fast-sdxl', {
  input: {
    prompt: 'Fantasy landscape',
    negative_prompt: 'blurry, low quality',
    image_size: 'landscape_16_9',
    num_inference_steps: 25,
    guidance_scale: 7.5,
    scheduler: 'DPM++ 2M Karras',
  },
});

// Stable Diffusion 3
const sd3 = await fal.subscribe('fal-ai/stable-diffusion-v3-medium', {
  input: {
    prompt: 'A mountain lake at sunset',
    negative_prompt: 'ugly, deformed',
    image_size: 'landscape_16_9',
  },
});
```

## Image-to-Image

### Image Editing with Flux
```typescript
// Image to image
const result = await fal.subscribe('fal-ai/flux/dev/image-to-image', {
  input: {
    prompt: 'Transform to watercolor painting style',
    image_url: 'https://example.com/photo.jpg',
    strength: 0.75,  // How much to change (0-1)
    num_inference_steps: 28,
  },
});

// Inpainting (edit specific areas)
const inpaint = await fal.subscribe('fal-ai/flux/dev/inpainting', {
  input: {
    prompt: 'A red sports car',
    image_url: 'https://example.com/street.jpg',
    mask_url: 'https://example.com/mask.png',  // White = edit area
  },
});
```

### ControlNet
```typescript
// Generate with pose/edge control
const controlled = await fal.subscribe('fal-ai/flux-controlnet', {
  input: {
    prompt: 'A professional dancer',
    control_image_url: 'https://example.com/pose.jpg',
    controlnet_conditioning_scale: 0.8,
  },
});
```

## Video Generation

### Kling Video
```typescript
const video = await fal.subscribe('fal-ai/kling-video/v1/standard/text-to-video', {
  input: {
    prompt: 'A golden retriever running through a field of flowers',
    duration: '5',  // seconds
    aspect_ratio: '16:9',
  },
});

console.log(video.video.url);
```

### Image to Video
```typescript
const i2v = await fal.subscribe('fal-ai/kling-video/v1/standard/image-to-video', {
  input: {
    prompt: 'The person starts walking forward',
    image_url: 'https://example.com/person.jpg',
    duration: '5',
  },
});
```

### Luma Dream Machine
```typescript
const luma = await fal.subscribe('fal-ai/luma-dream-machine', {
  input: {
    prompt: 'A timelapse of clouds moving over mountains',
    aspect_ratio: '16:9',
  },
});
```

## Real-Time Generation

### WebSocket Streaming
```typescript
import * as fal from '@fal-ai/serverless-client';

// Real-time image generation with streaming
const stream = await fal.stream('fal-ai/flux/dev', {
  input: {
    prompt: 'A beautiful sunset',
    image_size: 'landscape_16_9',
  },
});

for await (const event of stream) {
  if (event.images) {
    console.log('Generated:', event.images[0].url);
  }
}
```

### Real-Time SDXL (Low Latency)
```typescript
// Ultra-low latency for interactive apps
const realtime = await fal.subscribe('fal-ai/fast-lcm-diffusion', {
  input: {
    prompt: 'Abstract art',
    image_size: 'square',
    num_inference_steps: 4,  // LCM needs very few steps
  },
});
```

## Background Removal & Editing

```typescript
// Remove background
const nobg = await fal.subscribe('fal-ai/birefnet', {
  input: {
    image_url: 'https://example.com/photo.jpg',
  },
});

// Upscale image
const upscaled = await fal.subscribe('fal-ai/creative-upscaler', {
  input: {
    image_url: 'https://example.com/small.jpg',
    scale: 2,
    creativity: 0.5,
    prompt: 'High quality, detailed',
  },
});

// Face swap
const swapped = await fal.subscribe('fal-ai/face-swap', {
  input: {
    base_image_url: 'https://example.com/target.jpg',
    swap_image_url: 'https://example.com/face.jpg',
  },
});
```

## Next.js Integration

### API Route (App Router)
```typescript
// app/api/generate/route.ts
import * as fal from '@fal-ai/serverless-client';
import { NextRequest, NextResponse } from 'next/server';

fal.config({ credentials: process.env.FAL_KEY });

export async function POST(request: NextRequest) {
  const { prompt, model = 'fal-ai/flux/schnell' } = await request.json();
  
  try {
    const result = await fal.subscribe(model, {
      input: {
        prompt,
        image_size: 'landscape_16_9',
        num_images: 1,
      },
    });
    
    return NextResponse.json({ 
      imageUrl: result.images[0].url,
      seed: result.seed,
    });
  } catch (error) {
    return NextResponse.json({ error: 'Generation failed' }, { status: 500 });
  }
}
```

### Proxy Route for Client-Side
```typescript
// app/api/fal/proxy/route.ts
import { route } from '@fal-ai/serverless-client/server-proxy';

export const { GET, POST, PUT, DELETE } = route;
```

```typescript
// Client-side usage
'use client';
import * as fal from '@fal-ai/serverless-client';

fal.config({ proxyUrl: '/api/fal/proxy' });

async function generateImage(prompt: string) {
  const result = await fal.subscribe('fal-ai/flux/schnell', {
    input: { prompt, image_size: 'square_hd' },
  });
  return result.images[0].url;
}
```

## Queue System for Long Tasks

```typescript
// Submit to queue (returns immediately)
const { request_id } = await fal.queue.submit('fal-ai/flux/dev', {
  input: { prompt: 'Complex scene', num_images: 4 },
});

// Check status
const status = await fal.queue.status('fal-ai/flux/dev', {
  requestId: request_id,
});
console.log(status.status); // 'IN_QUEUE' | 'IN_PROGRESS' | 'COMPLETED'

// Get result when ready
if (status.status === 'COMPLETED') {
  const result = await fal.queue.result('fal-ai/flux/dev', {
    requestId: request_id,
  });
}

// Or use webhooks
await fal.queue.submit('fal-ai/flux/dev', {
  input: { prompt: 'Scene' },
  webhookUrl: 'https://your-server.com/webhook',
});
```

## Model Comparison

| Model | Speed | Quality | Best For |
|-------|-------|---------|----------|
| flux/schnell | ~0.5s | Good | Real-time, previews |
| flux/dev | ~3s | Excellent | Production images |
| flux-pro | ~5s | Best | Professional work |
| fast-sdxl | ~2s | Good | General purpose |
| sd-v3-medium | ~4s | Excellent | Detailed scenes |
| kling-video | ~60s | Good | Video generation |

## 实时流式图像生成

```typescript
// JavaScript/TypeScript 客户端流式生成
import { fal } from "@fal-ai/client";

const stream = await fal.stream("fal-ai/flux/schnell", {
  input: {
    prompt: "a beautiful sunset over mountains",
    num_inference_steps: 20,
  },
});

// 使用异步迭代器处理流式事件
for await (const event of stream) {
  // 每个事件包含 {image: {url: "data:...", content_type: "image/jpeg"}}
  if (event.image?.url) {
    console.log("收到图像预览");
    // 显示图像: event.image.url 是 data URI
    previewImage.src = event.image.url;
  }
}

// 获取最终结果
const finalResult = await stream.done();
console.log("最终图像:", finalResult.images[0].url);
```

```python
# Python 客户端流式生成
import fal_client

for event in fal_client.stream(
    "fal-ai/flux/schnell",
    arguments={
        "prompt": "a beautiful sunset over mountains",
        "num_inference_steps": 20
    },
):
    # 每个事件包含 {"image": {"url": "data:...", "content_type": "image/jpeg"}}
    image_url = event.get("image", {}).get("url")
    if image_url:
        print(f"收到图像预览")
        # 处理预览图像...
```

### React 组件中使用流式生成

```tsx
import { fal } from "@fal-ai/client";
import { useState } from "react";

function ImageGenerator() {
  const [preview, setPreview] = useState<string | null>(null);
  const [finalImage, setFinalImage] = useState<string | null>(null);
  const [isGenerating, setIsGenerating] = useState(false);

  async function generateImage(prompt: string) {
    setIsGenerating(true);
    setPreview(null);
    setFinalImage(null);

    const stream = await fal.stream("fal-ai/flux/schnell", {
      input: { prompt, num_inference_steps: 20 },
    });

    for await (const event of stream) {
      if (event.image?.url) {
        setPreview(event.image.url);  // 显示实时预览
      }
    }

    const result = await stream.done();
    setFinalImage(result.images[0].url);
    setIsGenerating(false);
  }

  return (
    <div>
      <button onClick={() => generateImage("a cat in space")}>
        {isGenerating ? "生成中..." : "生成图像"}
      </button>
      {preview && <img src={preview} alt="预览" className="opacity-50" />}
      {finalImage && <img src={finalImage} alt="最终结果" />}
    </div>
  );
}
```

## 相关资源

- **fal.ai 文档**: https://fal.ai/docs
- **模型库**: https://fal.ai/models
- **API 参考**: https://fal.ai/docs/api-reference
- **流式处理文档**: https://fal.ai/docs/streaming
- **定价**: https://fal.ai/pricing

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1837620622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
