---
name: gemini
description: Use when the user asks to run Gemini CLI (gemini -p, gemini -r) or references Google Gemini for code analysis, refactoring, codebase investigation, or multi-model collaboration
metadata:
  author: integralmedia1
---

# Gemini CLI Skill

## Running a Task
1. Ask the user (via `AskUserQuestion`) which model to run (`gemini-3.1-pro-preview`, `gemini-3-pro-preview`, `gemini-3-flash-preview`, `gemini-2.5-pro`, or `gemini-2.5-flash`) AND which approval mode to use (`yolo`, `auto_edit`, `plan`, or `default`) in a **single prompt with two questions**.
2. Select the approval mode required for the task; default to `--approval-mode plan` unless edits or tool execution are necessary.
3. Assemble the command with the appropriate options:
   - `-m, --model <MODEL>`
   - `--approval-mode <yolo|auto_edit|plan|default>`
   - `-o, --output-format <text|json|stream-json>`
   - `-s, --sandbox` (isolate tool execution; uses gVisor on Linux, Seatbelt on macOS)
   - `--include-directories <DIR>` (additional workspace directories for multi-project context)
   - `-p, --prompt` (non-interactive/headless mode)
   - `"your prompt here"` (as the prompt value)
4. When continuing a previous session, use `gemini -r latest -p "follow-up prompt"`. When resuming, don't use configuration flags unless explicitly requested by the user (e.g. if they specify the model when requesting to resume). To resume a specific session: `gemini -r <INDEX> -p "prompt"`.
5. **IMPORTANT**: By default, append `2>/dev/null` to all `gemini` commands to suppress progress/debug output (stderr). Only show stderr if the user explicitly requests debug output (`-d` flag) or if troubleshooting is needed.
6. For prompts containing special characters, use `printf` for safe piping:
   ```bash
   printf '%s' "Your prompt with 'quotes' and $pecial chars" | gemini -m <MODEL> --approval-mode <MODE> -o text 2>/dev/null
   ```
   Alternatively, use `-p` with a heredoc:
   ```bash
   gemini -m <MODEL> --approval-mode <MODE> -o text -p "$(cat <<'PROMPT'
   Your multi-line prompt here.
   PROMPT
   )" 2>/dev/null
   ```
7. Run the command, capture stdout (stderr filtered as appropriate), and summarize the outcome for the user.
8. **After Gemini completes**, inform the user: "You can resume this Gemini session at any time by saying 'gemini resume' or asking me to continue with additional analysis or changes."

### Quick Reference
| Use case | Approval mode | Key flags |
| --- | --- | --- |
| Read-only review or analysis | `plan` | `--approval-mode plan -o text 2>/dev/null` |
| Apply local edits | `auto_edit` | `--approval-mode auto_edit -o text 2>/dev/null` |
| Full delegation (auto-approve all) | `yolo` | `--approval-mode yolo -o text 2>/dev/null` |
| Sandboxed execution | `yolo` | `-s --approval-mode yolo -o text 2>/dev/null` |
| Codebase investigation | `yolo` | `--approval-mode yolo -o text 2>/dev/null` (uses built-in investigator) |
| Multi-project context | Match task needs | `--include-directories <DIR> 2>/dev/null` |
| Resume recent session | Inherited from original | `-r latest -p "prompt" 2>/dev/null` |
| Structured output | Match task needs | `-o json 2>/dev/null` |

## Following Up
- After every `gemini` command, immediately use `AskUserQuestion` to confirm next steps, collect clarifications, or decide whether to resume with `gemini -r latest`.
- When resuming, pipe the new prompt via `-p`: `gemini -r latest -p "new prompt" 2>/dev/null`. The resumed session automatically uses the same model and approval mode from the original session.
- Restate the chosen model, approval mode, and any special flags when proposing follow-up actions.

## Critical Evaluation of Gemini Output

Gemini is powered by Google models with their own knowledge cutoffs and limitations. Treat Gemini as a **colleague, not an authority**.

### Guidelines
- **Trust your own knowledge** when confident. If Gemini claims something you know is incorrect, push back directly.
- **Research disagreements** using WebSearch or documentation before accepting Gemini's claims. Share findings with Gemini via resume if needed.
- **Remember knowledge cutoffs** - Gemini may not know about recent releases, APIs, or changes that occurred after its training data.
- **Don't defer blindly** - Gemini can be wrong. Evaluate its suggestions critically, especially regarding:
  - Model names and capabilities
  - Recent library versions or API changes
  - Best practices that may have evolved

### When Gemini is Wrong
1. State your disagreement clearly to the user
2. Provide evidence (your own knowledge, web search, docs)
3. Optionally resume the Gemini session to discuss the disagreement. **Identify yourself as Claude** so Gemini knows it's a peer AI discussion. Use your actual model name (e.g., the model you are currently running as) instead of a hardcoded name:
   ```bash
   printf '%s' "This is Claude (<your current model name>) following up. I disagree with [X] because [evidence]. What's your take on this?" | gemini -r latest -o text 2>/dev/null
   ```
4. Frame disagreements as discussions, not corrections - either AI could be wrong
5. Let the user decide how to proceed if there's genuine ambiguity

## Error Handling
- Stop and report failures whenever `gemini --version` or a `gemini -p` command exits non-zero; request direction before retrying.
- If execution fails with `MODEL_CAPACITY_EXHAUSTED` (429) → wait 30 seconds and retry, max 3 attempts.
- Before you use high-impact flags (`--approval-mode yolo`, `-s` sandbox) ask the user for permission using AskUserQuestion unless it was already given.
- When output includes warnings or partial results, summarize them and ask how to adjust using `AskUserQuestion`.

---
> Source: [integralmedia1/skill-gemini](https://github.com/integralmedia1/skill-gemini) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
