---
name: kintone-best-practice
description: Guidelines and best practices for kintone customization and plugin development. Use this skill when writing JavaScript/CSS for kintone, using the REST API, or reviewing code for security and compatibility. It covers safe coding patterns, API usage, and security requirements. Use when this capability is needed.
metadata:
  author: hiroyukimakita
---

# kintone Best Practices

This skill provides official guidelines for developing on the kintone platform.

## When to Use

- **Writing JavaScript Customizations**: To ensure code is safe, compatible, and maintainable.
- **Using kintone REST API**: To follow performance rules and best practices.
- **Security Reviews**: To check for common vulnerabilities like XSS or credential leaks.
- **Code Reviews**: To verify compliance with kintone's coding standards.

## Usage Instructions

### 1. JavaScript Coding

For rules regarding variables, DOM manipulation, and URL handling:

- **Read**: [references/javascript-guidelines.md](references/javascript-guidelines.md)

**Key Points**:

- Use IIFE or block scope to avoid global variable pollution.
- Do NOT rely on kintone's internal DOM structure (id/class attributes).
- Use `kintone.api.url()` for URL generation.

### 2. Secure Coding

For security requirements and preventing vulnerabilities:

- **Read**: [references/secure-coding.md](references/secure-coding.md)

**Key Points**:

- Prevent XSS by avoiding `innerHTML` with untrusted data.
- Never store secrets (API keys) in frontend code; use Plugin Proxy.
- Validate URLs to prevent open redirects.

### 3. General & API Best Practices

For REST API usage and general development tips:

- **Read**: [references/general-practices.md](references/general-practices.md)

**Key Points**:

- Avoid massive parallel requests.
- Use Bulk APIs for data operations.
- Be aware of kintone update impacts.

## Quick Checklist for Developers

1. **Scope**: Is all code wrapped in an IIFE (`(() => { ... })();`)?
2. **Globals**: Are you modifying `window` or `cybozu` objects? (Don't!)
3. **Selectors**: Are you selecting elements by auto-generated IDs? (Don't! Use API or custom elements).
4. **XSS**: Are you using `innerHTML`? (Use `innerText` or `textContent`).
5. **Secrets**: Are credentials hardcoded? (Use Proxy or Backend).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hiroyukimakita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
