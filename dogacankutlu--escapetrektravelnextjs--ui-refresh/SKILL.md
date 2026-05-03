---
name: ui-refresh
description: Refreshes the Escape Travel Trekking UI to be bright-only (no dark mode), vibrant, clearly outlined, and uncluttered. Use when the user requests design tweaks, “more colorful”, “no dark mode”, “looks wrong in Chrome”, or layout/spacing improvements. Use when this capability is needed.
metadata:
  author: dogacankutlu
---

# UI Refresh (Bright + Vibrant + Clean)

## Goal
- Bright-only UI (no dark mode behavior)
- Vibrant accents (gradients, color borders) without clutter
- Clear section boundaries (outlined cards/sections)
- Consistent layout in Chrome and other browsers

## Defaults for this project
- **No dark mode**: remove `dark:*` classes and remove `prefers-color-scheme` overrides.
- **Consistent images**: use `aspect-[16/9]` for thumbnails; `object-cover` for photos.
- **Avoid clutter**: collapse long text with `<details>` where appropriate.
- **Keep dependencies flat**: do not add UI libraries unless explicitly asked.

## Workflow (follow in order)
1. **Make the site bright-only**
   - In `app/globals.css`: set `color-scheme: light;` and ensure only light variables are defined.
   - Remove `@media (prefers-color-scheme: dark)` blocks.
   - Remove all `dark:*` Tailwind classes across `app/`.

2. **Increase vibrancy without mess**
   - Prefer 1–2 accent gradients (emerald → sky → amber).
   - Use **border-2** + soft backgrounds (`bg-white/70`, `shadow-sm`) for sections/cards.
   - Keep typography calm: strong headings, lighter body (`text-black/70`).

3. **Improve clarity of sections**
   - Wrap major sections in a shared “panel” style:
     - `rounded-[28px] border-2 border-<accent> bg-white/70 p-7 shadow-sm`
   - Use consistent spacing:
     - page: `space-y-12` or `space-y-14`
     - section: `space-y-6`

4. **Fix Chrome / aspect ratio issues**
   - For images inside cards, always use:
     - wrapper: `relative aspect-[16/9] overflow-hidden rounded-2xl`
     - image: `fill` + `className="object-cover"`
   - Ensure no unintentional overflow: avoid huge absolutely-positioned blurs without bounds.

5. **Validate**
   - Run: `npm run lint` and `npm run build`.
   - Quickly sanity-check in Chrome at common widths (mobile/tablet/desktop).

## Where to edit (common)
- `app/layout.tsx`: header/footer styling + global background
- `app/globals.css`: theme variables and light-only settings
- `app/page.tsx`: home layout density, images, section styles
- `app/contact/page.tsx`: CTA blocks, buttons, contact cards

## Mini patterns

### 16:9 photo block
```tsx
<div className="relative aspect-[16/9] w-full overflow-hidden rounded-2xl border-2 border-black/10 bg-white">
  <Image
    src={photoUrl}
    alt={alt}
    fill
    sizes="(min-width: 1024px) 360px, (min-width: 640px) 45vw, 100vw"
    className="object-cover"
  />
</div>
```

### “Less clutter” expandable details
```tsx
<details className="rounded-2xl border-2 border-black/10 bg-background/70 p-4 text-sm">
  <summary className="cursor-pointer select-none font-medium">More details</summary>
  <div className="mt-2 text-black/70">…</div>
</details>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dogacankutlu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
