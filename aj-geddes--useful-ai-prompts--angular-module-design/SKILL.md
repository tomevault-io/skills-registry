---
name: angular-module-design
description: > Use when this capability is needed.
metadata:
  author: aj-geddes
---

# Angular Module Design

## Table of Contents

- [Overview](#overview)
- [When to Use](#when-to-use)
- [Quick Start](#quick-start)
- [Reference Guides](#reference-guides)
- [Best Practices](#best-practices)

## Overview

Architect scalable Angular applications using feature modules, lazy loading, services, and RxJS for reactive programming patterns.

## When to Use

- Large Angular applications
- Feature-based organization
- Lazy loading optimization
- Dependency injection patterns
- Reactive state management

## Quick Start

Minimal working example:

```typescript
// users.module.ts
import { NgModule } from "@angular/core";
import { CommonModule } from "@angular/common";
import { ReactiveFormsModule } from "@angular/forms";
import { UsersRoutingModule } from "./users-routing.module";
import { UsersListComponent } from "./components/users-list/users-list.component";
import { UserDetailComponent } from "./components/user-detail/user-detail.component";
import { UsersService } from "./services/users.service";

@NgModule({
  declarations: [UsersListComponent, UserDetailComponent],
  imports: [CommonModule, ReactiveFormsModule, UsersRoutingModule],
  providers: [UsersService],
})
export class UsersModule {}
```

## Reference Guides

Detailed implementations in the `references/` directory:

| Guide | Contents |
|---|---|
| [Feature Module Structure](references/feature-module-structure.md) | Feature Module Structure |
| [Lazy Loading Routes](references/lazy-loading-routes.md) | Lazy Loading Routes |
| [Service with RxJS](references/service-with-rxjs.md) | Service with RxJS |
| [Smart and Presentational Components](references/smart-and-presentational-components.md) | Smart and Presentational Components |
| [Dependency Injection and Providers](references/dependency-injection-and-providers.md) | Dependency Injection and Providers |

## Best Practices

### ✅ DO

- Follow established patterns and conventions
- Write clean, maintainable code
- Add appropriate documentation
- Test thoroughly before deploying

### ❌ DON'T

- Skip testing or validation
- Ignore error handling
- Hard-code configuration values

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aj-geddes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
