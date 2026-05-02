---
name: volcengine-security-kms
description: Key lifecycle management with Volcengine KMS. Use when users need key creation, rotation policies, encryption/decryption workflows, or key permission troubleshooting. Use when this capability is needed.
metadata:
  author: openclaw
---

# volcengine-security-kms

Operate KMS keys with lifecycle awareness and least-privilege access checks.

## Execution Checklist

1. Confirm key purpose, algorithm, and usage scope.
2. Create or select key and validate policy bindings.
3. Execute encrypt/decrypt/sign task.
4. Return key metadata, operation result, and audit hints.

## Safety Rules

- Never expose plaintext secrets in logs.
- Rotate keys according to policy windows.
- Validate caller permissions before key operations.

## References

- `references/sources.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
