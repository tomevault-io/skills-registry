---
name: zuplo
description: > Use when this capability is needed.
metadata:
  author: neversight
---

## Critical Patterns

### Route Configuration (REQUIRED)

```json
{
  "routes": [
    {
      "path": "/api/users",
      "methods": ["GET", "POST"],
      "handler": {
        "module": "$import(@zuplo/runtime)",
        "export": "urlRewriteHandler",
        "options": {
          "rewritePattern": "https://api.example.com/users"
        }
      },
      "policies": {
        "inbound": ["rate-limit", "api-key-auth"]
      }
    }
  ]
}
```

### API Key Auth (REQUIRED)

```typescript
// ✅ ALWAYS: Use built-in API key authentication
export default {
  policies: {
    inbound: [
      {
        name: "api-key-auth",
        policyType: "api-key-inbound",
        handler: {
          export: "ApiKeyInboundPolicy",
          module: "$import(@zuplo/runtime)"
        }
      }
    ]
  }
};
```

---

## Decision Tree

```
Need auth?                 → Use api-key-inbound policy
Need rate limiting?        → Use rate-limit policy
Need caching?              → Use cache policy
Need transforms?           → Use custom policy handler
Need monitoring?           → Enable analytics
```

---

## Resources

- **Gateway Setup**: [gateway-setup.md](gateway-setup.md)
- **GitOps**: [gitops.md](gitops.md)
- **Performance**: [performance.md](performance.md)
- **Security**: [security.md](security.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
