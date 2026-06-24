---
name: node-minify
description: | Use when this capability is needed.
metadata:
  author: srod
---

# node-minify

Lightweight Node.js minification library supporting JS, CSS, HTML, JSON, and images.

## Installation

```bash
# Core + compressor(s)
npm install @node-minify/core @node-minify/terser
```

## Quick Start

```ts
import { minify } from "@node-minify/core";
import { terser } from "@node-minify/terser";

// File-based
await minify({
  compressor: terser,
  input: "src/app.js",
  output: "dist/app.min.js",
});

// In-memory
const result = await minify({
  compressor: terser,
  content: "const x = 1; const y = 2;",
});
```

## Compressor Selection

| Type | Recommended | Alternatives |
|------|-------------|--------------|
| **JS** | `terser` (modern, fast) | `esbuild` (fastest), `swc`, `oxc`, `uglify-js`, `google-closure-compiler` (advanced optimizations, requires Java) |
| **CSS** | `lightningcss` (Rust, fastest) | `esbuild`, `clean-css`, `cssnano`, `csso` |
| **HTML** | `html-minifier` | `minify-html` |
| **JSON** | `jsonminify` | - |
| **Images** | `sharp` (WebP/AVIF) | `svgo` (SVG), `imagemin` (PNG/JPEG/GIF) |
| **Utility** | `no-compress` | Pass-through for concatenation without minification |

**Deprecated (avoid)**: `babel-minify`, `uglify-es`, `yui`, `crass`, `sqwish`

## API Reference

### Settings (user input)

```ts
type Settings = {
  compressor: Compressor;           // Required: compressor function
  input?: string | string[];        // File path(s) or glob: "src/*.js"
  output?: string | string[];       // Output path(s) or pattern: "$1.min.js"
  content?: string | Buffer;        // In-memory content (skip input/output)
  options?: Record<string, unknown>; // Compressor-specific options
  type?: "js" | "css";              // Required for esbuild, yui
  publicFolder?: string;            // Prepend to input paths
  replaceInPlace?: boolean;         // Overwrite input files
  silence?: boolean;                // Suppress console output
};
// Additional options: buffer, timeout, allowEmptyOutput (see TypeScript types)
```

### Return Value

`minify()` returns `Promise<string>` - the minified content.

## Common Patterns

### Glob/Wildcards

```ts
// All JS files in folder
await minify({
  compressor: terser,
  input: "src/**/*.js",
  output: "dist/bundle.min.js",
});
```

### Multiple Files -> Multiple Outputs

```ts
// $1 = original filename
await minify({
  compressor: terser,
  input: ["a.js", "b.js"],
  output: "$1.min.js", // -> a.min.js, b.min.js
});
```

### Source Maps

```ts
await minify({
  compressor: terser,
  input: "app.js",
  output: "app.min.js",
  options: { sourceMap: true },
});
```

### CSS with esbuild

```ts
import { esbuild } from "@node-minify/esbuild";

await minify({
  compressor: esbuild,
  type: "css", // Required for esbuild
  input: "styles.css",
  output: "styles.min.css",
});
```

### Image Optimization (WebP/AVIF)

```ts
import { sharp } from "@node-minify/sharp";

await minify({
  compressor: sharp,
  input: "image.png",
  output: "image.webp",
  options: { format: "webp", quality: 80 },
});
```

### HTML Minification

```ts
import { htmlMinifier } from "@node-minify/html-minifier";

await minify({
  compressor: htmlMinifier,
  input: "index.html",
  output: "index.min.html",
  options: {
    collapseWhitespace: true,
    removeComments: true,
  },
});
```

## Compressor-Specific Notes

| Compressor | Notes |
|------------|-------|
| `esbuild` | Requires `type: "js"` or `type: "css"` |
| `lightningcss` | Uses Buffer internally, supports `targets: { chrome: 95 }` |
| `google-closure-compiler` | Requires Java; advanced optimizations available |
| `yui` | Deprecated; requires Java; needs `type` |

## Error Handling

```ts
try {
  await minify({ compressor: terser, input: "app.js", output: "app.min.js" });
} catch (err) {
  if (err instanceof Error) {
    console.error("Minification failed:", err.message);
  }
}
```

## CLI

```bash
npx node-minify --compressor terser --input src/app.js --output dist/app.min.js
```

---
> Source: [srod/node-minify](https://github.com/srod/node-minify) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
