---
name: review-plugin-claude
description: Automated code review for plugin implementations against AnimalAL project standards. Analyzes nested plugins for pattern compliance (NestedStockPlugin structure), IDisposable, IInternalCallTracker, ChatHistory parameter, logger usage, error handling, description attributes, thread-safe call tracking, lazy initialization, StandardKernel registration, and test coverage. Use when the user says /review-plugin, "review plugin", "review NestedXPlugin", or asks to audit or check plugin code against project standards. Use when this capability is needed.
metadata:
  author: dezverev
---

# Plugin Code Review

Reviews plugin implementations against project standards. Reference implementation: `src/Plugins/NestedKernel/SK/NestedStockPlugin.cs`.

## When to Apply

- User says `/review-plugin <PluginName>`, `/review-plugin --all --nested`, or `/review-plugin <Name> --suggest-fixes`
- User asks to "review plugin", "audit plugin code", or "check plugin against standards"
- User asks whether a plugin follows the nested plugin pattern

## Usage Examples

| Command / request | Action |
|-------------------|--------|
| `/review-plugin NestedCalendarPlugin` | Review only `NestedCalendarPlugin.cs` |
| `/review-plugin --all --nested` | Review all nested plugins in `src/Plugins/NestedKernel/SK/` |
| `/review-plugin CalendarPlugin --suggest-fixes` | Review and include concrete fix suggestions (code/location) |

## Review Workflow

1. **Identify scope**: Single plugin (e.g. `NestedCalendarPlugin`), or all nested plugins when `--all --nested` is indicated.
2. **Load reference**: Use `NestedStockPlugin.cs` as the canonical structure.
3. **Run checklist** (see below) and note pass/fail/warnings per item.
4. **Check registration**: In `src/AnimalKernel/Core/StandardKernel.cs`, method `AddNestedKernelPlugins`. Verify the plugin is instantiated, added via `builder.Plugins.AddFromObject(..., "AgentName")`, added to `_pluginInstances["AgentName"]`, and to `_disposablePlugins`.
5. **Check test coverage**: In `src/IntegrationTesterApp/TestCaseDefinitions.cs`, search for the agent name (e.g. `StockAgent`, `WeatherAgent`) in `ExpectedToolsToCall` or test `Name`/`Prompt`. Count scenarios; recommend at least happy-path, error-handling, and conversation-context tests where relevant.
6. **Emit report** using the Report Format below. If `--suggest-fixes` was requested, include concrete code snippets or file/location for each fix.

## Compliance Checklist

Compare the plugin under review to `NestedStockPlugin.cs`. Mark ✓ (pass), ✗ (fail), or ⚠️ (partial/warning).

| Check | Requirement | Where to Look |
|-------|-------------|---------------|
| **Nested pattern** | Class in `AnimalAI.Plugins.NestedKernel.SK`, internal kernel built in private method, Lazy-init | Class declaration, `CreateInternalKernel`, `AddPlugins` |
| **IDisposable** | Class declares `IDisposable`; `Dispose()` disposes `_serviceProvider`; comment that Kernel is not disposed | Class declaration, `#region Dispose` |
| **IInternalCallTracker** | Class declares `IInternalCallTracker`; has `TrackInternalCall` and `GetAndClearInternalCalls`; thread-safe with lock | Class declaration, `#region Internal Call Tracking` |
| **Tracking fields** | `List<InternalFunctionCall> _internalCalls` and `object _internalCallsLock`; lock used in both interface methods | `#region Tracking Fields` |
| **InternalFunctionCallTracker** | After `builder.Build()`, `kernel.AutoFunctionInvocationFilters.Add(new InternalFunctionCallTracker(this, loggerFactory))` | End of `CreateInternalKernel()` |
| **ChatHistory parameter** | Main `[KernelFunction]` method has `ChatHistory? ChatHistory = null` (or same name used by `KernelConstants.ChatHistoryArgumentKey`); comment that it is injected via filter | Kernel function signature |
| **Logger usage** | `ILoggerFactory?`, `ILogger<NestedXPlugin>? _logger`; `_logger?.LogInformation` for function call/result, `_logger?.LogError` in catch | Constructor, kernel function |
| **Lazy&lt;Kernel&gt;** | `Lazy<Kernel> _internalKernel`; created in constructor as `new Lazy<Kernel>(() => CreateInternalKernel())`; never built in ctor | `#region Kernel Fields`, constructor |
| **Error handling** | try/catch around kernel invocation; on exception, log and return a string (e.g. `$"Error calling SubKernel: {ex.Message}"`) | Kernel function body |
| **Description attributes** | `[Description("...")]` on the kernel function; describes *when* to use and *what* it does; `[return: Description("...")]` and param descriptions | Above kernel function and parameters |
| **Regions** | Logical regions used: Kernel Fields, Logger Fields, Tracking Fields, Other Fields, Constructors, AddPlugins, Kernel Functions, Internal Call Tracking, Dispose | File structure |
| **Registration** | In `StandardKernel.AddNestedKernelPlugins`: instance created, `AddFromObject(instance, "AgentName")`, `_pluginInstances["AgentName"] = instance`, `_disposablePlugins.Add(instance)` | `StandardKernel.cs` |
| **Test coverage** | At least one test with `ExpectedToolsToCall` containing `"AgentName.MethodName"`; ideally error and context tests | `TestCaseDefinitions.cs` |

