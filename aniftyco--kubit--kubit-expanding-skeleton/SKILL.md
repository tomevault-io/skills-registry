---
name: kubit-expanding-skeleton
description: Use when adding new files, patterns, or examples to skeleton/. Use when creating new controller, model, job, mailable, migration, view, or component in the skeleton app.
metadata:
  author: aniftyco
---

# Expanding the Kubit Skeleton

## Overview

The skeleton/ directory is the ideal Kubit app. Add patterns here first, then add types to make them compile.

## When to Use

- Adding new file to skeleton/ (controller, model, etc.)
- Adding new pattern or convention
- Demonstrating new framework feature
- User asks to show how something would work

## Directory Structure

```
skeleton/
├── app/
│   ├── routes.ts           # Route definitions
│   ├── controllers/        # Class-based controllers
│   ├── models/             # ORM models with decorators
│   ├── jobs/               # Job classes
│   └── mail/               # Mailable classes
├── components/             # Shared React components
├── views/                  # React page components (SSR)
├── config/                 # Runtime configuration
├── database/migrations/    # Migration classes
├── public/                 # Static assets
└── tests/                  # Test files
```

## Conventions by File Type

**Controllers:** Class with async methods, receive HttpContext, return view() or values
```typescript
export default class FooController {
  public async index({ request, response }: HttpContext) {
    return view('foo/index', { data });
  }
}
```

**Models:** Class extending Model with @column decorators
```typescript
@use(SoftDeletes)
export class Foo extends Model {
  @column({ primary: true }) public id: number;
  @column() public name: string;
  @column.dateTime({ autoCreate: true }) public createdAt: DateTime;
}
```

**Jobs:** Class extending Job with @property and handle()
```typescript
export class FooJob extends Job {
  @property() public data: string;
  async handle() { /* ... */ }
}
```

**Mailables:** Class extending Mailable with view rendering
```typescript
export class FooMail extends Mailable {
  @property() public name: string;
  async handle() { return this.view('emails.foo', { name: this.name }); }
}
```

**Migrations:** Class extending Migration with up/down
```typescript
export default class extends Migration {
  async up() { return schema.createTable('foos', (t) => { /* ... */ }); }
  async down() { return schema.dropTableIfExists('foos'); }
}
```

**Views:** React FC with typed props, default export
```typescript
const Foo: FC<{ data: string }> = ({ data }) => <div>{data}</div>;
export default Foo;
```

**Components:** React FC with named export
```typescript
export const Button: FC<Props> = ({ children }) => <button>{children}</button>;
```

## Import Conventions

| Import | Source |
|--------|--------|
| `kubit` | Core: `defineConfig`, `env`, `use` |
| `kubit:router` | `router` |
| `kubit:inertia` | `view` |
| `kubit:server` | `HttpContext` |
| `kubit:orm` | `Model`, decorators, relations |
| `kubit:db` | `Migration`, `schema` |
| `kubit:queue` | `Job`, `property` |
| `kubit:mail` | `Mailable` |
| `@app/*` | App-level imports |

## Checklist When Adding to Skeleton

- [ ] Follows existing conventions (class-based, decorators)
- [ ] Uses correct import paths
- [ ] Types exist in packages/core/ (add if needed)
- [ ] `tsc --noEmit` passes
- [ ] Update SKELETON_APP.md if structure changes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aniftyco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
