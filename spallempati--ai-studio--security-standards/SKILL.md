---
name: security-standards
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Security Standards for Code Generation

When generating code for this project, always prioritize security and follow
these guidelines based on industry-standard security frameworks (OWASP, CWE, HIPAA).

## Critical Security Rules (Top Priority)

### Rule 1: Enforce Authorization on Every Object Access

**Goal**: Prevent Broken Object Level Authorization (BOLA), the #1 API risk.  
**Corresponds to**: OWASP API1:2023, CWE-862

For any function accessing data via a user-supplied ID, you **MUST** verify the
authenticated user has permission for that specific object ID.

**Example**: `get_record(record_id, current_user)` must check
`if record.owner_id != current_user.id: raise PermissionDenied()`

**Rationale**: Prevents users from accessing data they don't own.

### Rule 2: Use Parameterized Queries, Never String Concatenation

**Goal**: Eliminate SQL Injection (SQLi).  
**Corresponds to**: OWASP A03:2021, CWE-89

You **MUST NOT** build database queries by concatenating strings with user input.
You **MUST** use parameterized queries (prepared statements).

**Example (Good)**:
```python
db.execute("SELECT * FROM users WHERE id = ?", (user_id,))
```

**Example (Bad)**:
```python
db.execute("SELECT * FROM users WHERE id = '" + user_id + "'")
```

**Rationale**: This is the single most effective defense against SQLi.

### Rule 3: Encode All Output Contextually

**Goal**: Prevent Cross-Site Scripting (XSS).  
**Corresponds to**: OWASP A03:2021, CWE-79

When rendering user data in output, you **MUST** apply context-aware encoding. Use a
vetted library. Differentiate between HTML body, attributes, JS, etc.

**Example**: Use `encodeForHTML()` for `<div>` content and
`encodeForJavaScript()` for `<script>` content.

- You **SHOULD** use context-appropriate encoding (HTML, JavaScript, URL, CSS)
- You **SHOULD** implement Content Security Policy (CSP) headers where applicable

**Rationale**: Prevents the browser from executing malicious user-supplied scripts.

### Rule 4: Externalize All Secrets

**Goal**: Prevent credential exposure.  
**Corresponds to**: OWASP A07:2021, CWE-798

You **MUST NOT** hard-code secrets (passwords, API keys, tokens) in source code. You
**MUST** retrieve secrets from a secure external source at runtime.

**Example**: `api_key = os.environ.get('API_KEY')` or use Azure Key Vault

- You **MUST** rotate GitHub PATs & deploy tokens <= 90 days
- You **MUST** store secrets using User Secrets, Azure Key Vault, or equivalent

**Rationale**: Prevents secrets from being committed to version control.

### Rule 5: Validate Server-Side Request Destinations

**Goal**: Prevent Server-Side Request Forgery (SSRF).  
**Corresponds to**: OWASP A10:2021, CWE-918

When making a server-side request to a user-provided URL, you **MUST** validate the
destination against a strict allowlist of hosts and protocols. Do not allow
requests to internal, private, or loopback IP addresses.

**Example**: Parse the URL, resolve the IP, and check it against an allowlist.

**Rationale**: Prevents the server from being used as a proxy to attack internal systems.

### Rule 6: Use Memory-Safe Functions and Bounds Checking (C/C++)

**Goal**: Prevent memory corruption bugs like buffer overflows.  
**Corresponds to**: CWE-787, CWE-119, CWE-125

In C/C++, you **MUST NOT** use unsafe functions like `strcpy`. Use bounded
alternatives like `strncpy`. You **MUST** add explicit bounds checking to all loops
that access buffers.

**Example**: `if (index >= 0 && index < BUFFER_SIZE) { buffer[index] = val; }`

**Rationale**: Directly mitigates the #1 most dangerous software weakness.

### Rule 7: Rate-Limit and Paginate All API Endpoints

**Goal**: Prevent Denial of Service and resource exhaustion.  
**Corresponds to**: OWASP API4:2023

Every API endpoint you generate **MUST** include a call to a rate-limiting
mechanism. Any endpoint that returns a list **MUST** implement pagination by default
and enforce a maximum page size.

**Example**: Apply a rate-limiter and use `limit` and `offset` parameters with a
hard-coded maximum for `limit`.

**Rationale**: Protects API availability and controls operational costs.

### Rule 8: Protect PHI in All Operations

**Goal**: Ensure HIPAA compliance for Protected Health Information.  
**Corresponds to**: HIPAA §164.312, HITECH Act

PHI includes: patient names, SSN, MRN, dates, diagnoses, treatments, insurance.

- You **MUST NOT** log PHI—use hashed identifiers in logs instead
- You **MUST** encrypt PHI at rest (AES-256) and in transit (TLS 1.2+)
- You **MUST** return only necessary PHI fields (minimum necessary principle)
- You **MUST** use synthetic data in tests—never real patient data
- Error messages **MUST NOT** contain PHI values
- Database queries **MUST** select specific columns, never `SELECT *` for PHI
- PHI fields in APIs **MUST** be filtered based on user role and need-to-know

