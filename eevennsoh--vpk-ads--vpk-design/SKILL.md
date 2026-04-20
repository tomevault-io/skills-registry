---
name: vpk-design
description: >- Use when this capability is needed.
metadata:
  author: eevennsoh
---

# Atlassian Design System Quick Reference

> Essential patterns for using ADS. For detailed references, see the `references/` directory.

## Core Principle: ADS First, Always

**Default Approach**: Always use Atlassian Design System (@atlaskit) components, tokens,
and icons FIRST before considering alternatives.

**Priority Order:**

1. Design tokens first → Tailwind classes for missing tokens → inline styles as last resort
2. ADS components first → shadcn for missing → custom components as a last resort
3. ADS icons first → lucide-react if not found in ADS

## References

For comprehensive documentation, see these reference files:

| File                                  | Content                                                                 |
| ------------------------------------- | ----------------------------------------------------------------------- |
| `references/tokens.md`                | Complete token tables (colors, spacing, typography, elevation, borders) |
| `references/components.md`            | All component APIs with props and examples                              |
| `references/primitives.md`            | Box, Stack, Inline, Grid, Text, Pressable, Anchor                       |
| `references/styling.md`               | Styling patterns with design tokens                                     |
| `references/content-standards.md`     | Voice, tone, accessibility, inclusive language                          |
| `references/guidelines.md`            | Component guidelines and decision trees                                 |
| `references/examples.md`              | Code examples for common patterns                                       |
| `references/search.md`                | How to find ADS components, icons, tokens                               |
| `references/implement-figma-design.md`| Figma-to-code workflow, MCP tools, and Tailwind-to-ADS translation      |
| `references/visual-testing.md`        | Visual testing with /agent-browser for theme and Figma validation       |
| `references/verification.md`          | ADS compliance verification workflow and checklists                     |
| `references/custom-components.md`     | VPK custom component wrappers (CustomTooltip, etc.)                     |
| `references/dropdown-positioning.md`  | ADS DropdownMenu parent-relative positioning pattern                    |

---

## Quick Start

```tsx
import { token } from "@atlaskit/tokens";
import { Stack, Inline, Box, Text } from "@atlaskit/primitives";
import Button from "@atlaskit/button/new";
import Heading from "@atlaskit/heading";
```

---

## Typography Patterns

### Headings

**NEVER use** native HTML heading elements like `<h1>`, `<h2>`, etc.
**Always use** the ADS Heading component:

```tsx
import Heading from '@atlaskit/heading';

<Heading size="xxlarge">Page title</Heading>
<Heading size="large">Section title</Heading>
<Heading size="medium">Subsection</Heading>
```

### Text

**NEVER use** native HTML elements like `<p>`, `<span>`, `<div>` for text.
**Always use** the ADS Text primitive:

```tsx
import { Text } from '@atlaskit/primitives';

<Text>Regular text</Text>
<Text weight="bold">Bold text</Text>
<Text size="small" color="color.text.subtle">Secondary info</Text>
```

### Composite Font Tokens

Use composite tokens that set size, weight, line-height, and family in one declaration:

```tsx
<div style={{ font: token('font.body') }}>Default text (14px/400/20px)</div>
<div style={{ font: token('font.body.small') }}>Small text (12px/400/16px)</div>
<h2 style={{ font: token('font.heading.large') }}>Page title (24px/500/28px)</h2>
```

| Token                  | Size | Weight | Line Height |
| ---------------------- | ---- | ------ | ----------- |
| `font.heading.xxlarge` | 35px | 500    | 40px        |
| `font.heading.xlarge`  | 29px | 500    | 32px        |
| `font.heading.large`   | 24px | 500    | 28px        |
| `font.heading.medium`  | 20px | 500    | 24px        |
| `font.heading.small`   | 16px | 600    | 20px        |
| `font.body.large`      | 16px | 400    | 24px        |
| `font.body`            | 14px | 400    | 20px        |
| `font.body.small`      | 12px | 400    | 16px        |

---

## Icon Usage

Icons come from two packages: `@atlaskit/icon` and `@atlaskit/icon-lab`.

### Import Pattern

```tsx
// Pattern: import {PascalCase}Icon from '@atlaskit/{icon|icon-lab}/core/{kebab-case}'
import AddIcon from '@atlaskit/icon/core/add';
import EditIcon from '@atlaskit/icon/core/edit';
import SearchIcon from '@atlaskit/icon/core/search';

// Usage - label is REQUIRED
<AddIcon label="Add item" />
<AddIcon label="Add" size="small" />  // 16px
<AddIcon label="Add" size="medium" /> // 24px (default)
<AddIcon label="Add" size="large" />  // 32px
```

### Common Icon Mappings

