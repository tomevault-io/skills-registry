---
name: animal-kernel-web-dev
description: Starts the AnimalKernel.Web site via run-dev script, uses Context7 for React best practices and Cursor's built-in browser (cursor-ide-browser) for browser-driven development and testing. Use when building or testing the clientapp UI, when the user invokes animal-kernel-web-dev with a follow-up task, or when developing the AnimalKernel.Web frontend with expert UX and browser verification. Use when this capability is needed.
metadata:
  author: dezverev
---

# Animal Kernel Web Dev

Develops and tests the AnimalKernel.Web clientapp (Vite + React) by starting the site, fetching React best practices from Context7, implementing changes with an expert UX stance, and verifying behavior in the browser via Cursor's built-in browser (cursor-ide-browser). The user's follow-up prompt after invoking the skill is the development task to implement and verify.

## When to Apply

- User invokes the animal-kernel-web-dev skill with a follow-up task (e.g. "animal-kernel-web-dev: add a dark mode toggle")
- User asks to build or test the AnimalKernel.Web clientapp UI
- User wants to develop the AnimalKernel.Web frontend with browser-driven verification
- Task involves the clientapp under `src/AnimalKernel.Web/clientapp` and benefits from React best practices and live browser checks

## Persona and Principles

When applying this skill, adopt:

- **Expert, fit-and-finish**: Prioritize polish, consistent spacing, typography, and clear visual hierarchy.
- **Clean minimalistic UX**: Prefer simple layouts and controls; avoid clutter; use progressive disclosure (e.g. advanced options in a secondary panel or behind "Show more").
- **Power users**: Keep debug and deeper information available but secondary (e.g. collapsible debug pane, optional "Show technical details"). Balance "simple by default" with "everything available when needed."

## Discovery

- Treat the user message that invoked the skill (and any follow-up) as the development task.
- If the task is ambiguous, ask one short clarifying question before starting the site and implementation.

## Workflow

### 1. Parse task

Use the user message that invoked the skill (and any follow-up) as the development task. If ambiguous, ask one short clarifying question.

### 2. Start the site

- **From repo root**: Run `./src/AnimalKernel.Web/run-dev.sh` (Linux/macOS) or `.\src\AnimalKernel.Web\run-dev.ps1` (Windows).
- Run the script in the background (or a separate terminal) so the agent can continue. Do not block on the script.
- Wait until the app is reachable: poll `http://localhost:5173` (Vite) or rely on the script's "Backend is up" message plus a short delay for Vite to start.
- If the site is already running (e.g. ports 5000 and 5173 in use), skip starting the script and proceed to step 3.

### 3. Fetch React (and related) guidance

- Use **user-context7** MCP:
  1. Call **resolve-library-id** with `libraryName: "React"` and `query` derived from the user task.
  2. Call **query-docs** with the returned `libraryId` and a focused query (e.g. best practices for the UI pattern or hook in question).
- Optionally resolve "Vite" for build or dev best practices if the task involves tooling or config.
- Limit Context7 calls to 1–3 per task.

### 4. Implement and refine

- Edit code under `src/AnimalKernel.Web/clientapp` (and backend under `src/AnimalKernel.Web` only if the task requires it).
- Apply the expert persona: minimalistic UX, fit-and-finish, debug info available for power users.
- Use Context7 results to follow React best practices.

### 5. Verify in the browser

- Use **cursor-ide-browser** MCP (Cursor's built-in browser):
  1. **browser_navigate** to `http://localhost:5173` (Vite dev server).
  2. Use **browser_snapshot** to inspect structure, then **browser_click**, **browser_fill_form**, **browser_type**, or **browser_take_screenshot** as needed to verify the task and UX.
- Before first use of browser tools, read the tool descriptors from `mcps/cursor-ide-browser/tools/` (or `.cursor/projects/<project>/mcps/cursor-ide-browser/tools/`), per GeneralDevGuide. Call tools with arguments matching the schema.
- **Lock/unlock**: If interacting with the page (click, type, etc.), call **browser_lock** after navigation (or when a tab already exists); call **browser_unlock** when done with all browser operations for the turn.

### 6. Cleanup (when requested or when concluding)

- **When the user asks to stop the dev servers**: Kill the run-dev process. Find processes using ports 5000 and 5173 (e.g. `lsof -i :5000 -i :5173` or `rg`/task manager), or kill the background terminal process that ran `run-dev.sh` / `run-dev.ps1`. On Linux/macOS you can `pkill -f "run-dev.sh"` or kill the dotnet and node processes for the Web project and Vite.
- **When the user asks to close browser tabs**: Use **cursor-ide-browser** to close or leave tabs as requested (e.g. **browser_tabs** to list, then close if the tool supports it).
- At the end of an animal-kernel-web-dev session, if the user has not asked to keep servers running, offer to stop the dev servers and close verification tabs, or perform cleanup if the user previously asked to "stop when done."

### 7. Report

Summarize what was built or changed, how it was tested in the browser (e.g. "Clicked X, verified Y"), and any follow-up suggestions. If cleanup was performed, note that servers were stopped and/or tabs closed.

## References

- **Start script**: [src/AnimalKernel.Web/run-dev.sh](src/AnimalKernel.Web/run-dev.sh), [src/AnimalKernel.Web/run-dev.ps1](src/AnimalKernel.Web/run-dev.ps1). Backend starts first (port 5000), then clientapp (port 5173).
- **Client app**: [src/AnimalKernel.Web/clientapp/](src/AnimalKernel.Web/clientapp/) (Vite + React). [vite.config.ts](src/AnimalKernel.Web/clientapp/vite.config.ts) proxies `/api` and `/chathub` to the backend.
- **MCP**: Context7 tools `resolve-library-id`, `query-docs`. Cursor browser (cursor-ide-browser) tools: `browser_navigate`, `browser_snapshot`, `browser_click`, `browser_fill_form`, `browser_type`, `browser_take_screenshot`, `browser_lock`, `browser_unlock`, `browser_tabs`. List and read schemas from `mcps/cursor-ide-browser/tools/` or the project metadata path before calling.
- **GeneralDevGuide**: Always read MCP tool schema before calling any MCP tool.

## Checklist Before Delivering

- [ ] Task parsed from user message; site started (or detected already running).
- [ ] Context7 used for React (and optionally Vite) guidance; 1–3 calls.
- [ ] Code changes under clientapp (and backend only if needed); expert persona applied.
- [ ] Cursor browser (cursor-ide-browser) used to navigate to http://localhost:5173 and verify behavior.
- [ ] If user asked to stop servers or close tabs, cleanup performed; otherwise cleanup offered or noted.
- [ ] Report summarizes changes, browser verification, and follow-up suggestions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
