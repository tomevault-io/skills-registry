---
name: ramain-browser-automator
description: From a structured task spec (goal, start URL, allowed domains, sensitive-data keys), generate a ready-to-run browser-use Python script with safety scaffolding — allowed-domain enforcement and environment-variable-based credential injection — baked in from the start. Use when this capability is needed.
metadata:
  author: riteshkew
---

# Workflow

When this skill triggers, follow these steps in order.

## Step 1 — Capture the task spec

Ask the user for:

- **goal** — plain-English description of what the browser agent should accomplish
- **startUrl** — the URL to open first (e.g. `https://example.com`)
- **allowedDomains** — one or more domains the agent is permitted to navigate to (prevents scope creep)
- **sensitiveDataKeys** (optional) — names of environment variables that hold credentials (e.g. `LOGIN_EMAIL`, `LOGIN_PASSWORD`); their values will never be hard-coded in the script
- **maxSteps** (optional) — maximum number of agent steps before the run is aborted (default: 20)

Example spec:

```json
{
  "goal": "Log in to the dashboard and download the latest invoice as a PDF.",
  "startUrl": "https://app.example.com/login",
  "allowedDomains": ["app.example.com"],
  "sensitiveDataKeys": ["EXAMPLE_EMAIL", "EXAMPLE_PASSWORD"],
  "maxSteps": 15
}
```

If the user is unsure, suggest the worked scenario in `examples/input.md`.

## Step 2 — Save the spec and run the generator

Save the spec as a JSON file, then run from the skill root (`skills/ramain-browser-automator/`):

```bash
node scripts/gen-task.mjs path/to/task.json > path/to/agent_task.py
```

The engine emits a valid Python script to stdout. Redirect it to a `.py` file.

The generated script:

- Imports `Agent` and `Browser` from `browser_use`, plus an async LLM client
- Builds a `sensitive_data` dict by reading each key from `os.environ` (never hard-coded)
- Configures the browser with `allowed_domains` from the spec
- Sets the agent `task` to the goal string
- Caps execution with a `max_steps` guard
- Wraps everything in an `asyncio.run(main())` entrypoint

## Step 3 — Tell the user how to run the generated script

The generated script is ready to execute but requires:

1. `browser-use` installed in a Python environment:
   ```bash
   pip install browser-use
   playwright install chromium
   ```
2. A supported LLM provider's API key set in the environment (e.g. `OPENAI_API_KEY`).
3. Any sensitive-data env vars the script reads (listed in the `sensitive_data` block).

Then run:

```bash
python agent_task.py
```

This skill only generates the script. Network access, a real browser, and an LLM key are required at runtime and are out of scope for this skill.

## Step 4 — Iterate

Common refinements:

- Narrow `allowedDomains` to reduce blast radius
- Add more `sensitiveDataKeys` to avoid any credential appearing in plain text
- Lower `maxSteps` for faster fail-fast behavior
- Adjust the goal wording for precision

Update the spec JSON and rerun Step 2 to regenerate.

## Example

See `examples/input.md` for a worked e-commerce checkout scenario and `examples/agent_task.py` for the generated script.

Run the example yourself:

```bash
cd skills/ramain-browser-automator
bash examples/run.sh
```

The script generates `examples/agent_task.py` and writes a verification report to `examples/output.md`.

---
> Source: [riteshkew/yc-skills](https://github.com/riteshkew/yc-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
