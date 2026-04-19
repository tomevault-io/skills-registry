---
name: javascript-code-style
description: This skill should be used when writing, editing, or reviewing any JavaScript code in the ECP codebase. It provides coding standards for error handling in Express API routes. Always apply these guidelines when working with JavaScript files (.js) in the api/ directory. Use when this capability is needed.
metadata:
  author: iamyojimbo
---

# JavaScript Code Style

This skill provides JavaScript coding standards for the ECP codebase. After
writing code, note the code rules applied, e.g. "Applied: 1.1 - Always wrap
async route handlers in try-catch".

## Rules

1. Error Handling
   1. Always wrap async Express route handlers in try-catch blocks. The ECP API
      has no global error middleware, so unhandled promise rejections cause
      requests to hang indefinitely until timeout. Note: This does NOT apply to
      database migrations (`api/migrations/`) - migrate-mongo handles errors at
      the framework level.
   2. Use `setErrorResponse(res, title, detail, status)` from `../lib/http.js`
      for error responses.
   3. Log errors with `console.error()` before returning error response.
   4. Return 400 for client errors (validation, bad input), 500 for server
      errors (database failures, unexpected exceptions).
   5. Example:
      ```javascript
      router.post('/endpoint', async (req, res) => {
        try {
          // ... route logic
          res.json(result);
        } catch (error) {
          console.error('Error in /endpoint:', error);
          return setErrorResponse(res, 'Operation failed', error.message, 500);
        }
      });
      ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iamyojimbo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
