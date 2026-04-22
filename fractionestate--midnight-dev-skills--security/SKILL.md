---
name: security
description: Security auditing patterns for Midnight Network smart contracts and dApps. Use when reviewing code for vulnerabilities, privacy leaks, cryptographic weaknesses, or performing security audits. Use when this capability is needed.
metadata:
  author: fractionestate
---

# Security Auditing for Midnight Network

Expert knowledge for auditing Midnight Network contracts and privacy-preserving applications.

## Security Priorities

1. **Privacy Protection** - Ensure sensitive data stays private
2. **Cryptographic Integrity** - Verify commitments, nullifiers, proofs
3. **Access Control** - Validate authorization patterns
4. **Input Validation** - Check all assertions and bounds
5. **State Safety** - Prevent manipulation and reentrancy

## Severity Classification

| Level    | Icon | Description                     | Examples                   |
| -------- | ---- | ------------------------------- | -------------------------- |
| Critical | 🔴   | Funds at risk, privacy broken   | Witness exposure, key leak |
| High     | 🟠   | Significant leak or bypass      | Predictable nullifier      |
| Medium   | 🟡   | Logic errors, incomplete checks | Missing validation         |
| Low      | 🟢   | Best practice violations        | Poor error messages        |
| Info     | ℹ️   | Improvement suggestions         | Code clarity               |

## Quick Checklist

### Compact Contracts

- [ ] All assertions have descriptive messages
- [ ] Sensitive data uses `witness` or `secret`
- [ ] No plaintext secrets in ledger
- [ ] Commitments use salt (hash2)
- [ ] Nullifiers include secret context
- [ ] Range checks before arithmetic
- [ ] Access control where needed

### TypeScript dApps

- [ ] Wallet availability checked
- [ ] Transactions properly confirmed
- [ ] No secrets logged or exposed
- [ ] Private state encrypted
- [ ] Error boundaries in place
- [ ] HTTPS enforced

## References

- [references/vulnerabilities.md](references/vulnerabilities.md) - Common vulnerability patterns

## Assets

- [assets/audit-report.md](assets/audit-report.md) - Audit report template

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fractionestate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
