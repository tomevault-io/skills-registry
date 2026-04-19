---
name: cre8-a2ui-react
description: Agent-to-UI schema for CRE8 React Components (@tmorrow/cre8-react). Use when building React UIs with CRE8/Innovexa design system, generating React pages or applications using Cre8* components, creating landing pages, dashboards, forms, or any React UI with the CRE8 component library. Triggers on requests mentioning CRE8 React, @tmorrow/cre8-react, Innovexa React components, or building React UI with the CRE8 design system. Provides 72 React components with props, patterns, and usage examples. Use when this capability is needed.
metadata:
  author: tmorrowdev
---

# CRE8 A2UI React — Agent-to-UI Schema

React component library for the CRE8 design system. 72 components organized by category.

## System Overview

```yaml
library: "@tmorrow/cre8-react"
version: "1.0.0"
framework: React
componentCount: 72
naming: PascalCase (Cre8Button, Cre8Card, etc.)
```

## Component Categories

| Category | Count | Key Components |
|----------|-------|----------------|
| Actions | 2 | Cre8Button, Cre8DangerButton |
| Forms | 10 | Cre8Field, Cre8Select, Cre8CheckboxField, Cre8RadioField, Cre8DatePicker |
| Layout | 10 | Cre8Layout, Cre8Card, Cre8Grid, Cre8Section, Cre8Hero, Cre8Band |
| Typography | 3 | Cre8Heading, Cre8TextPassage, Cre8TextLink |
| Navigation | 16 | Cre8Header, Cre8Footer, Cre8GlobalNav, Cre8PrimaryNav, Cre8Tabs, Cre8Breadcrumbs |
| Disclosure | 7 | Cre8Accordion, Cre8Modal, Cre8Dropdown, Cre8Popover, Cre8Tooltip |
| Feedback | 7 | Cre8Alert, Cre8Badge, Cre8LoadingSpinner, Cre8ProgressMeter |
| Data | 14 | Cre8Table, Cre8List, Cre8LinkList, Cre8Tag, Cre8Chart |
| Media | 2 | Cre8Icon, Cre8Logo |
| Marketing | 2 | Cre8Feature, Cre8PageHeader |

## Quick Patterns

### Page Layout
```jsx
<Cre8Layout>
  <Cre8Header>
    <Cre8GlobalNav>
      <Cre8GlobalNavItem href="/">Home</Cre8GlobalNavItem>
    </Cre8GlobalNav>
  </Cre8Header>
  <main>
    <Cre8LayoutContainer>
      {/* Page content */}
    </Cre8LayoutContainer>
  </main>
  <Cre8Footer />
</Cre8Layout>
```

### Login Form
```jsx
<Cre8Card>
  <Cre8Heading type="h2">Sign In</Cre8Heading>
  <Cre8Field label="Email" type="email" />
  <Cre8Field label="Password" type="password" />
  <Cre8Button text="Sign In" variant="primary" fullWidth />
</Cre8Card>
```

### Data Table
```jsx
<Cre8Table>
  <Cre8TableHeader>
    <Cre8TableRow>
      <Cre8TableHeaderCell>Name</Cre8TableHeaderCell>
      <Cre8TableHeaderCell>Status</Cre8TableHeaderCell>
    </Cre8TableRow>
  </Cre8TableHeader>
  <Cre8TableBody>
    <Cre8TableRow>
      <Cre8TableCell>Item 1</Cre8TableCell>
      <Cre8TableCell><Cre8Badge text="Active" /></Cre8TableCell>
    </Cre8TableRow>
  </Cre8TableBody>
</Cre8Table>
```

### Tabbed Content
```jsx
<Cre8Tabs>
  <Cre8Tab label="Tab 1" selected />
  <Cre8Tab label="Tab 2" />
  <Cre8TabPanel>Content for Tab 1</Cre8TabPanel>
  <Cre8TabPanel>Content for Tab 2</Cre8TabPanel>
</Cre8Tabs>
```

## Reference Files

Load component details as needed:

- **[references/actions.md](references/actions.md)** — Cre8Button, Cre8DangerButton
- **[references/forms.md](references/forms.md)** — Field, Select, Checkbox, Radio, DatePicker
- **[references/layout.md](references/layout.md)** — Layout, Card, Grid, Section, Hero, Band
- **[references/navigation.md](references/navigation.md)** — Header, Footer, Nav components, Tabs, Breadcrumbs
- **[references/disclosure.md](references/disclosure.md)** — Accordion, Modal, Dropdown, Popover, Tooltip
- **[references/feedback.md](references/feedback.md)** — Alert, Badge, LoadingSpinner, Progress
- **[references/data.md](references/data.md)** — Table, List, LinkList, Tag, Chart

## AI Implementation Rules

### Buttons
- Use `variant="primary"` for main CTA (one per view)
- Use `variant="secondary"` for secondary actions
- Use `Cre8DangerButton` only for destructive actions
- Keep text short: max 3 words, Title Case

### Forms
- Always provide `label` for form fields
- Use appropriate `type` for Cre8Field (email, password, tel, etc.)
- Group related checkboxes in `Cre8CheckboxField`
- Group radio buttons in `Cre8RadioField` with shared `name`

### Layout
- Wrap pages in `Cre8Layout`
- Use `Cre8LayoutContainer` for max-width content
- Use `Cre8Card` to group related content
- Use `Cre8Grid` + `Cre8GridItem` for grid layouts

### Navigation
- Use `Cre8Header` with `Cre8GlobalNav` for site header
- Use `Cre8PrimaryNav` for main navigation
- Use `Cre8Breadcrumbs` for hierarchical navigation
- Use `Cre8Tabs` for content switching

### Feedback
- Use `status` prop: "info", "success", "warning", "error"
- Use `Cre8Alert` for page-level messages
- Use `Cre8InlineAlert` for contextual messages
- Use `Cre8Badge` for status indicators

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tmorrowdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
