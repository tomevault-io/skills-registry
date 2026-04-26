---
name: osworld-execute
description: Execute Python code (typically pyautogui commands) in the OSWorld environment. Returns execution result with success status, return code, duration, stdout, and stderr. Use when this capability is needed.
metadata:
  author: bdambrosio
---

# OSWorld Execute Tool (Level 4)

## Input
- `python` or `value`: Python code string (required) - typically pyautogui commands
- `return_observation`: bool (default: false) - include observation in response
- `value` parameter can be used as alternative to `python`

## Output
- Note ID (bound to `out` variable) containing:
  - `text`: formatted execution result
  - `format`: "text"
  - `metadata`: execution data including:
    - `success`: boolean - whether execution succeeded
    - `returncode`: integer - return code (0 = success)
    - `duration_ms`: integer - execution duration in milliseconds
    - `step_counter`: integer - step counter after execution
    - `stdout`: string - standard output
    - `stderr`: string - standard error
    - `python_code`: string - the executed code
    - `observation`: dict (if return_observation=true) - observation after execution
    - `timestamp`: float (if return_observation=true) - observation timestamp

## Configuration
- `OSWORLD_URL` environment variable (defaults to `http://localhost:3002`)
- Or pass `osworld_url` in character config's `osworld_config` section

## Common Workflow
```json
{"type":"osworld-observe","out":"$obs"}
{"type":"osworld-execute","python":"pyautogui.click(100,200)","out":"$result"}
{"type":"osworld-observe","out":"$obs2"}
```

## Notes
- Python code is executed directly in the OSWorld environment
- Common commands: `pyautogui.click(x, y)`, `pyautogui.typewrite(text)`, `pyautogui.press(key)`
- No retries or corrections - Jill owns error handling
- Execution is synchronous and blocking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
