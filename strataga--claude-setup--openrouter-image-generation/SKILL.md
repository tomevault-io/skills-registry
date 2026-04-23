---
name: openrouter-image-generation
description: | Use when this capability is needed.
metadata:
  author: strataga
---

# OpenRouter Image Generation Response Format

## Problem
When using OpenRouter's image generation models (like `google/gemini-2.5-flash-image`),
the response format differs from expectations. Images are not returned in the standard
location, causing parsing failures.

## Context / Trigger Conditions
- Using OpenRouter API (`https://openrouter.ai/api/v1/chat/completions`) for image generation
- Model is an image generation model (e.g., `google/gemini-2.5-flash-image`)
- Response returns 200 OK but no image found in expected location
- Looking for image URL or base64 data and not finding it

## Solution

### 1. Use the Chat Completions Endpoint (NOT /images/generations)
```python
response = requests.post(
    "https://openrouter.ai/api/v1/chat/completions",  # NOT /images/generations
    headers={
        "Authorization": f"Bearer {OPENROUTER_API_KEY}",
        "Content-Type": "application/json",
    },
    json={
        "model": "google/gemini-2.5-flash-image",
        "messages": [{"role": "user", "content": "Generate a pixel art sword"}],
    },
)
```

### 2. Extract Image from Correct Location
Images are returned in `choices[0].message.images` array, NOT in a standard image field:

```python
result = response.json()
message = result.get("choices", [{}])[0].get("message", {})
images = message.get("images", [])

if images:
    # Image URL is a base64 data URL
    img_url = images[0].get("image_url", {}).get("url", "")
    # Format: "data:image/png;base64,<base64_data>"

    if img_url.startswith("data:image"):
        # Extract base64 data after the comma
        b64_data = img_url.split(",")[1]
        img_bytes = base64.b64decode(b64_data)
```

### 3. Complete Response Structure
```json
{
  "choices": [{
    "message": {
      "content": "Here is your image...",
      "images": [{
        "image_url": {
          "url": "data:image/png;base64,iVBORw0KGgo..."
        }
      }]
    }
  }]
}
```

## Verification
- Check that `response.json()["choices"][0]["message"]["images"]` exists and has items
- Verify the `image_url.url` starts with `data:image`
- Decode base64 and write to file - should produce valid image

## Example

```python
import requests
import base64

def generate_image(prompt: str, api_key: str) -> bytes:
    response = requests.post(
        "https://openrouter.ai/api/v1/chat/completions",
        headers={
            "Authorization": f"Bearer {api_key}",
            "Content-Type": "application/json",
        },
        json={
            "model": "google/gemini-2.5-flash-image",
            "messages": [{"role": "user", "content": prompt}],
        },
        timeout=120,
    )

    result = response.json()
    images = result["choices"][0]["message"].get("images", [])

    if not images:
        raise ValueError("No image in response")

    img_url = images[0]["image_url"]["url"]
    b64_data = img_url.split(",")[1]
    return base64.b64decode(b64_data)

# Usage
img_bytes = generate_image("A pixel art treasure chest", api_key)
with open("treasure.png", "wb") as f:
    f.write(img_bytes)
```

## Notes
- All models generate at fixed sizes (typically 1024x1024) regardless of requested size
- The `/images/generations` endpoint may return 404 for some models - use chat/completions instead
- Cost-effective models include `google/gemini-2.5-flash-image` for pixel art generation
- Include style instructions in the prompt for consistent results (e.g., "16-bit pixel art, SNES style")

## References
- OpenRouter API Documentation: https://openrouter.ai/docs
- Available image models can be queried via: `GET https://openrouter.ai/api/v1/models`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/strataga) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
