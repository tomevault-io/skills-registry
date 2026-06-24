---
name: document
description: | Use when this capability is needed.
metadata:
  author: edmundmiller
---

# Document Canvas

Display markdown documents with optional text selection and diff highlighting.

## Example Prompts

Try asking:

- "Draft an email to the marketing team about the Q1 product launch"
- "Help me edit this blog post - show it so I can highlight the parts to revise"
- "Write a project proposal and let me review it"
- "Show me the README so I can select sections to update"
- "Compose a response to this customer complaint"

## OpenCode Tools

### High-Level (Recommended)

**Display a document (read-only):**
```
Use canvas_display_document with:
  content: "# My Document\n\nMarkdown content here."
  title: "Document Title"
  diffs: [{ startOffset: 10, endOffset: 20, type: "added" }]  // optional
```

**Edit/select from a document:**
```
Use canvas_edit_document with:
  content: "# My Document\n\nSelect some text here."
  title: "Edit Mode"
```

### Low-Level (Full Control)

```
Use canvas_spawn with:
  kind: "document"
  scenario: "edit" or "display" or "email-preview"
  config: { content: "...", title: "..." }
```

## Scenarios

### `display` (default)
Read-only document view with markdown rendering. User can scroll but cannot select text.

### `edit`
Interactive document view with text selection. User can click and drag to select text, which is returned when canvas closes.

- Renders markdown with syntax highlighting (headers, bold, italic, code, links, lists, blockquotes)
- Diff highlighting: green background for additions, red for deletions
- Click and drag to select text
- Selection returned on close

### `email-preview`
Specialized view for email content display with email-specific formatting.

## Configuration

```typescript
interface DocumentConfig {
  content: string;        // Markdown content
  title?: string;         // Document title (shown in header)
  diffs?: DocumentDiff[]; // Optional diff markers for highlighting
  readOnly?: boolean;     // Disable selection (default: false for edit)
}

interface DocumentDiff {
  startOffset: number;    // Character offset in content
  endOffset: number;
  type: "added" | "removed" | "modified";
}
```

## Markdown Rendering

Supported markdown features:
- **Headers** (`# H1`, `## H2`, etc.)
- **Bold** (`**text**`)
- **Italic** (`*text*`)
- **Code** (`` `inline` `` and fenced blocks)
- **Links** (`[text](url)`)
- **Lists** (`-` or `*` bullets)
- **Blockquotes** (`>`)

## Selection Result

```typescript
interface DocumentSelection {
  selectedText: string;   // The selected text
  startOffset: number;    // Start character offset
  endOffset: number;      // End character offset
  startLine: number;      // Line number (1-based)
  endLine: number;
  startColumn: number;    // Column in start line
  endColumn: number;
}
```

## Controls

- **Mouse click and drag**: Select text (edit scenario)
- `Up/Down` or scroll: Navigate document
- `q` or `Esc`: Close/cancel

## Active Canvas Operations

After spawning with `canvas_spawn`, you can:

```
Use canvas_get_selection to get current text selection
Use canvas_get_content to get full document content
Use canvas_update to change content/config
Use canvas_close to close the canvas
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edmundmiller) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
