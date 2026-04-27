---
name: oauth-config-generator
description: Generate OAuth 2.0 configuration for social login providers (Google, GitHub, etc.). Triggers on "create oauth config", "generate oauth setup", "social login config", "oauth2 integration". Use when this capability is needed.
metadata:
  author: ehtbanton
---

# OAuth Config Generator

Generate OAuth 2.0 configuration for social authentication providers.

## Output Requirements

**File Output:** `oauth.ts` with provider configurations
**Format:** Valid TypeScript
**Standards:** OAuth 2.0, Passport.js

## When Invoked

Immediately generate complete OAuth configuration for specified providers.

## Example Invocations

**Prompt:** "Create OAuth config for Google and GitHub"
**Output:** Complete OAuth setup with both providers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ehtbanton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
