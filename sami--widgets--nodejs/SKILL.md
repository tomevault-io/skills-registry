---
name: node-js
description: JavaScript runtime built on Chrome's V8 engine Use when this capability is needed.
metadata:
  author: sami
---

# Node.js Skill

## Best Practices
1.  **Async/Await**: Use `async/await` over Callbacks and raw Promises.
2.  **Error Handling**: Always handle Promise rejections (`.catch()` or `try/catch`). Unhandled rejections crash the process.
3.  **Streams**: Use Streams/Pipelines for large file processing to save memory.
4.  **Security**: Use `helmet` for headers. Validate all inputs.

## Common Pitfalls
*   **Blocking the Event Loop**: CPU-intensive tasks (e.g., image resizing) on the main thread. Use Worker Threads or queues.
*   **Sync APIs**: Using `fs.readFileSync` in HTTP handlers. Never do this.

## References
*   [Node.js Docs](https://nodejs.org/docs/latest/api/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sami) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
