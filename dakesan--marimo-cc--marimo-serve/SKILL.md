---
name: marimo-serve
description: | Use when this capability is needed.
metadata:
  author: dakesan
---

# Marimo Development Server

Start a marimo development server with hot reload for interactive notebook development.

## Server Command

To start the server in background:

```bash
uv run marimo run --watch {filename} --port 2818
```

Use the Bash tool with `run_in_background: true` parameter to keep the server running.

## Configuration

- Default port: 2818
- Access URL: `http://localhost:2818`
- Hot reload: Enabled with `--watch` flag

## Verification

After starting the server:

1. Check running tasks with `/tasks` command
2. Verify server is accessible via WebFetch to `http://localhost:2818`
3. Use `lsof -i :2818` to confirm port is in use

## Workflow

1. Ensure the target notebook file exists
2. Start server with `run_in_background: true`
3. Edit notebook files with the Edit tool
4. Server automatically reloads on file changes
5. Fetch results via WebFetch when needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dakesan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
