---
name: ddd-in-go
description: Domain-Driven Design patterns for Go applications Use when this capability is needed.
metadata:
  author: mthang1801
---

## Core Concepts

- BaseAggregate, BaseEntity
- Value objects (immutable)
- Domain events
- Repository pattern (port & adapter)
- CQRS pattern
- Specification pattern
- Saga pattern
- Event Sourcing pattern
- Event bus

## Example

```go
// ✅ GOOD: Aggregate with domain events
type User struct {
    BaseAggregate
    email string
    status UserStatus
}

func (u *User) ChangeEmail(email string) error {
    if u.status == UserStatusInactive {
        return ErrUserInactive
    }
    u.email = email
    u.AddEvent(&UserEmailChanged{UserID: u.ID, Email: email})
    return nil
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mthang1801) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
