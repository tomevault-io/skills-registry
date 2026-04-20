---
name: slice-factory
description: Zustand slice factory design. Use when creating new form input slices, understanding slice patterns, or asking about slice architecture. Use when this capability is needed.
metadata:
  author: hajimism
---

# Slice Factory

Location: `front/src/model/common/lib/slice-factory.ts`

## Purpose

Factory for generating Zustand slices for form input fields.
Reduces boilerplate (value, setter, getErrorMessages, getIsValid).

Key names can be input in kebab-case. Matching directory names ensures naming consistency.

## Design Decisions

### Why a factory?

Each slice was repeating the same structure:

```ts
// Before: 26 lines/slice
export type TitleSlice = {
  title: string
  setTitle: (title: string) => void
  getTitleErrorMessages: (phase: ValidationPhase) => string[]
  getTitleIsValid: (phase: ValidationPhase) => boolean
}
export const createTitleSlice = (initial) => (set, get) => ({
  title: initial,
  setTitle: (v) => set({ title: v }),
  getTitleErrorMessages: (phase) => getValidationErrorMessage({...}),
  getTitleIsValid: (phase) => ... .length === 0,
})

// After: 4 lines/slice
export type TitleSlice = InputSliceShape<'title', string>
export const createTitleSlice = createInputSlice('title', titleValidation)
```

### Type-safe key generation

`InputSliceShape<K, T>` auto-generates method names from key:

- `'title'` → `title`, `setTitle`, `getTitleErrorMessages`, `getTitleIsValid`
- Type-safe via TypeScript template literal types

### Kebab-case support

Input keys in kebab-case are auto-converted to camelCase:

- `'notify-subscribers'` → `notifySubscribers`, `setNotifySubscribers`, ...
- Matches directory naming convention

### Validation optional

Validation function is optional:

- **With**: `createInputSlice('title', titleValidation)` → errors from validation result
- **Without**: `createInputSlice('category')` → errors always empty, IsValid always true

Use for enum fields or always-valid fields.

## When NOT to use

Cases the factory doesn't cover:

1. **Cross-field dependency**: `password` must match `confirmPassword`
2. **Custom methods**: `getPasswordStrength()` or similar
3. **Conditional validation**: validation rules change based on state

→ Write slice manually, or factory + spread composition

```ts
// Manual example
export const createPasswordSlice = (initial) => (set, get) => ({
  password: initial,
  setPassword: (v) => set({ password: v }),
  getPasswordErrorMessages: (phase) => {
    const base = validateWithStandardSchema(...)
    if (get().password !== get().confirmPassword) {
      base.push({ key: 'password', input: v, message: 'Passwords must match' })
    }
    return base
  },
  ...
})

// Factory + additional methods
const baseSlice = createInputSlice('password', passwordValidation)
export const createPasswordSlice = (initial) => (...args) => ({
  ...baseSlice(initial)(...args),
  getPasswordStrength: () => calculateStrength(args[1]().password),
})
```

## Key Files

- `front/src/model/common/lib/slice-factory.ts` - Factory implementation
- `front/src/model/common/lib/slice-factory.test.ts` - Tests
- `front/src/common/lib/string.ts` - kebabToCamelCase, capitalize
- `front/src/model/article/detail/form/inputs/*/slice.ts` - Usage examples

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hajimism) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
