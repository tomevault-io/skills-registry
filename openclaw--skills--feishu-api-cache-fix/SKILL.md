---
name: feishu-api-cache-fix
description: > Fix Feishu API rate limit issue Use when this capability is needed.
metadata:
  author: openclaw
---
# feishu-api-cache-fix

> Fix Feishu API rate limit issue

**Version**: 1.0.1
**Author**: @bryan-chx
**Tags**: feishu, api, fix, performance

## Problem

Gateway calls Feishu API every minute, causing rate limit exhaustion.

## Solution

Add 2-hour cache to probe.ts

## Usage

```bash
sudo bash fix_feishu_cache.sh
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
