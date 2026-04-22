---
name: migration-review
description: >- Use when this capability is needed.
metadata:
  author: michael0520
---

Review migration completeness between source (old codebase) and target (new codebase).

## Arguments

- `$ARGUMENTS` - Format: `--from <source_path> --to <target_path>`
  - `--from`: Source path in old project (e.g., `/Users/jayden/f2e-networking-jayden/apps/mxsecurity-web/src/app/pages/account`)
  - `--to`: Target path in new project (e.g., `libs/mxsecurity/account-page`)

## Review Process

### Step 0: Switch to Feature Worktree

Before starting, switch to the feature's worktree:

```bash
cd /Users/jayden/one-ui-mxsecurity
```

### Step 1: Read All Files

Read all `.ts` and `.html` files from both `--from` and `--to` directories.

### Step 2: Extract and Compare Form Controls

**Reference:** `form-extraction skill`

```bash
# From HTML
grep -oE 'formControlName="[^"]+"' {path}/**/*.html | sort -u
grep -oE 'formGroupName="[^"]+"' {path}/**/*.html | sort -u

# From TypeScript
grep -E '(this\.fb\.group|this\.#fb\.group|new FormGroup|new UntypedFormGroup)' {path}/**/*.ts
```

Compare: List all form controls from source that are missing in target.

### Step 3: Extract and Compare Form Validators

**Reference:** `form-extraction skill`

```bash
# Source (old) - Angular Validators
grep -oE 'Validators\.(required|email|minLength|maxLength|min|max|pattern)(\([^)]*\))?' {from}/**/*.ts | sort -u

# Target (new) - OneValidators
grep -oE 'OneValidators\.[a-zA-Z]+(\([^)]*\))?' {to}/**/*.ts | sort -u

# Custom validators
grep -oE '#?[a-zA-Z]+Validator\b' {path}/**/*.ts | sort -u
```

Compare: For each form control, verify all validators from source exist in target.

**Validator Mapping:**

| Old (Validators) | New (OneValidators) |
| ---------------- | ------------------- |
| `Validators.required` | `OneValidators.required` |
| `Validators.email` | `OneValidators.email` |
| `Validators.minLength(n)` | `OneValidators.minLength(n)` |
| `Validators.maxLength(n)` | `OneValidators.maxLength(n)` |
| `Validators.min(n)` + `Validators.max(m)` | `OneValidators.range(n, m)` |
| `Validators.pattern(x)` | `OneValidators.pattern(x)` |

For detailed patterns, see `rules/tools/forms/patterns.md`.

### Step 4: Extract and Compare HTML Keys

**From HTML files, extract and compare:**

1. **CSS Classes** (functional classes only, skip styling classes)
2. **Angular Directives** - `*ngIf` / `@if`, `*ngFor` / `@for`, `[ngClass]`, `[ngStyle]`
3. **Material Components** - All `<mat-xxx>` components used
4. **Event Bindings** - All `(click)`, `(submit)`, `(change)` etc.
5. **Property Bindings** - All `[disabled]`, `[hidden]`, `[value]` etc.
6. **Translation Keys** - Source: `{{ 'xxx' | translate }}`, Target: `{{ t('xxx') }}`

### Step 5: UI Guidelines Review

Check target files for compliance:

1. **Button Types (CRITICAL - Auto-fix)** - `mat-raised-button` -> `mat-flat-button`, search and replace all
2. **Page Layout Structure (CRITICAL)** - Root wrapper MUST have `class="gl-page-content"`. Content wrapped in `class="content-wrapper"`. `<one-ui-breadcrumb />` first. `<mx-page-title>` uses `[title]` input binding. Use `<div *transloco="let t" class="gl-page-content">` NOT `<ng-container>`
3. **Dialog Configuration** - Use shared dialog config (`smallDialogConfig`, `mediumDialogConfig`, etc.). No custom `min-width` in SCSS. No `panelClass` for sizing
4. **Table Toolbar** - Action buttons use `#rightToolbarTemplate`. Icons use `svgIcon="icon:xxx"` format. Include `data-testid` attributes
5. **Translation** - Use `transloco` (not `translate` pipe). Use `*transloco="let t"` pattern
6. **Form Error Display** (Reference: `form-extraction skill`) - `required`, `minLength`, `maxLength`, `range`, `rangeLength`, `email` -> MUST use `<mat-error oneUiFormError="field">`. All other validators -> MUST use `@if/@else` with custom message
7. **Special Input Fields** - Password fields -> `<mx-password-input>`. Number-only fields -> `oneUiNumberOnly` directive
8. **Tab Groups** - Must use `mxTabGroup` directive: `<mat-tab-group mxTabGroup animationDuration="0ms">`

