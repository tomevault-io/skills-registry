---
name: urlsession-code-review
description: Reviews URLSession networking code for iOS/macOS. Covers async/await patterns, request building, error handling, caching, and background sessions. Use when this capability is needed.
metadata:
  author: existential-birds
---

# URLSession Code Review

## Quick Reference

| Topic | Reference |
|-------|-----------|
| Async/Await | [async-networking.md](references/async-networking.md) |
| Requests | [request-building.md](references/request-building.md) |
| Errors | [error-handling.md](references/error-handling.md) |
| Caching | [caching.md](references/caching.md) |

## Review Checklist

### Response Validation
- [ ] HTTP status codes validated - URLSession does NOT throw on 404/500
- [ ] Response cast to HTTPURLResponse before checking status
- [ ] Both transport errors (URLError) and HTTP errors handled

### Memory & Resources
- [ ] Downloaded files moved/deleted (async API doesn't auto-delete)
- [ ] Sessions with delegates call `finishTasksAndInvalidate()`
- [ ] Long-running tasks use `[weak self]`
- [ ] Stored Task references cancelled when appropriate

### Configuration
- [ ] `timeoutIntervalForResource` set (default is 7 days!)
- [ ] URLCache sized adequately (default 512KB too small)
- [ ] Sessions reused for connection pooling

### Background Sessions
- [ ] Unique identifier (especially with app extensions)
- [ ] File-based uploads (not data-based)
- [ ] Delegate methods used (not completion handlers)

### Security
- [ ] No hardcoded secrets (use Keychain)
- [ ] Header values sanitized for CRLF injection
- [ ] Query params via URLComponents (not string concat)

## Output Format

```markdown
### Critical
1. [FILE:LINE] Missing HTTP status validation
   - Issue: 404/500 responses not treated as errors
   - Fix: Check `httpResponse.statusCode` is 200-299
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/existential-birds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
