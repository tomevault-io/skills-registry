---
name: fastapi-chameleon
description: > Use when this capability is needed.
metadata:
  author: mikeckennedy
---

# FastAPI Chameleon

Adds integration of the Chameleon template language to FastAPI.

## Installation

```bash
pip install fastapi-chameleon
```

## API overview

### Setup

Point the library at your Chameleon templates folder once at app startup.

- `global_init`: Initialize the Chameleon template engine for your app

### Rendering views

Decorate FastAPI view functions to render templates, or build a response directly.

- `template`: Decorate a FastAPI view to render its return value through a Chameleon template
- `response`: Render a template and return it as a FastAPI response directly

### Error responses

Short-circuit a view to render a friendly error page with the right status code.

- `not_found`: Short-circuit the current view and render a friendly 404 page
- `generic_error`: Short-circuit the current view and render an error page with a custom status code

### Exceptions

Exception types raised by the library (the error helpers raise these internally).

- `exceptions.FastAPIChameleonException`
- `exceptions.FastAPIChameleonNotFoundException`
- `exceptions.FastAPIChameleonGenericException`

## Resources

- [Full documentation](https://mkennedy.codes/docs/fastapi-chameleon/)
- [llms.txt](llms.txt) — Indexed API reference for LLMs
- [llms-full.txt](llms-full.txt) — Comprehensive documentation for LLMs
- [Source code](https://github.com/mikeckennedy/fastapi-chameleon)

---
> Source: [mikeckennedy/fastapi-chameleon](https://github.com/mikeckennedy/fastapi-chameleon) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-02 -->
