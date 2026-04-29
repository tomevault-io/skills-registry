---
name: xss-prevention
description: > Use when this capability is needed.
metadata:
  author: aj-geddes
---

# XSS Prevention

## Table of Contents

- [Overview](#overview)
- [When to Use](#when-to-use)
- [Quick Start](#quick-start)
- [Reference Guides](#reference-guides)
- [Best Practices](#best-practices)

## Overview

Implement comprehensive Cross-Site Scripting (XSS) prevention using input sanitization, output encoding, CSP headers, and secure coding practices.

## When to Use

- User-generated content display
- Rich text editors
- Comment systems
- Search functionality
- Dynamic HTML generation
- Template rendering

## Quick Start

Minimal working example:

```javascript
// xss-prevention.js
const createDOMPurify = require("dompurify");
const { JSDOM } = require("jsdom");
const he = require("he");

const window = new JSDOM("").window;
const DOMPurify = createDOMPurify(window);

class XSSPrevention {
  /**
   * HTML Entity Encoding - Safest for text content
   */
  static encodeHTML(str) {
    return he.encode(str, {
      useNamedReferences: true,
      encodeEverything: false,
    });
  }

  /**
   * Sanitize HTML - For rich content
   */
  static sanitizeHTML(dirty) {
    const config = {
      ALLOWED_TAGS: [
// ... (see reference guides for full implementation)
```

## Reference Guides

Detailed implementations in the `references/` directory:

| Guide | Contents |
|---|---|
| [Node.js XSS Prevention](references/nodejs-xss-prevention.md) | Node.js XSS Prevention |
| [Python XSS Prevention](references/python-xss-prevention.md) | Python XSS Prevention |
| [React XSS Prevention](references/react-xss-prevention.md) | React XSS Prevention |
| [Content Security Policy](references/content-security-policy.md) | Content Security Policy |

## Best Practices

### ✅ DO

- Encode output by default
- Use templating engines
- Implement CSP headers
- Sanitize rich content
- Validate URLs
- Use HTTPOnly cookies
- Regular security testing
- Use secure frameworks

### ❌ DON'T

- Trust user input
- Use innerHTML directly
- Skip output encoding
- Allow inline scripts
- Use eval()
- Mix contexts (HTML/JS)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aj-geddes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
