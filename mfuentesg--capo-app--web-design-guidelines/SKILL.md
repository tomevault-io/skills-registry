---
name: web-design-guidelines
description: Review UI code for Web Interface Guidelines compliance. Use when auditing design, accessibility, or UX. Use when this capability is needed.
metadata:
  author: mfuentesg
---

# Web Interface Guidelines

Review files for compliance with Web Interface Guidelines from Vercel Labs.

## How It Works

When you ask Copilot to review UI code:

1. Fetch the latest guidelines from Vercel Labs
2. Read specified files
3. Check against all rules in the fetched guidelines
4. Output findings in the terse format

## Guidelines Source

```
https://raw.githubusercontent.com/vercel-labs/web-interface-guidelines/main/command.md
```

## Usage

Examples:

- Review features/songs/components/song-detail/song-detail.tsx for UI compliance
- Audit components/ui/card.tsx for accessibility
- Review all button components in components/ui/

## Review Checklist

Accessibility

- Form inputs have associated labels
- Buttons have descriptive text or aria-labels
- Color is not the only way to convey information
- Focus indicators are visible
- Keyboard navigation works
- ARIA roles are appropriate
- Alt text for images when applicable

Design

- Consistent spacing
- Clear typography hierarchy
- Consistent color palette
- Predictable interaction patterns

User Experience

- Loading states visible
- Error messages helpful
- Empty states exist
- Feedback on interaction is clear
- Responsive behavior on mobile

## Commands

```bash
pnpm lint
pnpm typecheck
pnpm build
```

## Resources

- https://ui.shadcn.com
- https://tailwindcss.com
- https://www.w3.org/WAI/
- https://www.w3.org/WAI/WCAG21/quickref/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mfuentesg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
