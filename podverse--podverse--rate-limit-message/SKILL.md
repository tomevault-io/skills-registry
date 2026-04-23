---
name: rate-limit-message
description: Standardizes 429 handling by using the shared rate-limit helper to render user-facing messages. Use when handling API errors that may return 429 (rate limited) responses or when the user mentions rate limiting. Use when this capability is needed.
metadata:
  author: podverse
---

# Rate Limit Message Handling

## Instructions

- When an API call can return a 429 rate-limit response, use the shared helper in `apps/web/src/utils/rateLimit/rateLimitAlert.ts`.
- Prefer rendering the returned message in the UI instead of relying on alert dialogs when a component has a dedicated error/message area.
- Keep existing call sites working by using the helper’s optional `onMessage` and `suppressAlert` options when UI rendering is needed.

## Example

```ts
const handled = await handleRateLimitAlert(error, undefined, tMisc, {
  suppressAlert: true,
  onMessage: (message) => setStatusError(message),
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/podverse) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
