---
name: zds-design-system
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Zelis Design System (ZDS) Standards

## Core Principles

- **MUST** use Bootstrap 5 as the foundation
- **MUST** follow simplicity-first approach
- **MUST** ensure WCAG 2.1 AA accessibility compliance
- **MUST** use ZDS-approved component patterns
- **MUST** use compact/small variants by default

## Button Standards

- **MUST** use `btn-sm` size for all buttons
- **MUST** use `type="button"` for non-submit buttons
- **MUST** add `aria-label` for icon-only buttons

| Action Type | Classes |
|-------------|---------|
| Primary | `btn btn-primary btn-sm` |
| Secondary | `btn btn-outline-secondary btn-sm` |
| Success | `btn btn-success btn-sm` |
| Danger | `btn btn-danger btn-sm` |
| Link | `btn btn-link btn-sm` |

```html
<!-- Standard buttons -->
<button type="button" class="btn btn-primary btn-sm">Save</button>
<button type="button" class="btn btn-outline-secondary btn-sm">Cancel</button>

<!-- Button group -->
<div class="d-flex gap-2">
    <button type="button" class="btn btn-primary btn-sm">Save</button>
    <button type="button" class="btn btn-outline-secondary btn-sm">Cancel</button>
</div>

<!-- Button with icon -->
<button type="button" class="btn btn-primary btn-sm">
    <i class="fas fa-plus me-2"></i>Add Item
</button>
```

## Card Standards

- **MUST** use standard card structure with `card-body`
- **MUST NOT** use `shadow-sm` on cards (ZDS uses borders, not shadows)
- **MUST NOT** use `border-0` on cards (keep default border)
- **MUST NOT** use `fw-semibold` or `fw-bold` on `card-title`
- **MUST NOT** use custom `py-*` `px-*` on `card-body`

```html
<!-- Standard card -->
<div class="card">
    <div class="card-header bg-light">Section Title</div>
    <div class="card-body">
        <h5 class="card-title">Card Title</h5>
        <p class="card-text">Content goes here.</p>
    </div>
    <div class="card-footer text-muted">Footer</div>
</div>

<!-- Card grid -->
<div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-3">
    <div class="col">
        <div class="card h-100">
            <div class="card-body">Content</div>
        </div>
    </div>
</div>
```

## Form Controls

- **MUST** use `form-control-sm` for all inputs
- **MUST** use `form-select-sm` for all selects
- **MUST** use `small` class for labels (not `fw-bold`)
- **SHOULD** use `form-label` for labels
- **SHOULD** use `mb-3` for form group margins

```html
<!-- Text input -->
<div class="mb-3">
    <label for="nameInput" class="small">Name</label>
    <input type="text" class="form-control form-control-sm" id="nameInput" />
</div>

<!-- Select -->
<div class="mb-3">
    <label for="statusSelect" class="small">Status</label>
    <select class="form-select form-select-sm" id="statusSelect">
        <option value="">Select...</option>
        <option value="1">Active</option>
        <option value="2">Inactive</option>
    </select>
</div>

<!-- Form row -->
<div class="row">
    <div class="col-md-6 mb-3">
        <label class="small">First Name</label>
        <input type="text" class="form-control form-control-sm" />
    </div>
    <div class="col-md-6 mb-3">
        <label class="small">Last Name</label>
        <input type="text" class="form-control form-control-sm" />
    </div>
</div>

<!-- Checkbox -->
<div class="form-check">
    <input class="form-check-input" type="checkbox" id="activeCheck" />
    <label class="form-check-label" for="activeCheck">Active</label>
</div>

<!-- Input with validation -->
<div class="mb-3">
    <label class="small">Email</label>
    <input type="email" class="form-control form-control-sm is-invalid" />
    <div class="invalid-feedback">Please enter a valid email.</div>
</div>
```

## Table Standards

- **MUST** wrap tables in `<div class="table-responsive">`
- **SHOULD** use `table-hover` for interactive tables
- **SHOULD** use `table-light` for header background

```html
<div class="table-responsive">
    <table class="table table-hover">
        <thead class="table-light">
            <tr>
                <th scope="col">Name</th>
                <th scope="col">Status</th>
                <th scope="col">Actions</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>John Doe</td>
                <td><span class="badge text-bg-success">Active</span></td>
                <td>
                    <button type="button" class="btn btn-outline-secondary btn-sm">Edit</button>
                </td>
            </tr>
        </tbody>
    </table>
</div>
```

