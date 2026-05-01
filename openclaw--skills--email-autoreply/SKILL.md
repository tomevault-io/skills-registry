---
name: email-autoreply
description: Generate context-aware email replies. Use when this capability is needed.
metadata:
  author: openclaw
---
# Email Auto-Reply Generator

Generate context-aware email replies.

## Usage

```bash
npx email-autoreply
```

## API

```typescript
import { generateReply } from 'email-autoreply';

const reply = await generateReply({
  scenario: "product-inquiry",
  tone: "professional",
  originalEmail: "你们的产品多少钱？"
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
