---
name: libweb
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libweb Skill

## When to Use

- Adding authentication to HTTP endpoints
- Configuring CORS for API access
- Validating request payloads
- Building secure REST APIs with Hono

## Key Concepts

**authMiddleware**: Validates JWT tokens from Authorization header.

**corsMiddleware**: Handles preflight requests and CORS headers.

**validationMiddleware**: Validates request body against JSON schema.

## Usage Patterns

### Pattern 1: Secure endpoint

```javascript
import { authMiddleware, corsMiddleware } from "@copilot-ld/libweb";

app.use(corsMiddleware({ origins: ["http://localhost:3000"] }));
app.use("/api/*", authMiddleware(authConfig));
```

### Pattern 2: Validate requests

```javascript
import { validationMiddleware } from "@copilot-ld/libweb";

const schema = {
  type: "object",
  required: ["message"],
  properties: { message: { type: "string", maxLength: 1000 } },
};

app.post("/api/chat", validationMiddleware(schema), handler);
```

## Integration

Used by Web and API extensions. Works with Hono framework middleware pattern.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
