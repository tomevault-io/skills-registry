---
name: frontend-llm-output-handler
description: Guide for enhancing frontend code with the capability to render AI/LLM outputs (Markdown, LaTeX, code blocks) securely and accurately in the UI. Use when this capability is needed.
metadata:
  author: haoliangcheng
---

# Frontend LLM Output Handler

## Goal
Enhance existing frontend code to support the rendering of complex LLM response strings.

- **Input**: Frontend code (JavaScript, React, TypeScript).
- **Output**: Enhanced frontend code with integrated capabilities for parsing Markdown, rendering LaTeX math formulas, and displaying interactive code blocks.

## Dependencies
The implementation uses the following packages:
- **Markdown**: `marked`
- **Math/LaTeX**: `katex`
- **Sanitization**: `dompurify`
- **Syntax Highlighting**: `highlight.js`
- **React Styling**: `styled-components`
- **DOM Utility**: `jQuery` (optional, for non-React implementations)

## Instructions

To enhance frontend components for AI-generated responses, follow this 4-step integration pipeline:

### 1. Pre-processing (Math Compatibility)
AI models often produce single backslashes in LaTeX (e.g., `\frac`). Most Markdown parsers consume these as escape characters. You must normalize them before parsing.

```javascript
// Normalize backslashes for KaTeX compatibility
const normalized = rawText.replace(/\\/g, '\\\\');
```

### 2. Markdown Parsing
Use a robust parser like `marked`. Ensure you handle the output as an HTML string.

### 3. Code Block Decoration
AI responses frequently contain code. Enhance standard output by wrapping it in a container with a language label to improve readability and visual structure.

### 4. Security & Post-processing
- **Sanitize with Config**: ALWAYS use `DOMPurify`. Ensure your configuration allows the specific tags and attributes you've added (e.g., `class`).
  ```javascript
  DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['p', 'br', 'strong', 'em', 'img', 'code', 'pre', 'ul', 'ol', 'li', 'h1', 'h2', 'h3', 'div', 'span', 'table', 'thead', 'tbody', 'tr', 'th', 'td'],
    ALLOWED_ATTR: ['src', 'alt', 'class', 'href', 'style']
  });
  ```
- **Math Rendering (KaTeX)**: Call `renderMathInElement` after the content is added to the DOM. You MUST specify delimiters so the renderer knows what to look for:
  ```javascript
  renderMathInElement(container, {
    delimiters: [
      { left: '$$', right: '$$', display: true },
      { left: '$', right: '$', display: false },
      { left: '\\(', right: '\\)', display: false },
      { left: '\\[', right: '\\]', display: true }
    ],
    throwOnError: false
  });
  ```

## Performance & UX Tips (React)
- **Memoization**: Use `useMemo` to wrap the parsing logic and `React.memo` for message components. This prevents expensive re-parsing of the entire chat history when new messages arrive.
- **Auto-scroll**: Implement a `useEffect` that monitors the message list and updates `scrollTop` to keep the latest AI response in view.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haoliangcheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