Auto-fix: If `mat-raised-button` is found, automatically replace with `mat-flat-button`.

### Step 6: Generate Report

```markdown
# Migration Review Report

**Source:** {from_path}
**Target:** {to_path}
**Date:** {current_date}

## Summary

| Category            | Source | Target | Missing | Completeness |
| ------------------- | ------ | ------ | ------- | ------------ |
| Form Controls       | X      | Y      | Z       | XX%          |
| Form Validators     | X      | Y      | Z       | XX%          |
| Material Components | X      | Y      | Z       | XX%          |
| Event Bindings      | X      | Y      | Z       | XX%          |
| Translation Keys    | X      | Y      | Z       | XX%          |
| UI Guidelines       | -      | X      | Y       | XX%          |

**Overall Completeness: XX%**

## UI Guidelines Compliance

| Rule                  | Status     | Notes                    |
| --------------------- | ---------- | ------------------------ |
| mat-flat-button       | OK/FIXED   | Auto-fixed X occurrences |
| Page Layout Structure | OK/MISSING | Details...               |
| Dialog Config         | OK/MISSING | Details...               |
| Table Toolbar         | OK/MISSING | Details...               |
| Translation Pattern   | OK/MISSING | Details...               |
| Form Error Display    | OK/MISSING | Non-basic validators use @if/@else? |
| Password Fields       | OK/MISSING | Using mx-password-input? |
| Number Input          | OK/MISSING | Using oneUiNumberOnly? |
| Tab Groups            | OK/MISSING | Using mxTabGroup? |

## Critical Missing Items

### Form Controls (CRITICAL)

- [ ] `controlName` - not found in target

### Form Validators (CRITICAL)

- [ ] `controlName: Validators.required` - not found in target

## Warnings

- Source uses `*ngIf` but target should use `@if`
- Source uses `*ngFor` but target should use `@for`

## Detailed Comparison

### Form Controls

| Control Name | In Source | In Target | Status  |
| ------------ | --------- | --------- | ------- |
| username     | Yes       | Yes       | OK      |
| password     | Yes       | No        | MISSING |

### Form Validators

| Control  | Validator | In Source | In Target | Status  |
| -------- | --------- | --------- | --------- | ------- |
| username | required  | Yes       | Yes       | OK      |
| username | maxLength | Yes       | No        | MISSING |

### Material Components

...
```

## Focus Areas

1. **Form Validation Completeness** - CRITICAL. Every validator in source must exist in target. Check custom validators and group-level validators. Error display: only `required`, `minLength`, `maxLength`, `range`, `rangeLength`, `email` use `oneUiFormError`; all others MUST use `@if/@else`
2. **HTML Key Migration** - CRITICAL. Every `formControlName` must exist. Every event binding must be migrated
3. **UI Guidelines Compliance** - CRITICAL (Auto-fix). `mat-raised-button` -> `mat-flat-button`. Page layout structure. Dialog config. Table toolbar patterns
4. **Syntax Modernization** (Warnings only) - `*ngIf` -> `@if`, `*ngFor` -> `@for`, `Validators.*` -> `OneValidators.*`, `appNumberOnly` -> `oneUiNumberOnly`, Password manual toggle -> `mx-password-input`

## Step 7: Generate MIGRATION-ANALYSIS.md (REQUIRED)

Always generate the migration analysis document after completing the review.

**Location:** `{target}/domain/src/lib/docs/MIGRATION-ANALYSIS.md`

**Required Sections:**
1. Overview (source, target, date, status)
2. Migration Summary table
3. UI Guidelines Compliance table
4. File Structure
5. Detailed Comparison (form controls, validators, translation keys, API endpoints)
6. DDD Architecture Compliance
7. Syntax Modernization
8. Issues Fixed During Migration
9. Notes

```bash
mkdir -p {target}/domain/src/lib/docs
```

Then create the file with full migration analysis content.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michael0520) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
