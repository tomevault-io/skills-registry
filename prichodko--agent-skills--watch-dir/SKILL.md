---
name: watch-dir
description: Watch directories for file changes using Node.js fs.watch. Use when monitoring files, detecting changes, or building watch-based workflows. Use when this capability is needed.
metadata:
  author: prichodko
---

# Watch Directory

Watch directory and subdirectories for changes using `fs.watch`.

## CLI

```bash
.claude/skills/watch-dir/watch.ts [dir]
```

Watches `dir` (default: current) recursively. Outputs `event: path` on changes. Ctrl-C to stop.

## Code Examples

### Basic (recursive)

```ts
import { watch } from "fs";

const watcher = watch("./src", { recursive: true }, (event, path) => {
  console.log(`${event}: ${path}`);
});
```

## Async iterator

```ts
import { watch } from "fs/promises";

const watcher = watch("./src", { recursive: true });
for await (const { eventType, filename } of watcher) {
  console.log(`${eventType}: ${filename}`);
}
```

## Cleanup on SIGINT

```ts
import { watch } from "fs";

const watcher = watch("./src", { recursive: true }, (event, path) => {
  console.log(`${event}: ${path}`);
});

process.on("SIGINT", () => {
  watcher.close();
  process.exit(0);
});
```

## Events

- `rename`: file created, deleted, or renamed
- `change`: file content modified

## Notes

- `recursive: true` required for subdirectories
- `import.meta.dir` = current file's directory (bun only)
- call `watcher.close()` to stop watching

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/prichodko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
