---
name: kubernetes-integration
description: >- Use when this capability is needed.
metadata:
  author: adaptive-enforcement-lab
---

# Kubernetes Integration

## When to Use This Skill

A well-designed Kubernetes CLI works seamlessly both on developer laptops and inside cluster pods. This section covers:

- **[Client Configuration](client-configuration.md)** - Automatic config detection for all environments
- **[RBAC Setup](rbac-setup.md)** - Service accounts and permissions
- **[Common Operations](common-operations/index.md)** - List, patch, and restart resources

---


## Implementation

*See [examples.md](examples.md) for detailed code examples.*

---


## Key Principles

| Practice | Description |
| ---------- | ------------- |
| **Use contexts everywhere** | Pass `context.Context` to all Kubernetes operations |
| **Handle cancellation** | Respect context cancellation for clean shutdowns |
| **Wrap errors with context** | Include resource type and name in error messages |
| **Default to current namespace** | Match kubectl behavior for namespace resolution |
| **Support both configs** | Always handle in-cluster and out-of-cluster scenarios |
| **Minimal RBAC** | Request only the permissions your CLI needs |

---

*Build clients that work everywhere: laptop, CI runner, or pod.*


## Examples

See [examples.md](examples.md) for code examples.
## References

- [Source Documentation](https://adaptive-enforcement-lab.com/build/go-cli-architecture/)
- [AEL Build](https://adaptive-enforcement-lab.com/build/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptive-enforcement-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
