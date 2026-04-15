---
name: guidewire-rate-limits
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Guidewire Rate Limits

## Overview

Cloud API enforces per-tenant rate limits. Batch operations use the batch API endpoint. Implement exponential backoff on 429 responses. Use API Gateway throttling in GCC. Optimize with bulk endpoints for batch processing.

For detailed implementation, see: [implementation guide](references/implementation-guide.md)

## Resources

- [Guidewire Developer Portal](https://developer.guidewire.com/)
- [Cloud API Reference](https://docs.guidewire.com/cloud/pc/202503/apiref/)
- [Guidewire Cloud Console](https://gcc.guidewire.com)
- [Gosu Language Guide](https://gosu-lang.github.io/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
