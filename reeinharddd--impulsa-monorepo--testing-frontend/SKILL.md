---
name: testing-frontend
description: Frontend testing standards (Angular). Use when writing component or service tests for Web. Use when this capability is needed.
metadata:
  author: reeinharddd
---

# Frontend Testing Skill

> **Purpose:** Ensure UI stability via Component and Service tests.

## File Naming

- **Component:** `[name].component.spec.ts` (alongside source).
- **Service:** `[name].service.spec.ts`.

## Testing Signals

```typescript
it("should update signal", () => {
  fixture.componentRef.setInput("inputName", value);
  fixture.detectChanges();
  expect(component.computedValue()).toBe(expected);
});
```

## Rules

- **No Flaky Tests**: Must be deterministic.
- **Edge Cases**: Test empty states, error states, loading states.
- **Coverage**: 70% minimum for components.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reeinharddd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
