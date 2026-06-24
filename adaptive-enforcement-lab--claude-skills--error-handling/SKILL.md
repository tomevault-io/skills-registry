---
name: error-handling
description: >- Use when this capability is needed.
metadata:
  author: adaptive-enforcement-lab
---

# Error Handling

## When to Use This Skill

Error handling for GitHub App tokens addresses:

- **Token expiration (401)** - Expired tokens after 1 hour
- **Permission errors (403)** - Missing app permissions or installation scopes
- **Rate limits (429)** - API usage limits and retry strategies
- **Network failures** - Transient connectivity issues
- **Validation errors (422)** - Invalid request payloads

> **Error Handling Strategy**
>
>
> 1. **Detect** - Identify error type from HTTP status codes
> 2. **Classify** - Determine if error is retryable
> 3. **Retry** - Use exponential backoff for transient errors
> 4. **Escalate** - Provide actionable messages for permanent failures


## Implementation

See the full implementation guide in the [source documentation](https://adaptive-enforcement-lab.com/patterns/github-actions/).


## Examples

See [examples.md](examples.md) for code examples.


## Full Reference

See [reference.md](reference.md) for complete documentation.
## References

- [Source Documentation](https://adaptive-enforcement-lab.com/patterns/github-actions/)
- [AEL Patterns](https://adaptive-enforcement-lab.com/patterns/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptive-enforcement-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
