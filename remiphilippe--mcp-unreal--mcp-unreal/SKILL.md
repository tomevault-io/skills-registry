---
name: ue-test-patterns
description: Patterns for writing and running UE 5.7 automation tests Use when this capability is needed.
metadata:
  author: remiphilippe
---
## UE 5.7 Test Execution
- Headless: `UnrealEditor-Cmd <project> -ExecCmds="Automation RunTests <filter>;Quit" -nullrhi -unattended -nosplash -ABSLOG=<path>`
- Parse "Test Completed." lines: `Result={Success|Fail}` and `Path={}`
- Failure details between `BeginEvents` / `EndEvents` markers
- Exit codes unreliable on macOS — always parse logs
- Add `sleep 1` after test for log flush on macOS

## Test Naming
- `ProjectName.Unit.*` — headless unit tests
- `ProjectName.Visual.*` — GPU visual tests
- `ProjectName.Integration.*` — integration tests

---
> Source: [remiphilippe/mcp-unreal](https://github.com/remiphilippe/mcp-unreal) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
