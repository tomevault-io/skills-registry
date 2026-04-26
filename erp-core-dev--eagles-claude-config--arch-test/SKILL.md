---
name: arch-test
description: Generate architecture tests to enforce structural rules Use when this capability is needed.
metadata:
  author: erp-core-dev
---

# Architecture Tests

Generate tests that enforce architectural rules and prevent drift.

## For .NET (NetArchTest)
```bash
dotnet add package NetArchTest.Rules
```
```csharp
[Fact]
public void Controllers_ShouldNotReference_Repositories()
{
    Types.InAssembly(typeof(Program).Assembly)
        .That().ResideInNamespace("Controllers")
        .ShouldNot().HaveDependencyOn("Repositories")
        .GetResult().IsSuccessful.Should().BeTrue();
}

[Fact]
public void Services_ShouldHave_InterfacePrefix()
{
    Types.InAssembly(typeof(Program).Assembly)
        .That().ResideInNamespace("Services.Interfaces")
        .Should().HaveNameStartingWith("I")
        .GetResult().IsSuccessful.Should().BeTrue();
}
```

## Arguments
- `--rules=<layers|naming|dependencies>`: Which rules to generate

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erp-core-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
