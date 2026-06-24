---
name: flutter-rust-bridge
description: Main text is split into `references/`, with a short summary for each section Use when this capability is needed.
metadata:
  author: vartec-chs
---

# flutter_rust_bridge Skill

Main text is split into `references/`, with a short summary for each section
below.

## References

- [01. Foundations and generation](references/01-foundation-and-generation.md) -
  Core FRB workflow, generated files, project layout, and regeneration rules.
- [02. API design, async, errors, security](references/02-api-design-async-errors-security.md) -
  Bridge API shape, async usage, typed errors, and security rules for sensitive
  data.
- [03. State, naming, serialization](references/03-state-naming-serialization.md) -
  Opaque state, naming and defaults, and when to use typed FRB models versus
  manual serialization.
- [04. Opaque types, dynamic values, callbacks](references/04-opaque-dynamic-callbacks.md) -
  Opaque handles, dynamic escape hatches, Dart/Rust callbacks, and interaction
  patterns.
- [05. Lifetimes and Hoplixi boundaries](references/05-lifetimes-and-hoplixi-boundaries.md) -
  Lifetime-bearing APIs, borrow safety, and Hoplixi-specific ownership guidance.
- [06. Integration, testing, workflows](references/06-integration-testing-workflows.md) -
  Dart/Rust layering, testing strategy, and common FRB task workflows.
- [07. Code style and generated files](references/07-code-style-generated-files.md) -
  Style rules plus the safe policy for generated files.
- [08. Type mapping, returns, streams](references/08-type-mapping-returns-streams.md) -
  Rust-to-Dart type mapping, return and error behavior, and stream APIs. This is
  the main streams section.
- [09. Binary interop and custom codecs](references/09-binary-interop-and-custom-codecs.md) -
  Custom codecs, zero-copy binary transfer, and experimental UI-state utilities.
- [10. Low-level safety, cancellation, logging](references/10-low-level-safety-cancellation-logging.md) -
  Opaque lifecycle internals, cancellation, progress streams, logging, and
  review checklists.

Open the appropriate file depending on which part of the skill you're working
on.

---
> Source: [vartec-chs/Hoplixi](https://github.com/vartec-chs/Hoplixi) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
