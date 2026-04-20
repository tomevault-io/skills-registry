---
name: apryse-annotate
description: Add annotations to PDFs using Apryse SDK. Highlight, underline, strikeout text. Add sticky notes and shapes. Use when user wants to mark up, annotate, or add comments to PDF documents. Use when this capability is needed.
metadata:
  author: ipixeldust
---

# PDF Annotations

Add text markup and annotations to PDFs. All scripts are in the `scripts/` directory.

## Available Scripts

### Highlight Text
```bash
node scripts/highlight.js <input.pdf> <output.pdf> --search "text to find" [--color yellow]
```
Colors: yellow, red, green, blue, orange, pink, cyan

### Underline Text
```bash
node scripts/underline.js <input.pdf> <output.pdf> --search "text to find" [--color blue]
```

### Strikeout Text
```bash
node scripts/strikeout.js <input.pdf> <output.pdf> --search "text to find" [--color red]
```

### Add Sticky Note
```bash
node scripts/add-note.js <input.pdf> <output.pdf> <page> <x> <y> "note content" [--color yellow]
```
Coordinates are in points (72 points = 1 inch). Origin is bottom-left.

### List Annotations
```bash
node scripts/list-annotations.js <input.pdf>
```
Outputs JSON array of all annotations with type, page, position.

### Flatten Annotations
```bash
node scripts/flatten.js <input.pdf> <output.pdf> [--forms-only]
```
Converts annotations to static content (non-editable).

## When to Use

- User asks to "highlight" text → `highlight.js`
- User asks to "underline" text → `underline.js`
- User asks to "strikeout", "strikethrough", or "cross out" → `strikeout.js`
- User asks to "add note", "add comment", or "annotate" → `add-note.js`
- User asks to "list annotations" or "what annotations" → `list-annotations.js`
- User asks to "flatten" or "make annotations permanent" → `flatten.js`

## Color Options

| Color | Use for |
|-------|---------|
| yellow | General highlights |
| red | Important, errors, deletions |
| green | Approved, correct |
| blue | Links, references |
| orange | Warnings, attention |

## Notes

- Search is case-sensitive and matches whole words by default
- Annotations are added to a copy; original file is unchanged
- Use `list-annotations.js` to see existing annotations before modifying

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ipixeldust) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