**Example (Good)**:
```python
logger.info("Record accessed", extra={"record_hash": sha256(mrn)})
```

**Example (Bad)**:
```python
logger.info(f"Accessed patient {name}, MRN: {mrn}, diagnosis: {dx}")
```

## OWASP Top Ten 2021 Guidelines

### 1. Broken Access Control

- Generated code **MUST** include access control checks for any resource access
  or actions that require authorization
- You **MUST** adhere to the principle of least privilege and validate user roles
- You **MUST NOT** assume user permissions without explicit verification

### 2. Cryptographic Failures

- You **MUST** use standard, vetted cryptographic libraries (e.g., `cryptography` in Python)
- You **MUST** follow best practices for key management and **MUST NOT** hardcode secrets
- You **MUST** use strong, industry-accepted algorithms such as AES-GCM for
  encryption and SHA-256 for hashing
- You **MUST** use a cryptographically secure random number generator
- PHI/PII **MUST** be encrypted at rest (AES-256) and in transit (TLS 1.2+)

### 3. Injection Prevention

- You **MUST** prevent injection attacks by using parameterized queries or
  proper escaping mechanisms
- For any user input incorporated into queries or commands, you **MUST** use
  proper sanitization
- You **MUST** use prepared statements and input validation

### 4. Insecure Design

- You **MUST NOT** generate code that inherently trusts untrusted sources
- You **MUST NOT** bypass necessary security checks for convenience
- You **SHOULD** incorporate threat modeling principles where applicable
- You **MUST** default to secure configurations and explicit security controls

### 5. Security Misconfiguration

- You **MUST** include secure default configurations in generated code
- You **MUST** set appropriate permissions and file access controls
- You **MUST** disable debug modes in production environments
- You **MUST NOT** include hardcoded insecure values like default passwords or API keys

### 6. Vulnerable and Outdated Components

- You **SHOULD** suggest using the latest secure versions of libraries and components
- You **MUST** check for known vulnerabilities in dependencies
- You **MUST** pin dependency versions
- You **MUST** download all dependencies from the authorized Artifactory
- You **MUST** pass dependency-scan with 0 criticals before merge (Snyk or Grype)

### 7. Identification and Authentication Failures

- You **MUST** ensure that generated code uses strong password hashing
  mechanisms (e.g., bcrypt, scrypt)
- You **MUST** implement secure session management practices
- You **MUST** follow OWASP guidelines for authentication and session handling
- You **MUST NOT** store passwords in plain text
- You **SHOULD** enforce MFA on all repo contributors (verified via org policy)

### 8. Software and Data Integrity Failures

- You **MUST** use secure deserialization practices
- You **MUST** include integrity checks for software updates and data to prevent tampering
- You **MUST** ensure the authenticity of sources
- You **MUST** validate data integrity before processing

### 9. Security Logging and Monitoring Failures

- You **MUST** include logging mechanisms for security-related events
- You **SHOULD** log failed login attempts, access control failures, and suspicious activities
- You **MUST** ensure that logs are properly configured for monitoring and analysis
- You **MUST NOT** log sensitive data like passwords, tokens, or PHI
- PHI access **MUST** be logged to audit trails (who, what, when, why) without
  logging the PHI content itself

### 10. Server-Side Request Forgery (SSRF)

- You **MUST** validate and restrict destinations of external requests made from the server
- You **MUST** prevent SSRF attacks by ensuring that only intended domains or IPs are accessed
- You **MUST** implement allowlists for external request destinations
- You **MUST** use proper URL validation and sanitization

## Additional CWE Top 25 Rules

### CWE-352: Cross-Site Request Forgery (CSRF)

- For web applications, you **MUST** include CSRF tokens in forms and links
- You **MUST** validate these tokens on the server-side to prevent CSRF attacks
- You **SHOULD** use SameSite cookie attributes where appropriate

### CWE-22: Path Traversal

- When handling file paths in generated code, you **MUST** validate and restrict paths
- You **MUST** prevent unauthorized access to files outside of intended directories
- You **MUST** use allowlists for file access and normalize paths before validation

## Project-Specific Security Context

### API Security Patterns

- Always use HTTPS with SSL certificate verification (`verify=True`)
- Implement proper timeout handling (30 seconds default)
- Handle rate limiting responses (HTTP 429) gracefully
- Use secure session management with proper headers
- Never log API tokens, sensitive authentication data, or PHI

## Implementation Principles

When generating code, you **MUST** adhere to the following principles:

1. **Security by Default**: Always choose the most secure option as the default
2. **Fail Securely**: Ensure that failures result in a secure state
3. **Defense in Depth**: Implement multiple layers of security controls
4. **Principle of Least Privilege**: Grant minimal necessary permissions
5. **Input Validation**: Validate all inputs at boundaries
6. **Error Handling**: Provide informative but not revealing error messages
7. **PHI Protection**: Treat all health data as sensitive; encrypt, audit, minimize

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
