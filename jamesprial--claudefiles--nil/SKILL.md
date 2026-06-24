---
name: go-nil
description: Go nil safety patterns. Routes to specific traps. Use when this capability is needed.
metadata:
  author: jamesprial
---

# Nil Safety

## Route by Type
- Interface nil trap → see [interface/](interface/)
- Map nil writes → see [map/](map/)
- Slice zero-value → see [slice/](slice/)
- Pointer receivers → see [pointer/](pointer/)

## Quick Check
- [ ] Check pointers before deref
- [ ] Check maps before write
- [ ] Typed nil != nil interface

## Common Gotcha
```go
var p *int
if p == nil {  // true
    fmt.Println("nil pointer")
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamesprial) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
