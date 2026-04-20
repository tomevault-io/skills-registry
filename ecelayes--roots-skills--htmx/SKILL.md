---
name: htmx-universal-patterns
description: Use when working with the definitive guide for building Hypermedia-Driven Applications (HDA) using HTMX, prioritizing security and UX patterns.
metadata:
  author: ecelayes
---

# HTMX Universal Standards

## Core Philosophy (HATEOAS)
1.  **HTML over JSON:** The server MUST respond with HTML fragments (partials), not JSON.
2.  **Side Effects via HTML:** Do not use client-side logic to update the DOM. Let the server response dictate changes via `hx-swap`.

## Security & CSRF (Critical)
1.  **CSRF Protection:** HTMX requests are non-idempotent (POST/PUT/DELETE) and require CSRF protection just like standard forms.
    - **Header Method:** Configure the global `hx-headers` to include the token: `<body hx-headers='{"X-CSRF-Token": "{{ csrfToken }}"}'>`.
    - **Form Method:** If headers aren't viable, ensure every `<form>` includes the hidden CSRF input.
2.  **XSS Prevention:** Since we are injecting HTML, ensure all user content rendered on the server is strictly escaped before reaching the client.

## Architectural Rules
1.  **The "Partial" Rule:** Identify strictly which part of the UI needs updating. Create a server route that renders *only* that component.
2.  **Idempotency:** GET requests should never change state. Use POST/PUT/PATCH/DELETE for actions.
3.  **Progressive Enhancement:** Design the feature to work with standard HTML forms/links first where possible.

## UX & Feedback Patterns
1.  **Request Indicators:** ALWAYS use `hx-indicator`.
    - Pattern: `<button hx-post="..." hx-indicator="#loading-spinner">Save</button>`
2.  **Active States:** Use the `htmx-added` class or `hx-vals` to manage active states via server rendering.

## Error Handling Protocol
The backend must communicate status via HTTP Codes:
- **200 OK:** Swap content normally.
- **204 No Content:** Do nothing.
- **422 Unprocessable Entity:** Validation error. Swap the form with the HTML containing error messages.
- **HX-Retarget:** Use this header if an error requires updating a global element (like a top-level alert) instead of the local target.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ecelayes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
