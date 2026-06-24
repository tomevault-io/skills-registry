---
name: dotnet
description: Work on the .NET solution for SphereIntegrationHub. Use this when editing services, components, JSON data, or tests. Use when this capability is needed.
metadata:
  author: pinedatec-eu
---

# .NET

## Main structure

- CLI tool: `src/SphereIntegrationHub` (Application).
- Tests: `tests/SphereIntegrationHub.cli.tests` (xUnit).

## Considerations

- If a method has a `CancellationToken`, it must be the last parameter in the method signature.
- SOLID principles must be followed.
- Variables must be descriptive and clear, without being excessively long.
- The order of methods in classes must be: public, protected, then private.
- Magic numbers, strings, etc. must be avoided. Use `private const` when elements are exclusive to a class, or, if shared, a `XxxxConsts` class with `internal const` members and reduced visibility.
- When possible, components should use a fluent style for construction/configuration.
- Methods must have clearly bounded responsibilities; actions should be focused and the code readable, split into small chunks (ideally no more than 50 lines of code).

## Tests

- Reusable, associated fixtures must be used, with methods that create common elements for multiple tests, reducing complexity and improving readability.
- Tests must be unit tests, using mocks or WireMock (for endpoints).
- Interfaces of the elements under test must be used, for example:  
  `IWorkflowExecutor executor = new WorkflowExecutor()`, ensuring that the interface is fully implemented and complete.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pinedatec-eu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
