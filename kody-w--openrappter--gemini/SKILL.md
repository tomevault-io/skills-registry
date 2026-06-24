---
name: gemini
description: Use Google Gemini API for text generation, vision, and multimodal tasks. Use when this capability is needed.
metadata:
  author: kody-w
---

# Gemini

Access Google's Gemini models via the API.

## Text Generation

```bash
curl -s "https://generativelanguage.googleapis.com/v1/models/gemini-pro:generateContent?key=$GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"contents":[{"parts":[{"text":"Explain quantum computing"}]}]}'
```

## Vision (Image Analysis)

```bash
# Base64 encode an image and send with prompt
IMAGE_B64=$(base64 -i image.jpg)
curl -s "https://generativelanguage.googleapis.com/v1/models/gemini-pro-vision:generateContent?key=$GEMINI_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "contents":[{"parts":[
      {"text":"What is in this image?"},
      {"inlineData":{"mimeType":"image/jpeg","data":"'$IMAGE_B64'"}}
    ]}]
  }'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kody-w) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
