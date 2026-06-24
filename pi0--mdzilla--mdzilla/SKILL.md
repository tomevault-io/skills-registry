---
name: mdzilla
description: > Use when this capability is needed.
metadata:
  author: pi0
---

# mdzilla

Markdown documentation browser for terminal and agents. Fetches and renders docs from local dirs, GitHub, npm, HTTP, and `llms.txt`.

## CLI

```sh
npx mdzilla <source> [query] [options]
```

### Sources

| Source    | Syntax                        | Notes                                                            |
| :-------- | :---------------------------- | :--------------------------------------------------------------- |
| Local dir | `mdzilla ./docs`              | Scan local docs directory                                        |
| File      | `mdzilla README.md`           | Render single markdown file                                      |
| GitHub    | `mdzilla gh:owner/repo`       | Looks for `docs/` in repo                                        |
| npm       | `mdzilla npm:package-name`    | Downloads package, reads docs                                    |
| HTTP      | `mdzilla https://example.com` | Tries `/llms.txt`, then markdown negotiation, then HTML→markdown |

### Options

- `--export <dir>` — Export docs to flat `.md` files
- `--plain` — Plain text output; auto-enabled for AI agents or non-TTY stdout

### Smart Resolve

The second positional argument is smart-resolved:

1. If it matches a navigation path → renders that page
2. Otherwise → searches titles and content

### Agent usage

```sh
npx mdzilla gh:unjs/h3 /guide/basics        # render a specific page
npx mdzilla gh:unjs/h3 router               # search for 'router'
npx mdzilla https://h3.unjs.io /guide       # render page from URL
npx mdzilla npm:consola --plain             # list all pages (plain)
```

Export docs for offline processing:

```sh
npx mdzilla <source> --export ./fetched-docs
```

Then read the exported `.md` files as needed.

## Programmatic API

### Export Docs

```js
import { exportSource } from "mdzilla";

await exportSource("./docs", "./dist/docs");
await exportSource("gh:unjs/h3", "./dist/h3-docs");
await exportSource("npm:h3", "./dist/h3-docs", { plainText: true });
await exportSource("https://h3.unjs.io", "./dist/h3-docs");
```

### Collection

```js
import { Collection, resolveSource } from "mdzilla";

const docs = new Collection(resolveSource("./docs"));
await docs.load();

console.log(docs.tree); // NavEntry[] navigation tree
console.log(docs.flat); // FlatEntry[] flat list

const content = await docs.getContent(docs.flat[0]);
const results = docs.filter("query"); // fuzzy search
```

---
> Source: [pi0/mdzilla](https://github.com/pi0/mdzilla) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
