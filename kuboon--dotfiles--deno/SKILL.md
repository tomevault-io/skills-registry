---
name: deno
description: Rules for using Deno Use when this capability is needed.
metadata:
  author: kuboon
---

# Imports

## bad

```ts
import { serve } from "https://deno.land/std@0.177.0/http/server.ts";
```

## good

run

- `deno add jsr:@std/http`
- `deno add npm:tailwindcss` to add the import map, then

```ts
import { serve } from "@std/http/server.ts";
import { serve } from "tailwindcss";
```

# Permissions

## bad

```sh
deno run --allow-net server.ts
```

## good

Write a `deno.json` file in the root of your project:

```json
{
  "permissions": {
    "default": {
      "net": ["example.com", "api.example.com"],
      "read": ["./data/*"],
      "env": {
        "ignore": true,
        "allow": ["API_KEY", "DATABASE_URL"]
      }
    },
    "build": {
      "net": ["deno.land"],
      "read": ["./src/*"],
      "write": ["./dist/*"]
    }
  }
}
```

Then run your script with the `-P` flags:

```sh
deno run -P server.ts
deno run -P=build build.ts
```

## reference

- https://github.com/denoland/deno/blob/main/cli/schemas/config-file.v1.json
- https://docs.deno.com/runtime/fundamentals/configuration/#permissions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kuboon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
