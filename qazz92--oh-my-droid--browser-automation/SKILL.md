---
name: browser-automation
description: Web browser automation for testing and data extraction Use when this capability is needed.
metadata:
  author: qazz92
---

# Browser Automation Skill

Automated web browser interactions.

## When to Use
- Form filling and submission
- Screenshot capture
- Data extraction
- E2E testing

## What_You_MUST_Do>
1. NAVIGATE to the target URL first
2. WAIT for page to load before interactions
3. USE snapshot for accessibility tree (better than screenshot)
4. VERIFY elements exist before interacting
5. HANDLE errors and timeouts gracefully

## What_You_MUST_NOT_Do>
1. DO NOT interact before page loads
2. DO NOT assume elements exist - verify first
3. DO NOT skip error handling
4. DO NOT use for localhost or private network URLs

## Capabilities

### Navigation
```typescript
await navigate("https://example.com")
await goBack()
await refresh()
```

### Interaction
```typescript
await click(selector)
await type(selector, "text")
await select(selector, "option")
```

### Verification
```typescript
await expect(selector).toBeVisible()
await takeScreenshot("page.png")
```

## Usage
Use with CDP (Chrome DevTools Protocol) tools.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qazz92) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