## Description Quality

Descriptions should enable accurate LLM tool selection. Prefer:

- **When**: "Use when the user asks about X" or "Use ONLY when…" with clear triggers.
- **What**: What the tool returns and that "the returned response MUST be used in your answer".
- **Params**: Describe format (e.g. "MUST INCLUDE COORDINATES") where it affects behavior.

Short or generic descriptions (e.g. "Gets calendar information") count as ⚠️; suggest a fuller sentence covering when and what.

## Report Format

Use this structure. Include "Apply Fixes" / "View Details" / "Skip" only if the environment supports those actions; otherwise omit that line.

```markdown
## Plugin Review: <PluginClassName>

### Compliance Status: <✓ Good | ⚠️ N% (Good/Fair) | ✗ Needs Work>

<One line per checklist item: ✓ / ✗ / ⚠️ and short label>

### Issues Found:

<For each issue:>
<number>. **<Title>** (<High|Medium|Low> Priority)
   - <One-line finding>
   - <Suggested change or "Add: …" / "Implement: …" / "Location: Line N">
   - Location: Line <N> (or "In StandardKernel.cs AddNestedKernelPlugins" / "In TestCaseDefinitions.cs")

### Recommendations:

- <Bullet list of concrete next steps>

[Apply Fixes] [View Details] [Skip]
```

## Scope Options

| User request | Scope |
|--------------|--------|
| `/review-plugin NestedCalendarPlugin` | Only `NestedCalendarPlugin.cs` (or the one named). |
| `/review-plugin --all --nested` | All classes in `src/Plugins/NestedKernel/SK/` that match the nested pattern (e.g. `Nested*Plugin.cs`). Report per plugin, then a short summary. |
| `--suggest-fixes` | For each issue, add a "Suggested change" with code snippet or exact edit where helpful. |

## File Locations

- **Reference plugin**: `src/Plugins/NestedKernel/SK/NestedStockPlugin.cs`
- **Tracker interface/impl**: `src/Plugins/NestedKernel/SK/InternalFunctionCallTracker.cs` (`IInternalCallTracker`, `InternalFunctionCall`, `InternalFunctionCallTracker`)
- **Registration**: `src/AnimalKernel/Core/StandardKernel.cs` → `AddNestedKernelPlugins`, `_pluginInstances`, `_disposablePlugins`
- **Tests**: `src/IntegrationTesterApp/TestCaseDefinitions.cs` — tool names are `"AgentName.MethodName"` (e.g. `"StockAgent.AskStockAgent"`). Agent name must match the name used in `AddFromObject(..., "AgentName")`.

## Example Snippets for Fixes

**Missing IInternalCallTracker**

- Class: `public class NestedXPlugin : IDisposable, IInternalCallTracker`
- Add the tracking region and interface implementation from `NestedStockPlugin.cs` (#region Internal Call Tracking).
- In `CreateInternalKernel()`, after `var kernel = builder.Build();`, add:  
  `kernel.AutoFunctionInvocationFilters.Add(new InternalFunctionCallTracker(this, loggerFactory));`

**Weak description**

- Current: `[Description("Gets calendar information")]`
- Suggested: `[Description("Use when the user asks about their calendar, schedule, meetings, or events. Returns calendar events and appointments. The returned response MUST be used in your answer to the user.")]`

**Registration**

- In `AddNestedKernelPlugins`: create instance, call `builder.Plugins.AddFromObject(instance, "AgentName")`, set `_pluginInstances["AgentName"] = instance`, and `_disposablePlugins.Add(instance)`.

## Sample Report (Target Style)

```markdown
## Plugin Review: NestedCalendarPlugin

### Compliance Status: ⚠️ 85% (Good)

✓ Follows nested plugin pattern
✓ IDisposable implemented correctly
✓ Lazy<Kernel> initialization
✓ ChatHistory parameter present
✗ Missing internal call tracking
⚠️ Description could be more specific

### Issues Found:

1. **Missing IInternalCallTracker** (High Priority)
   - NestedCalendarPlugin should implement IInternalCallTracker
   - Add: `public class NestedCalendarPlugin : IDisposable, IInternalCallTracker`
   - Implement: TrackInternalCall() and GetAndClearInternalCalls()
   - Location: Line 19

2. **Description Clarity** (Medium Priority)
   - Current: "Gets calendar information"
   - Suggested: "Gets calendar events, appointments, and schedule information. Use whenever the user asks about their calendar, schedule, meetings, or events."
   - Location: Line 98

3. **Test Coverage** (Low Priority)
   - Found 2 test cases in TestCaseDefinitions.cs
   - Recommended: Add test for error handling with invalid dates
   - Add test for conversation history context

### Recommendations:
- Add internal call tracking to monitor nested tool usage
- Enhance description for better LLM routing decisions
- Increase test coverage to 5+ scenarios
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
