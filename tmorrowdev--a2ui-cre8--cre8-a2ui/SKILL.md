---
name: cre8-a2ui
description: Agent-to-UI schema for CRE8 Web Components (@tmorrow/cre8-wc). Use when building vanilla JS/HTML UIs with CRE8/Innovexa design system using cre8-* custom elements, creating static landing pages, or integrating with non-React frameworks. Triggers on requests mentioning CRE8 web components, cre8-wc, cre8-* elements, Lit components, or vanilla HTML with CRE8 design system. For React projects, use cre8-a2ui-react instead. Use when this capability is needed.
metadata:
  author: tmorrowdev
---

# CRE8 A2UI — Web Components Schema

Web Components (Lit) library for the CRE8 design system. Uses custom elements with `cre8-` prefix.

## System Overview

```yaml
library: "@tmorrow/cre8-wc"
framework: Web Components (Lit)
baseClass: Cre8Element
formBaseClass: Cre8FormElement  
prefix: cre8-
naming: kebab-case (cre8-button, cre8-card, etc.)
```

## Usage

```html
<!-- Include CRE8 Web Components -->
<script type="module" src="@tmorrow/cre8-wc"></script>

<!-- Use components as custom elements -->
<cre8-button text="Submit" variant="primary"></cre8-button>
```

## Component Mapping (Web Components ↔ React)

| Web Component | React Component | Category |
|---------------|-----------------|----------|
| `<cre8-button>` | `<Cre8Button>` | Actions |
| `<cre8-danger-button>` | `<Cre8DangerButton>` | Actions |
| `<cre8-field>` | `<Cre8Field>` | Forms |
| `<cre8-select>` | `<Cre8Select>` | Forms |
| `<cre8-card>` | `<Cre8Card>` | Layout |
| `<cre8-grid>` | `<Cre8Grid>` | Layout |
| `<cre8-heading>` | `<Cre8Heading>` | Typography |
| `<cre8-alert>` | `<Cre8Alert>` | Feedback |
| `<cre8-modal>` | `<Cre8Modal>` | Disclosure |
| `<cre8-table>` | `<Cre8Table>` | Data |

## Quick Patterns

### Page Structure
```html
<cre8-layout>
  <cre8-header>
    <cre8-global-nav>
      <cre8-global-nav-item href="/">Home</cre8-global-nav-item>
    </cre8-global-nav>
  </cre8-header>
  <main>
    <cre8-layout-container>
      <!-- Content -->
    </cre8-layout-container>
  </main>
  <cre8-footer></cre8-footer>
</cre8-layout>
```

### Form
```html
<cre8-card>
  <cre8-heading type="h2">Sign In</cre8-heading>
  <cre8-field label="Email" type="email"></cre8-field>
  <cre8-field label="Password" type="password"></cre8-field>
  <cre8-button text="Sign In" variant="primary" full-width></cre8-button>
</cre8-card>
```

### Hero Section
```html
<cre8-hero>
  <cre8-layout-container>
    <cre8-heading type="h1">Welcome</cre8-heading>
    <cre8-text-passage>
      <p>Build amazing things.</p>
    </cre8-text-passage>
    <cre8-button text="Get Started" variant="primary"></cre8-button>
  </cre8-layout-container>
</cre8-hero>
```

## Web Component Syntax Notes

### Attributes vs Properties
- **Attributes**: Use kebab-case (`full-width`, `error-note`)
- **Events**: Use `@event-name` in Lit or `addEventListener`
- **Boolean attributes**: Present = true, absent = false

```html
<!-- Boolean true -->
<cre8-button text="Save" disabled></cre8-button>

<!-- Boolean false (omit attribute) -->
<cre8-button text="Save"></cre8-button>

<!-- String/enum values -->
<cre8-button text="Cancel" variant="secondary"></cre8-button>
```

### Event Handling
```html
<cre8-button text="Click Me" @click="${handleClick}"></cre8-button>

<!-- Or vanilla JS -->
<script>
  document.querySelector('cre8-button').addEventListener('click', handleClick);
</script>
```

## Reference Files

Component details match the React version with kebab-case naming:

- **[references/tokens.md](references/tokens.md)** — Design token reference
- **[references/form-components.md](references/form-components.md)** — Form elements
- **[references/layout-components.md](references/layout-components.md)** — Layout elements
- **[references/navigation-components.md](references/navigation-components.md)** — Navigation elements
- **[references/feedback-components.md](references/feedback-components.md)** — Feedback elements
- **[references/data-components.md](references/data-components.md)** — Data display elements
- **[references/utility-components.md](references/utility-components.md)** — Utility elements

## When to Use Web Components vs React

**Use Web Components (`cre8-a2ui`) when:**
- Building static HTML pages
- Integrating with vanilla JavaScript
- Using non-React frameworks (Vue, Angular, Svelte)
- Need framework-agnostic components

**Use React (`cre8-a2ui-react`) when:**
- Building React applications
- Need React-specific features (hooks, context)
- Working in a React codebase

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tmorrowdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
