---
name: hoot
description: Scheme→WebAssembly compiler (4K lines info). Use when this capability is needed.
metadata:
  author: plurigrid
---

# hoot

Scheme→WebAssembly compiler (4K lines info).

## Compile

```bash
guild compile-wasm -o out.wasm script.scm
```

## Features

- Full tail call optimization
- First-class continuations
- JavaScript interop
- Standalone Wasm modules

## Example

```scheme
(define-module (my-module)
  #:export (greet))

(define (greet name)
  (string-append "Hello, " name "!"))
```

## Runtime

```javascript
import { Hoot } from '@aspect/guile-hoot';
const mod = await Hoot.load('out.wasm');
mod.greet("World");
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plurigrid) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
