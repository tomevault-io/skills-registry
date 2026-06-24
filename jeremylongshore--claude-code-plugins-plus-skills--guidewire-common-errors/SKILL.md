---
name: guidewire-common-errors
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Guidewire Common Errors

## Overview

Fix 400 (validation), 401 (OAuth token expired), 403 (missing API role), 404 (wrong endpoint path), 409 (stale checksum - re-GET and retry), 422 (business rule violation - read userMessage). Gosu errors: ClassNotFoundException (wrong module), NPE (null entity reference), ValidationException (missing required fields).

For detailed implementation, see: [implementation guide](references/implementation-guide.md)

## Resources

- [Guidewire Developer Portal](https://developer.guidewire.com/)
- [Cloud API Reference](https://docs.guidewire.com/cloud/pc/202503/apiref/)
- [Guidewire Cloud Console](https://gcc.guidewire.com)
- [Gosu Language Guide](https://gosu-lang.github.io/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
