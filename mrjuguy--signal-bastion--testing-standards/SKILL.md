---
name: testing-standards
description: Standards for Automation Testing within Unreal Engine (C++/Blueprint), NOT Python. Use when this capability is needed.
metadata:
  author: mrjuguy
---

# Unreal Engine Testing Standards

## 1. Test Framework

- **Primary Tool**: **Unreal Automation Framework** (Session Frontend -> Automation).
- **Test Categories**:
  - `EAutomationTestFlags::EditorContext`: Requires Editor (Assets open).
  - `EAutomationTestFlags::ClientContext`: PIE/Simulate.
  - `EAutomationTestFlags::ProductFilter`: Categorized as "Functional/Smoke/Product".

## 2. Test Structure (C++)

- Define tests using `IMPLEMENT_SIMPLE_AUTOMATION_TEST(FMyTestNamespacedClassName, "Project.Category.TestName", EAutomationTestFlags::EditorContext | ...)`
- **Assertions**:
  - `TestTrue("Message", Condition)`
  - `TestFalse("Message", Condition)`
  - `TestEqual("Message", Actual, Expected)`
  - `TestNotNull("Message", ObjectPtr)`

## 3. Blueprint Automation

- Use **Functional Test Actor** (`AFunctionalTest`).
- Extend `FunctionalTest` in Blueprint.
- Use `BeginPlay` -> Setup -> `OnTick` / `Wait` -> Assert -> `FinishTest(Passed/Failed)`.

## 4. CI/CD Integration

- Run tests via Command Line:
  - `UnrealEditor-Cmd.exe "Project.uproject" -ExecCmds="Automation RunTests Project.Category; Quit" -log`
- Fail build if any `Error` is logged.

## 5. Scope

- **Smoke Tests**: Does the map load? Does the player pawn spawn?
- **Logic Tests**: Does `TakeDamage()` reduce HP correctly?
- **Replication Tests**: Does Client B see what Server A did? (Requires Multi-Process PIE tests).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrjuguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
