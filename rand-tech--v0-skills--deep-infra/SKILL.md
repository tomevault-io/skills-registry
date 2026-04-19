---
name: deep-infra
description: Deep Infra AI integration for model inference. Use when implementing Deep Infra-powered AI features. Use when this capability is needed.
metadata:
  author: rand-tech
---

# Deep Infra Integration

Deep Infra provides AI model inference.

## Guidelines

- The Deep Infra integration uses the `DEEPINFRA_API_KEY` environment variable.
- The Deep Infra integration should **ONLY** be used if specifically requested by the user.
- Otherwise, use the Vercel AI Gateway and AI SDK so the user does not need to configure anything.

## Environment Variables

- `DEEPINFRA_API_KEY` - Deep Infra API key

## Example Usage

```typescript
import { generateText } from 'ai'
import { createDeepInfra } from '@ai-sdk/deepinfra'

const deepinfra = createDeepInfra({
  apiKey: process.env.DEEPINFRA_API_KEY,
})

const { text } = await generateText({
  model: deepinfra('meta-llama/Meta-Llama-3.1-70B-Instruct'),
  prompt: 'What is the meaning of life?',
})
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rand-tech) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
