---
name: juicebox-common-errors
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# Juicebox Common Errors

## Error Reference

### 401 Authentication
```json
{"error": "invalid_api_key"}
```
**Fix:** Verify key at app.juicebox.ai > Settings.

### 403 Plan Limits
```json
{"error": "quota_exceeded"}
```
**Fix:** Check quota in dashboard, upgrade plan.

### 429 Rate Limited
**Fix:** Check `Retry-After` header. Implement exponential backoff.

### 400 Invalid Query
**Fix:** Ensure query is non-empty, check filter syntax.

### 404 Profile Not Found
**Fix:** Profile may be removed. Re-run search.

## Quick Diagnostic
```bash
curl -s -H "Authorization: Bearer $JUICEBOX_API_KEY" \
  https://api.juicebox.ai/v1/health
```

## Resources
- [Juicebox Docs](https://docs.juicebox.work)

## Next Steps
See `juicebox-debug-bundle`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
