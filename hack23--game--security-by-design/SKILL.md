---
name: security-by-design
description: High-level security principles and enforcement rules for building secure applications from the ground up with defense-in-depth Use when this capability is needed.
metadata:
  author: hack23
---

# Security-by-Design Skill

## Context
Applies to all code handling user input, authentication, or sensitive data. Also applies to infrastructure config, deployment pipelines, and security-critical functionality.

Security-by-Design means building security into every phase, not as an afterthought. Aligned with [Hack23 AB's ISMS](https://github.com/Hack23/ISMS-PUBLIC).

## Rules

1. **Assume Breach**: Limit blast radius and lateral movement
2. **Defense in Depth**: Multiple security layers — never rely on one control
3. **Least Privilege**: Minimum permissions required for functionality
4. **Fail Securely**: Fail to secure state, generic errors to users, detailed logs internally
5. **Never Trust Input**: Validate, sanitize, and encode all input (client + server)
6. **Secure by Default**: Disable unused features, secure configurations out of the box
7. **Encrypt Everything**: AES-256 at rest, TLS 1.3+ in transit
8. **No Secrets in Code**: Never hardcode credentials, keys, or tokens
9. **Audit Everything**: Log security events for compliance and forensics
10. **Minimize Attack Surface**: Remove unnecessary features, endpoints, dependencies
11. **Security Testing**: Test auth, authorization, validation, encryption paths
12. **Keep Dependencies Updated**: Patch known vulnerabilities promptly
13. **Document Security Decisions**: Threat models, architecture, risk decisions

## Examples

### ✅ Input Validation

```typescript
/** Validates plain-text user input — ISMS Policy SC-002 */
function validatePlainTextInput(input: unknown): string {
  if (typeof input !== 'string') throw new Error('Input must be a string');
  const normalizedInput = input.trim();
  if (normalizedInput.length === 0) throw new Error('Input is required');
  if (normalizedInput.length > 100) throw new Error('Input too long');
  return normalizedInput;
}

/** Encode untrusted data before inserting it into an HTML context */
function escapeHtml(input: string): string {
  return input
    .replace(/&/g, '&amp;')
    .replace(/</g, '&lt;')
    .replace(/>/g, '&gt;')
    .replace(/"/g, '&quot;')
    .replace(/'/g, '&#39;');
}

const userInput = '  Hello <script>alert("xss")</script>  ';
const plainText = validatePlainTextInput(userInput);
const safeHtml = `<p>${escapeHtml(plainText)}</p>`;

// For rich HTML, use a proven sanitizer such as DOMPurify instead of
// relying on ad-hoc character stripping.
```

### ✅ Secure Error Handling

```typescript
function handleRequest(): { success?: true; error?: string } {
  try {
    // Perform the requested operation here
    return { success: true };
  } catch {
    // In browser code, avoid logging raw error details to the console because
    // end users can access them. Send detailed diagnostics to restricted
    // server-side logs or monitoring instead.
    console.warn('Request failed');
    return { error: 'An error occurred. Please try again.' };
  }
}
```

### ❌ Anti-Patterns

```typescript
// BAD: Hardcoded secret
const API_KEY = "<API_KEY>";

// BAD: Leaking stack trace to user
function handleError(error: Error): { error: string } {
  return { error: error.stack ?? 'Unknown error' }; // Exposes internals!
}

// BAD: No input validation — XSS vulnerability
const userContent = '<img src=x onerror=alert(1)>';
document.body.innerHTML = userContent;
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
