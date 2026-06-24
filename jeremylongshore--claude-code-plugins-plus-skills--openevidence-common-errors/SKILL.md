---
name: openevidence-common-errors
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Common Errors

## Overview
Quick reference for OpenEvidence API errors with solutions.

### 401 — Authentication Failed
**Fix:** Verify API key at OpenEvidence developer portal.

### 403 — Organization Access Denied
**Fix:** Verify org ID and API key permissions.

### 422 — Invalid Query
**Fix:** Clinical question must be medical in nature.

### 429 — Rate Limited
**Fix:** Implement backoff. Check `Retry-After` header.

### 503 — Service Unavailable
**Fix:** DeepConsult queue may be full. Retry after delay.

## Quick Diagnostic
```bash
# Check API connectivity
curl -s -w "\nHTTP %{http_code}" https://api.openevidence.com/v1/health 2>/dev/null || echo "Endpoint check needed"
echo $OPENEVIDENCE_API_KEY | head -c 10
```

## Resources
- [OpenEvidence Docs](https://www.openevidence.com)

## Next Steps
See `openevidence-debug-bundle`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