## Modal Standards

- **SHOULD** use `modal-dialog-centered` for vertical centering
- **MUST** include proper ARIA attributes

```html
<div class="modal fade" id="exampleModal" tabindex="-1"
     aria-labelledby="exampleModalLabel" aria-hidden="true">
    <div class="modal-dialog modal-dialog-centered">
        <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="exampleModalLabel">Modal Title</h5>
                <button type="button" class="btn-close" data-bs-dismiss="modal"
                        aria-label="Close"></button>
            </div>
            <div class="modal-body">
                Modal content here.
            </div>
            <div class="modal-footer">
                <button type="button" class="btn btn-outline-secondary btn-sm"
                        data-bs-dismiss="modal">Cancel</button>
                <button type="button" class="btn btn-primary btn-sm">Save</button>
            </div>
        </div>
    </div>
</div>
```

## Color Palette

- **MUST** use semantic color classes
- **MUST NOT** use brand colors for badges (use semantic colors only)
- **SHOULD** use `text-bg-*` helpers for automatic contrast

| Type | Classes |
|------|---------|
| Background | `bg-primary`, `bg-secondary`, `bg-success`, `bg-danger`, `bg-warning`, `bg-info`, `bg-light`, `bg-dark` |
| Text | `text-primary`, `text-secondary`, `text-success`, `text-danger`, `text-muted`, `text-white` |
| Combined | `text-bg-primary`, `text-bg-secondary`, `text-bg-success`, `text-bg-danger` |
| Custom | `bg-ink-blue-gradient` (header gradient) |

## Typography

- **MUST** use semantic heading tags (`h1`-`h6`)
- **SHOULD** use `<small>` for secondary labels
- **MUST NOT** use `fw-bold` for labels (use `<small>` instead)

```html
<h5 class="mb-3">Section Title</h5>
<label class="form-label">Field Label</label>
<small class="text-muted">Helper text</small>
```

## Grid and Layout

- **SHOULD** prefer `col-auto` with `flex-wrap` for flexible layouts
- **MUST** use `gap-*` utilities for spacing between flex items
- **SHOULD** use `g-3` as standard gutter size

```html
<!-- Flexible auto-width columns -->
<div class="row g-2 flex-wrap">
    <div class="col-auto">Item 1</div>
    <div class="col-auto">Item 2</div>
    <div class="col-auto">Item 3</div>
</div>

<!-- Responsive columns -->
<div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-3">
    <div class="col">Item 1</div>
    <div class="col">Item 2</div>
    <div class="col">Item 3</div>
</div>
```

## Spacing

- **MUST** use Bootstrap spacing scale (0-5 plus auto)
- **SHOULD** use `px-3 py-2` for compact toolbar padding
- **SHOULD** use `mb-3` for form group margins
- **SHOULD** use `gap-2` for button groups

| Scale | Size |
|-------|------|
| `*-0` | 0 |
| `*-1` | 0.25rem (4px) |
| `*-2` | 0.5rem (8px) |
| `*-3` | 1rem (16px) |
| `*-4` | 1.5rem (24px) |
| `*-5` | 3rem (48px) |

## Accessibility

- **MUST** include `aria-label` on icon-only buttons
- **MUST** support keyboard navigation
- **MUST** maintain color contrast compliance
- **SHOULD** use `role` attributes where semantic HTML is insufficient

```html
<!-- Icon-only button with aria-label -->
<button type="button" class="btn btn-outline-secondary btn-sm" aria-label="Delete">
    <i class="fas fa-trash"></i>
</button>

<!-- Accessible modal trigger -->
<button type="button" class="btn btn-primary btn-sm"
        data-bs-toggle="modal" data-bs-target="#editModal"
        aria-haspopup="dialog">
    Edit
</button>
```

## ZDS Restrictions Summary

### MUST Use

- `btn-sm` for all buttons
- `form-control-sm` for all inputs
- `form-select-sm` for all selects
- `table-responsive` wrapper for tables
- Semantic color classes

### MUST NOT Use

- `shadow-sm` on cards
- `border-0` on cards
- `fw-semibold` or `fw-bold` on card titles
- Custom padding on card-body
- Brand colors for badges

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
