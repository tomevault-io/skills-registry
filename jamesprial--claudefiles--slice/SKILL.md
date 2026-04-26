---
name: go-nil-slice
description: Slice zero-value behavior - nil slice is usable Use when this capability is needed.
metadata:
  author: jamesprial
---

# Slice Zero-Value

## Problem
Nil slices are valid and usable, but behavior differs from empty slices.

## Pattern

### CORRECT - Nil slice works with append
```go
var s []int  // nil slice
s = append(s, 1, 2, 3)  // OK, creates new backing array
fmt.Println(s)  // [1 2 3]
```

### Nil vs Empty Slice
```go
var nilSlice []int           // nil slice
emptySlice := []int{}        // empty slice (non-nil)

len(nilSlice) == 0           // true
len(emptySlice) == 0         // true

nilSlice == nil              // true
emptySlice == nil            // false

json.Marshal(nilSlice)       // "null"
json.Marshal(emptySlice)     // "[]"
```

## When It Matters
```go
// JSON encoding differs
type Response struct {
    Items []string  // Will encode as null if nil, [] if empty
}
```

## Quick Fix
- [ ] Nil slices work with append, len, range
- [ ] Use nil for "no data", []T{} for "empty data"
- [ ] Check JSON encoding behavior if needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jamesprial) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