| ❌ Wrong  | ✅ Correct                         |
| --------- | ---------------------------------- |
| `folder`  | `folder-closed` or `folder-open`   |
| `user`    | `person`                           |
| `play`    | `video-play`                       |
| `arrow`   | `arrow-right`, `arrow-left`, etc.  |
| `chevron` | `chevron-down`, `chevron-up`, etc. |

**NEVER:**

- Import from `@atlaskit/icon/glyph/` (deprecated)
- Invent or guess icon names
- Use icons not verified to exist

---

## Common Components

### Button

```tsx
import Button from '@atlaskit/button/new';
import { IconButton } from '@atlaskit/button/new';

<Button appearance="primary" iconBefore={AddIcon}>Create</Button>
<Button appearance="subtle">Cancel</Button>
<IconButton icon={EditIcon} label="Edit" appearance="subtle" />
```

**Appearances:** `'default' | 'primary' | 'subtle' | 'warning' | 'danger' | 'discovery'`

### Form Inputs

```tsx
import TextField from '@atlaskit/textfield';
import TextArea from '@atlaskit/textarea';
import Checkbox from '@atlaskit/checkbox';
import { Radio } from '@atlaskit/radio';
import Select from '@atlaskit/select';
import Toggle from '@atlaskit/toggle';

<TextField value={value} onChange={handleChange} />
<Checkbox label="Accept terms" isChecked={checked} onChange={handleCheck} />
<Select options={[{ label: 'Option 1', value: '1' }]} onChange={handleSelect} />
<Toggle isChecked={enabled} onChange={handleToggle} />
```

### Status Indicators

```tsx
import Lozenge from '@atlaskit/lozenge';
import Badge from '@atlaskit/badge';

<Lozenge appearance="success">Done</Lozenge>
<Lozenge appearance="inprogress">In progress</Lozenge>
<Badge appearance="primary">{count}</Badge>
```

---

## Layout Primitives

Use `@atlaskit/primitives` (not `@atlaskit/primitives/compiled`)

```tsx
import { Box, Stack, Inline, Flex, Grid } from '@atlaskit/primitives';

// Vertical stacking
<Stack space="space.200">
  <div>Item 1</div>
  <div>Item 2</div>
</Stack>

// Horizontal layout
<Inline space="space.100" alignBlock="center">
  <Avatar />
  <Text>Name</Text>
</Inline>

// Flexible layout
<Flex gap="space.200" justifyContent="space-between">
  <div>Left</div>
  <div>Right</div>
</Flex>

// Box with background
<Box backgroundColor="elevation.surface.raised">
  Content
</Box>
```

---

## Token Patterns

### Spacing

**NEVER use** pixel or rem values for padding, margin, or gap.
**Always use** space tokens:

```tsx
padding: token("space.100"); // 8px
padding: token("space.200"); // 16px
padding: token("space.300"); // 24px
gap: token("space.150"); // 12px
margin: token("space.050"); // 4px
```

### Colors

```tsx
// Text colors
color: token("color.text"); // Primary text
color: token("color.text.subtle"); // Secondary text
color: token("color.text.subtlest"); // Tertiary text

// Background colors
backgroundColor: token("elevation.surface"); // Default surface
backgroundColor: token("color.background.neutral"); // Neutral
backgroundColor: token("color.background.brand.bold"); // Brand
backgroundColor: token("color.background.selected"); // Selected state

// Border colors
border: `${token("border.width")} solid ${token("color.border")}`;
```

### Interactive States

Use primitives with built-in token props, or inline styles with `token()`:

```tsx
// Using Box with backgroundColor prop (preferred)
<Box backgroundColor="color.background.neutral">Content</Box>

// For hover/active states, use Pressable or Button components
import { Pressable } from "@atlaskit/primitives";

<Pressable onClick={handleClick}>
  Interactive content
</Pressable>
```

---

## Content Writing Guidelines

### Capitalization

**ALWAYS use sentence case** for all UI text:

- ✅ "Project settings" NOT "Project Settings"
- ✅ "Email notifications" NOT "Email Notifications"

### Language

- Use contractions ("can't", "don't", "you'll")
- Use US English spelling ("color" not "colour")
- Use active voice
- Use "you" and "your"
- Use numerals for quantities ("3 projects" not "three projects")

### Buttons

Use sentence case with verb + noun pattern:

- ✅ "Save changes", "Delete project", "Send message"
- ❌ "Message", "Send"

---

## Design Verification

Use this skill to verify existing UI code follows ADS patterns.

### Verification Modes

**Single component:**
```
"verify this component follows ADS patterns"
"check tokens in SearchModal.tsx"
```

**Directory scan:**
```
"audit components/blocks/jira/ for ADS compliance"
"check all components in the search folder"
```

### What Gets Verified

