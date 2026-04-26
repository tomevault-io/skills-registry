---
name: backend-architecture-modules
description: Modular structure, namespace organization. Use when this capability is needed.
metadata:
  author: valec3
---

# Backend Architecture Modules

## When to use this skill
- Large applications
- Feature-based organization
- Team collaboration
- Reusable modules

## Workflow
- [ ] Organize by feature/module
- [ ] Use namespaces
- [ ] Define module boundaries
- [ ] Create module-specific folders
- [ ] Avoid cross-module dependencies

## Instructions

### Module Structure
```
app/
├── Modules/
│   ├── User/
│   │   ├── Controllers/
│   │   ├── Models/
│   │   ├── Services/
│   │   ├── Entities/
│   │   └── Config/
│   ├── Order/
│   └── Product/
```

### Module Registration
```php
<?php

// Config\Modules.php
$modules->discoverInNamespace = true;
$modules->aliases = [
    'User' => 'App\Modules\User',
    'Order' => 'App\Modules\Order',
];
```

## Resources
- Feature-based organization
- Clear module boundaries
- Independent deployment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
