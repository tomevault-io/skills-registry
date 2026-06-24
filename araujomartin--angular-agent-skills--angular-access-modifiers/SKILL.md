---
name: angular-access-modifiers
description: > Use when this capability is needed.
metadata:
  author: araujomartin
---

## Version Support

**Minimum Angular Version:** 15.0.0  
**Maximum Angular Version:** 21.0.0  
**Supported Versions:** 15, 16, 17, 18, 19, 20, 21

## When to Use

Load this skill when:
- Defining the public API of a component
- Exposing inputs/outputs (signals)
- Declaring state for the template
- Hiding internal logic from consumers and the template

## Critical Patterns



### public readonly for API/Inputs/Outputs

**Supported in:** v17+

Use `public readonly` to expose the public API of the component, using the modern `input()` and `output()` functions for signal-based inputs and outputs.

```typescript
import { Component, input, output, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-example',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `<button (click)="save()">Save</button>`
})
export class ExampleComponent {
  public readonly name = input.required<string>(); // input signal
  public readonly saved = output<void>(); // output signal

  save() {
    this.saved.emit();
  }
}
```

### protected readonly for template state

**Supported in:** v15+

Use `protected readonly` for properties accessed only by the class and the template (e.g., internal signals for displaying state).

```typescript
import { Component, signal, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-template-state',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `<div>{{ count() }}</div>`
})
export class TemplateStateComponent {
  protected readonly count = signal(0);
}
```

### private readonly for internal logic

**Supported in:** v15+

Use `private readonly` for properties only used inside the class (not accessible from the template or outside the component).

```typescript
import { Component, signal, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-secret',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `<span>Secret logic</span>`
})
export class SecretComponent {
  private readonly secretValue = signal('hidden');
}
```


## Extended examples and advanced patterns

For complete examples, common mistakes, and advanced patterns, see [references/patterns.md](references/patterns.md).


## Common Mistakes

### ❌ Don't use public for everything

**Why this is problematic:** Exposes internal state/logic unnecessarily.

```typescript
export class BadComponent {
  public count = 0; // ❌ Should be protected or private
}
```

**✅ Instead:**

```typescript
export class GoodComponent {
  protected readonly count = 0;
}
```

### ❌ Don't use private for template state

**Why this is problematic:** The template cannot access private members.

```typescript
export class BadComponent {
  private readonly count = 0; // ❌ Not accessible in the template
}
```

**✅ Instead:**

```typescript
export class GoodComponent {
  protected readonly count = 0; // ✅ Accessible in the template
}
```


## Quick Reference

| Task | Pattern | Version |
|------|---------|---------|
| Input signal | `public readonly input()` | v17+ |
| Output signal | `public readonly output()` | v17+ |
| Template state | `protected readonly signal()` | v15+ |
| Internal logic | `private readonly signal()` | v15+ |


## Resources

- [Angular.dev](https://angular.dev)
- [Angular Style Guide](https://angular.dev/style-guide)
- [TypeScript Handbook: Classes](https://www.typescriptlang.org/docs/handbook/classes.html)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/araujomartin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
