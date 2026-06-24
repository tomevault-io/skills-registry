---
name: libsecret
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libsecret Skill

## When to Use

- Generating cryptographic secrets for APIs
- Creating and signing JWTs
- Managing .env file variables
- Creating deterministic hashes from values

## Key Concepts

**Secret generation**: Cryptographically secure random string generation for API
keys and tokens.

**JWT creation**: HS256-signed JSON Web Tokens for authentication.

**Environment management**: Read/write .env files programmatically.

## Usage Patterns

### Pattern 1: Generate secrets

```javascript
import { generateSecret, generateSecretB64 } from "@copilot-ld/libsecret";

const apiKey = generateSecret(); // hex string
const token = generateSecretB64(); // base64url string
```

### Pattern 2: Manage .env variables

```javascript
import { getEnvVar, setEnvVar } from "@copilot-ld/libsecret";

await setEnvVar(".env", "API_SECRET", secret);
const value = await getEnvVar(".env", "API_SECRET");
```

### Pattern 3: Create JWT

```javascript
import { createJwt } from "@copilot-ld/libsecret";

const jwt = createJwt({ userId: "123" }, secret, { expiresIn: "1h" });
```

## Integration

Used during setup scripts and initialization. Called by scripts/env-secrets.js.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