1. **Token Compliance** - No hardcoded colors, spacing, fonts
2. **Component Usage** - ADS components over native HTML
3. **Accessibility** - Icon labels, form labels, keyboard access
4. **Content Standards** - Sentence case, contractions, vocabulary

### Verification Report Format

Reports include pass/fail status per category:

```
## ADS Compliance Report: [target]

### Token Compliance: ✓ PASS / ✗ FAIL
### Component Usage: ✓ PASS / ✗ FAIL
### Accessibility: ✓ PASS / ✗ FAIL
### Content Standards: ✓ PASS / ✗ FAIL

### Summary: X of 4 checks passed
```

For comprehensive verification documentation, see `references/verification.md`.

---

## Best Practices Checklist

- [ ] Using design tokens (not hardcoded colors, spacing, or fonts)
- [ ] Using ADS components (Button, TextField, Lozenge, etc.)
- [ ] Using primitives for layout (Stack, Inline, Box, Flex)
- [ ] Using Heading and Text components (not raw HTML elements)
- [ ] Importing from `@atlaskit/primitives` paths
- [ ] Icons have required `label` prop
- [ ] Following sentence case for UI text

---

## Custom Components

VPK provides custom wrappers for certain Atlaskit components. **Always check for a custom
component before importing Atlaskit directly.**

### Available Custom Components

| Component | Import | Purpose |
|-----------|--------|---------|
| `CustomTooltip` | `@/components/ui/custom-tooltip` | Tooltip with VPK defaults |
| `FooterDisclaimer` | `@/components/ui/footer-disclaimer` | AI disclaimer footer |
| `LivePageIcon` | `@/components/ui/icon-livepage` | Custom LivePage icon |

### Priority Order (Updated)

1. **Custom components first** (check `references/custom-components.md`)
2. ADS components (Button, TextField, etc.)
3. shadcn for missing components
4. Custom as last resort

See `references/custom-components.md` for full documentation.

---

## Figma Implementation Best Practices

When implementing designs from Figma, follow these principles:

### Always Start with Context

Never implement based on assumptions. Always fetch `get_design_context` and `get_screenshot` first to understand the exact design specifications.

### Incremental Validation

Validate frequently during implementation, not just at the end. Use `/agent-browser` to check progress after completing each major section.

### Document Deviations

If you must deviate from the Figma design (for accessibility, technical constraints, or ADS component limitations), document why in code comments.

### Reuse Over Recreation

Always check for existing ADS components before creating new ones. Consistency with the design system is more important than exact Figma replication.

### ADS First

When translating Figma output, prefer ADS design system patterns over literal Tailwind-to-code translation. ADS components handle accessibility, theming, and interaction states automatically.

---

## Visual Testing

After implementing components, validate visually using the `/agent-browser` skill.

> **Note:** `/agent-browser` is a **skill** (not a subagent). It uses the Playwright MCP server for browser automation. Invoke it using the `Skill` tool.

### Finding the Port

The dev server uses dynamic port allocation. Check `.dev-frontend-port` for the actual port:

```bash
cat .dev-frontend-port  # Returns the port number (e.g., 3000, 3001, etc.)
```

### How to Use

Invoke the skill and describe your testing needs in natural language:

```
/agent-browser
"Take screenshots of http://localhost:<port>/jira in both light and dark mode"
```

Replace `<port>` with the value from `.dev-frontend-port`.

### Example Prompts

**Theme testing:**
```
"Navigate to http://localhost:<port>/jira, set localStorage 'ui-theme' to 'light',
take a screenshot, then switch to dark mode and take another screenshot"
```

**Figma comparison:**
```
"Take a screenshot of http://localhost:<port>/confluence in light mode"
```

**Responsive testing:**
```
"Test http://localhost:<port>/jira at desktop (1440x900), tablet (768x1024),
and mobile (375x812) sizes"
```

### Route Mapping

| Block Location                  | Test URL                            |
| ------------------------------- | ----------------------------------- |
| `components/blocks/jira/`       | `http://localhost:<port>/jira`       |
| `components/blocks/confluence/` | `http://localhost:<port>/confluence` |
| `components/blocks/rovo/`       | `http://localhost:<port>/rovo`       |
| `components/blocks/search/`     | `http://localhost:<port>/search`     |
| `components/blocks/widget/`     | `http://localhost:<port>/widgets`    |

See `references/visual-testing.md` for comprehensive visual testing documentation.

---

## Documentation Links

- [ADS Component Library](https://atlassian.design/components)
- [Icon Explorer](https://atlassian.design/components/icon/icon-explorer)
- [Design Tokens](https://atlassian.design/components/tokens)
- [Primitives](https://atlassian.design/components/primitives)
- [Content Standards](https://atlassian.design/foundations/content)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eevennsoh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
