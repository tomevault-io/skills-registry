---
name: social-scheduler-pro
description: Optimize posting schedule for maximum engagement. Use when this capability is needed.
metadata:
  author: openclaw
---
# Social Media Scheduler

Optimize posting schedule for maximum engagement.

## Platforms

- **Chinese**: Douyin, Xiaohongshu, WeChat, Weibo, Kuaishou
- **International**: Instagram, Twitter, Facebook, LinkedIn

## Usage

```bash
npx social-scheduler
```

## API

```typescript
import { getSchedule } from 'social-scheduler';

const schedule = await getSchedule({
  platform: 'xiaohongshu',
  contentType: 'product-review',
  frequency: 'daily'
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
