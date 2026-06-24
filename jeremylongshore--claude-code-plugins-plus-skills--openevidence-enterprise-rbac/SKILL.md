---
name: openevidence-enterprise-rbac
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---

# OpenEvidence Enterprise RBAC

## Role Matrix
| Role | Read | Write | Admin |
|------|------|-------|-------|
| Viewer | Yes | No | No |
| Editor | Yes | Yes | No |
| Admin | Yes | Yes | Yes |

## Implementation
```typescript
const PERMS = {
  viewer: { read: true, write: false, admin: false },
  editor: { read: true, write: true, admin: false },
  admin: { read: true, write: true, admin: true },
};
```

## Resources
- [OpenEvidence Enterprise](https://www.openevidence.com)

## Next Steps
See `openevidence-migration-deep-dive`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
