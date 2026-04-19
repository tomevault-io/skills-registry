---
name: angular-component-inputs
description: > Use when this capability is needed.
metadata:
  author: araujomartin
---
## Version Support

**Minimum Angular Version:** 17.3.0  
**Maximum Angular Version:** 21.0.0  
**Supported Versions:** 17, 18, 19, 20, 21

> **Note:** Signal inputs (`input()`, `model()`) were introduced as **developer preview** in v17.3. They became **stable/production-ready** in v19.0.
## When to Use

Load this skill when:
- Defining component or directive inputs
- Migrating from `@Input()` decorators to function-based APIs
- Implementing reactive component interfaces
- Working with two-way binding using `model()`
- Building components with signal-based reactivity

## Critical Patterns

### Pattern 1: Function-Based Inputs

**Introduced in:** v17.3 (developer preview) | **Stable since:** v19.0

Use the `input()` and `input.required()` functions instead of the `@Input()` decorator. These create `InputSignal` objects that integrate seamlessly with Angular's reactivity system.

```typescript
// ✅ GOOD - Function-based inputs
import { Component, input, ChangeDetectionStrategy } from '@angular/core';

interface User {
  id: string;
  name: string;
}

@Component({
  selector: 'app-user-card',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <div class="user-card">
      <h3>{{ user().name }}</h3>
      <p>ID: {{ user().id }}</p>
      @if (disabled()) {
        <span class="badge">Disabled</span>
      }
    </div>
  `
})
export class UserCardComponent {
  // Required input - will error if not provided
  readonly user = input.required<User>();
  
  // Optional input with default value
  readonly disabled = input(false);
  
  // Optional input without default (undefined initially)
  readonly userId = input<string>();
}
```

```typescript
// ❌ BAD - Old decorator-based approach
import { Component, Input } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `...`
})
export class UserCardComponent {
  @Input({ required: true }) user!: User;
  @Input() disabled = false;
  @Input() selected = false;
}
```
### Pattern 2: Two-Way Binding with model()

**Introduced in:** v17.3 (developer preview) | **Stable since:** v19.0

Use the `model()` function for two-way binding. It creates both the input and its "Change" event automatically, replacing the `[(value)]` two-way binding pattern with decorators.

```typescript
// ✅ GOOD - Model for two-way binding
import { Component, model, input, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-custom-checkbox',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <label>
      <input 
        type="checkbox"
        [checked]="checked()"
        (change)="toggle()"
      />
      {{ label() }}
    </label>
  `
})
export class CustomCheckboxComponent {
  readonly label = input.required<string>();
  
  // Creates both "checked" input and its "checkedChange" event
  readonly checked = model(false);
  
  toggle(): void {
    this.checked.set(!this.checked());
  }
}
```

**Usage in parent:**
```typescript
@Component({
  selector: 'app-parent',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  imports: [CustomCheckboxComponent],
  template: `
    <!-- Two-way binding -->
    <app-custom-checkbox 
      label="Accept terms"
      [(checked)]="termsAccepted"
    />
    
    <!-- Or one-way binding -->
    <app-custom-checkbox 
      label="Subscribe"
      [checked]="subscribed"
      (checkedChange)="onSubscribeChange($event)"
    />
  `
})
export class ParentComponent {
  termsAccepted = signal(false);
  subscribed = signal(true);
  
  onSubscribeChange(value: boolean): void {
    this.subscribed.set(value);
  }
}
```

### Pattern 3: Input Aliases

**Introduced in:** v17.3 (developer preview) | **Stable since:** v19.0

Use the `alias` option to provide a different name for the input property in templates while keeping a different name in the component class.

```typescript
// ✅ GOOD - Input with alias
import { Component, input, ChangeDetectionStrategy } from '@angular/core';

@Component({
  selector: 'app-user-profile',
  standalone: true,
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <h2>{{ userName() }}</h2>
  `
})
export class UserProfileComponent {
  // Template uses "name", component uses "userName"
  readonly userName = input.required<string>({ alias: 'name' });
}
```

**Usage:**
```html
<!-- Use the alias in templates -->
<app-user-profile name="John Doe" />
```

For detailed examples and common mistakes, see [references/input-patterns.md](references/input-patterns.md).

## Quick Reference

| Task | Pattern |
|------|---------|
| Required input | `readonly user = input.required<User>()` |
| Optional input with default | `readonly disabled = input(false)` |
| Optional input without default | `readonly userId = input<string>()` |
| Input with alias | `readonly userName = input.required<string>({ alias: 'name' })` |
| Two-way binding | `readonly checked = model(false)` |
| Required model | `readonly value = model.required<string>()` |
| Access input value | `this.userName()` |
| Update model | `this.checked.set(true)` |

## Resources

- [Official Angular Signals Guide](https://angular.dev/guide/signals)
- [Signal Inputs Documentation](https://angular.dev/guide/signals/inputs)
- [Model Inputs Documentation](https://angular.dev/guide/signals/model)
- [Angular API Reference - input()](https://angular.dev/api/core/input)
- [Angular API Reference - model()](https://angular.dev/api/core/model)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/araujomartin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
