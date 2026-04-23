---
name: oauth-reviewer
description: Review OAuth 2.0 implementations against RFC 9700 (OAuth 2.0 Security Best Current Practice). Use when reviewing OAuth client or server code, auditing authorization flows, checking token handling, evaluating PKCE usage, or assessing OAuth security posture. Triggers on "review OAuth", "audit auth flow", "check OAuth security", or OAuth-related security concerns. Use when this capability is needed.
metadata:
  author: jtdowney
---

# OAuth 2.0 Security Review (RFC 9700)

Review OAuth implementations against [RFC 9700](https://www.rfc-editor.org/rfc/rfc9700.txt) — OAuth 2.0 Security Best Current Practice.

## Workflow

### 1. Determine Scope

Ask the user:
- **Role**: Client, authorization server, resource server, or combination?
- **Client type**: Public (SPA, native app, CLI) or confidential (server-side)?
- **Grant types used**: Authorization code, client credentials, device code?
- **Focus areas**: Any known concerns or specific flows to prioritize?

### 2. Identify OAuth Components

Locate in the codebase:
- Authorization request construction / handling
- Token endpoint requests / responses
- Redirect URI registration and validation
- PKCE implementation (code_verifier / code_challenge)
- Refresh token storage and rotation
- Token validation and audience checks
- Client authentication mechanisms
- State / nonce parameter handling

### 3. Evaluate Against Checklist

Work through the checklist below, rating each applicable item:
- **✅ Compliant** — Requirement fully satisfied
- **⚠️ Partial** — Met with caveats or gaps
- **❌ Non-compliant** — Violated or missing
- **➖ N/A** — Doesn't apply to this role/type
- **❓ Unknown** — Cannot determine from code inspection

See [rfc9700-requirements.md](references/rfc9700-requirements.md) for full requirement text and RFC section references.
See [attack-patterns.md](references/attack-patterns.md) for attack scenarios and detection guidance.

### 4. Report Findings

Structure the report as:
1. **Summary** — Overall posture, critical issues count
2. **Scope** — Role, client type, flows evaluated
3. **Critical Findings** — MUST/MUST NOT violations (highest priority)
4. **Recommendations** — SHOULD deviations worth fixing
5. **Detailed Analysis** — Section-by-section findings with code references

Cite both RFC sections (`RFC 9700 §2.1`) and code locations (`src/oauth.rs:142`).

## Quick-Reference Checklist

### Deprecated Grants
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| D1 | Resource owner password credentials grant not used | MUST NOT | 2.4 |
| D2 | Implicit grant (response_type=token) not used | SHOULD NOT | 2.1.2 |

### Redirect URI Validation
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| R1 | Exact string matching on redirect URIs (no patterns/wildcards) | MUST | 2.1, 4.1 |
| R2 | No open redirectors on client endpoints | MUST NOT | 4.11 |
| R3 | No HTTP scheme for authorization responses (except native loopback) | MUST NOT | 2.1 |
| R4 | AS rejects requests with invalid client_id/redirect_uri without auto-redirect | MUST NOT | 4.11 |

### PKCE
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| P1 | Public clients implement PKCE | MUST | 2.1.1 |
| P2 | Confidential clients implement PKCE | SHOULD | 2.1.1 |
| P3 | AS supports PKCE | MUST | 2.1.1 |
| P4 | AS enforces code_verifier when code_challenge was present | MUST | 4.8 |
| P5 | AS rejects code_verifier if no code_challenge in auth request | MUST | 4.8 |
| P6 | S256 challenge method used (not plain) | SHOULD | 2.1.1 |
| P7 | Code challenge values are transaction-specific and user-agent bound | MUST | 2.1.1 |

### CSRF Protection
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| C1 | Client uses PKCE, nonce (OIDC), or one-time state token | MUST | 2.1, 4.7 |
| C2 | State parameter invalidated after first use | SHOULD | 4.2.4 |

### Authorization Code Handling
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| A1 | Authorization codes invalidated after first use | MUST | 4.2.4 |
| A2 | On code replay, revoke all tokens from that grant | SHOULD | 4.2.4 |
| A3 | Nonce validated in ID Token (confidential OIDC clients) | MUST | 2.1.1 |

### Token Security
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| T1 | Sender-constrained access tokens (mTLS or DPoP) | SHOULD | 2.2 |
| T2 | Access tokens restricted to minimum privilege/scope | SHOULD | 2.3 |
| T3 | Access tokens audience-restricted to specific RS | SHOULD | 2.3 |
| T4 | Tokens not passed in URI query parameters | MUST NOT | 2.2 |

### Refresh Tokens
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| RT1 | Public client refresh tokens: sender-constrained OR rotated | MUST | 2.2 |
| RT2 | Refresh tokens bound to scope and resource server | MUST | 2.2 |
| RT3 | Refresh token replay detected (via constraining or rotation) | MUST | 2.2 |

### Client Authentication
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| CA1 | Client authentication enforced where feasible | SHOULD | 2.5 |
| CA2 | Asymmetric methods preferred (mTLS, private_key_jwt) | RECOMMENDED | 2.5 |

### Mix-Up Prevention
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| M1 | Clients with multiple AS: use iss parameter (RFC 9207) or distinct redirect URIs | MUST | 2.1, 4.4 |

### Transport & Headers
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| TH1 | End-to-end TLS | RECOMMENDED | 2.6 |
| TH2 | Reverse proxies sanitize inbound security headers | MUST | 4.13 |
| TH3 | No HTTP 307 redirects with credentials | MUST NOT | 4.12 |
| TH4 | Referrer-Policy: no-referrer on auth endpoints | RECOMMENDED | 4.2 |
| TH5 | No third-party resources on authorization pages | SHOULD NOT | 4.2 |

### In-Browser Security
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| B1 | postMessage origin/source strictly verified | MUST | 4.17 |
| B2 | CORS not supported at authorization endpoint | MUST NOT | 2.6 |
| B3 | CORS may be supported at token endpoint (direct-access) | MAY | 2.6 |

### Authorization Server Metadata
| # | Requirement | Level | RFC § |
|---|-------------|-------|-------|
| AS1 | Publish metadata per RFC 8414 | RECOMMENDED | 2.6 |
| AS2 | Publish code_challenge_methods_supported | SHOULD | 2.6 |
| AS3 | Clients use metadata for configuration | RECOMMENDED | 2.6 |

## Example

User: "review our OAuth client implementation for security"

1. Ask: "Is this a public or confidential client? Does it use multiple authorization servers?"
2. Locate auth flow: redirect URI handling, PKCE generation, token storage
3. Walk through checklist focusing on client requirements (R1–R4, P1/P2, C1, T4, RT1–RT3)
4. Check for deprecated grants (D1, D2)
5. Report findings with code locations and RFC section citations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jtdowney) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
