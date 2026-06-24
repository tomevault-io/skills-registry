---
name: diagnostics-lang-skill-creator
description: > Use when this capability is needed.
metadata:
  author: tarqd
---

# Diagnostics Language Skill Creator

This skill is a guided workflow. It produces a new, packaged language-specific
diagnostics skill that extends `diagnostics-base`. It must not be run silently
or in one shot — it has explicit user checkpoints at each phase.

**Before starting:** verify that `diagnostics-base` is available in the user's
skill library. If it is not, stop and tell the user:
> "This workflow requires the `diagnostics-base` skill to be installed first.
> Please install it and then re-run this workflow."

---

## Phase Tracker

Display this tracker at the start of each phase, updating status as you go.
Use `✅` for complete, `▶` for current, `○` for pending.

```
○ Phase 1 — Detect language & application context
○ Phase 2 — Scan codebase for existing libraries
○ Phase 3 — Research: idiomatic libraries & patterns
○ Phase 4 — User decisions: library stack & application profile
○ Phase 5 — Generate skill
○ Phase 6 — Test the skill via subagents
○ Phase 7 — Review & package
```

---

## Phase 1 — Detect language & application context

**Goal:** Know the language, know whether there's an existing codebase, and
understand the application type before doing anything else.

Start by checking if the conversation or any uploaded files already tell you
the language. If the user said "create a Rust diagnostics skill", the language
is Rust — do not ask again.

If the language is not clear, ask:
> "Which programming language should this skill target?"

Then determine whether there's an existing project to scan:
- Check `bash_tool` for common project files: `package.json`, `Cargo.toml`,
  `go.mod`, `pyproject.toml`, `requirements.txt`, `pom.xml`, `build.gradle`,
  `*.csproj`.
- If found, note the project root and continue to Phase 2.
- If no project is detected, tell the user and continue — Phase 2 will be
  shorter (no scan), but the web search in Phase 3 is still required.

Also note the application type if detectable from file structure:
- CLI (`main.rs`, `cmd/`, `__main__.py`, `bin/` entry points)
- HTTP service (Express/Axum/Gin/Flask/Spring controller patterns)
- Background worker / queue consumer
- Library / crate / package (no main entry point)
- Monorepo (multiple of the above)

**Checkpoint:** Print the phase tracker (Phase 1 ✅) and a one-paragraph
summary of what you found before proceeding.

---

## Phase 2 — Scan codebase for existing libraries

**Goal:** Find every logging, error handling, and diagnostics dependency
already in use. Detected libraries are the highest-priority signal — the
generated skill must be compatible with what's already there.

Run the appropriate scan based on language:

**TypeScript / JavaScript (`package.json`)**
```bash
# Extract dependencies + devDependencies keys
cat package.json | python3 -c "
import json,sys
d=json.load(sys.stdin)
libs=list({**d.get('dependencies',{}), **d.get('devDependencies',{})}.keys())
keywords=['pino','winston','bunyan','log4','consola','debug','morgan',
          'neverthrow','ts-results','effect','zod','yup','valibot','fp-ts']
print([l for l in libs if any(k in l for k in keywords)])
"
```

**Rust (`Cargo.toml`)**
```bash
# Find logging/error crates
grep -E '^\s*(thiserror|anyhow|eyre|miette|snafu|tracing|log|env_logger|
  tracing-subscriber|color-eyre|serde|tokio)' Cargo.toml Cargo.lock 2>/dev/null | head -30
```

**Go (`go.mod`)**
```bash
grep -E '(zap|zerolog|logrus|slog|sentry|pkg/errors|multierr|go-multierror)' go.mod
```

**Python (`pyproject.toml` / `requirements.txt`)**
```bash
grep -Ei '(structlog|loguru|logbook|rich|pydantic|marshmallow|cerberus|
  result|returns|stamina)' pyproject.toml requirements*.txt setup.cfg 2>/dev/null
```

If the project structure is non-standard or multi-language, adapt — the goal
is to find what's already wired in, not to run a rigid script.

Also look for existing logging configuration or setup files:
- `logger.ts`, `logging.py`, `log/config.go`, `src/telemetry.rs`, etc.
- Note any existing log format (JSON vs pretty, any custom fields).

**Checkpoint:** Print the phase tracker (Phase 2 ✅) and list every detected
library with a one-line description. If nothing was found, say so explicitly.

---

## Phase 3 — Research: idiomatic libraries & patterns

**Goal:** Find the current best-practice library landscape for this language
with real star counts and recency signals. Even if libraries were detected in
Phase 2, run this search — the project may be using a suboptimal choice, and
the user should know the alternatives.

Run **two** web searches in parallel:

**Search 1:** `{language} logging library best 2025 structured JSON`
**Search 2:** `{language} error handling library idiomatic {year} github stars`

For languages where the stdlib has evolved (Go `slog`, Python `structlog`,
JS native `Error.cause`), also search:
`{language} stdlib logging error handling 2025 best practices`

From results, build a comparison table for each category:

**Logging libraries:**
| Library | Stars | Last release | Key characteristic |
|---|---|---|---|
| ... | ... | ... | ... |

**Error / Result libraries:**
| Library | Stars | Last release | Key characteristic |
|---|---|---|---|
| ... | ... | ... | ... |

**Validation libraries** (if applicable):
| Library | Stars | Last release | Key characteristic |
|---|---|---|---|
| ... | ... | ... | ... |

For each entry, note:
- Whether it's the ecosystem default / blessed choice
- Whether it integrates with OpenTelemetry
- Whether it's appropriate for libraries vs. applications (or both)
- Any known maintenance concerns (abandoned, security issues)

Also find **one concrete idiomatic example** per major library: a short snippet
showing an error type definition and a log call. These become the seed examples
in the generated skill.

**Checkpoint:** Print the phase tracker (Phase 3 ✅), the comparison tables,
and the seed snippets. Tell the user you'll use these in Phase 4.

---

## Phase 4 — User decisions: library stack & application profile

**Goal:** Nail down the exact choices that drive the generated skill. Ask all
questions in one batch — do not drip-feed them one at a time.

Present a summary of what you know so far:
- Detected language
- Application type
- Libraries already in use (from Phase 2)
- Top recommended libraries (from Phase 3)

Then ask the following questions. Use `ask_user_input_v0` for any that have
clean option sets; use free-text prompts for open-ended ones.

**Required questions (always ask):**

1. **Logging library** — which one should the generated skill teach?
   Options: the detected library if any, top picks from Phase 3, or "none /
   stdlib only". If the project already uses one, default to that but confirm.

2. **Error / Result library** — typed errors (Result/Either pattern) or
   exception-based? Which library? Or stdlib only?

3. **Validation library** — is there a schema validation library in use?
   (e.g., zod, pydantic, serde) The skill should cover how to surface
   validation errors well.

4. **Application type** (if not already clear):
   - Library/crate/package (will be published; callers will match on errors)
   - Application/binary/service (owns the error boundary; can use opaque errors)
   - Both (monorepo with shared libs and services)

5. **Log output format:**
   - JSON in all environments
   - JSON in production, pretty in development (recommended)
   - Pretty only (dev tools / CLIs)

6. **Error rendering target** — who sees the errors?
   - End users (CLI, human-readable output required)
   - HTTP API consumers (JSON response body)
   - Internal / ops only (logs and dashboards)
   - Mixed (need all three views)

**Optional questions (ask only if not determinable from context):**

7. **Async runtime** (Rust: tokio/async-std; JS: native promises/async-await;
   Go: goroutines). Affects tracing/span integration.

8. **Are there any existing internal error conventions** (error code scheme,
   specific field names, a shared error base type) the skill should know about?

**Checkpoint:** Print the phase tracker (Phase 4 ✅) and a "Decision summary"
table with every chosen library and setting before generating anything. Confirm
with the user: "Does this look right? I'll generate the skill based on these
choices." Wait for explicit confirmation before Phase 5.

---

## Phase 5 — Generate the skill

**Goal:** Produce the language-specific SKILL.md by filling in the template
from `references/skill-template.md` with all discovered and decided information.

Read `references/skill-template.md` now. Fill every section using:
- Phase 2 findings (existing libraries, log format, any current config)
- Phase 3 research (star counts, concrete code examples)
- Phase 4 decisions (chosen library stack, application type, render targets)

**Non-negotiable requirements for the generated skill:**

1. **`diagnostics-base` must be a hard dependency.** SKILL.md frontmatter only
   supports `name` and `description` (a `compatibility:` key is silently
   ignored by the loader). Encode the dependency two ways:
   - In the `description`, end with the sentence: *"REQUIRES diagnostics-base
     to be installed — this skill does not repeat base rules, it extends them."*
   - As the very first line of the body, an enforced banner:
     > "REQUIRED: Read `diagnostics-base` fully before applying any pattern
     > in this skill. This skill is an extension — it does not repeat the base
     > rules. All base rules apply."

2. **Every pattern must reference the base rule it extends.** When showing
   how to write a log call, note which base rule (e.g., "structured fields —
   base §Structured logging") it implements.

3. **Library decision tree must be present.** Show when to use each chosen
   library in a decision table or flowchart — the most common confusion point
   for every language is "which of my libraries do I reach for here."

4. **Code examples must be real, runnable snippets** — not pseudocode. The
   base skill uses pseudocode; this skill uses actual syntax for the chosen
   language and libraries.

5. **The checklist addendum** must include language/library-specific items
   on top of the base checklist.

Name the output file: `diagnostics-{language-lowercase}.skill` (e.g.,
`diagnostics-typescript.skill`, `diagnostics-rust.skill`).

Save the SKILL.md to: `/tmp/diagnostics-{lang}/SKILL.md`

**Checkpoint:** Print the phase tracker (Phase 5 ✅) and show the generated
SKILL.md inline. Tell the user: "Here's the generated skill. Review it and
tell me anything to change before I package it."

---

## Phase 6 — Test the skill via subagents

**Goal:** Verify the generated skill actually changes a fresh agent's output
before packaging. A skill that reads well but doesn't land in practice is a
waste of context window. Subagent tests catch this gap.

Spawn **three** subagents using the `Agent` tool (subagent_type: `claude` or
`general-purpose`), in parallel. Each receives the path to the saved SKILL.md
plus the path to `diagnostics-base/SKILL.md`, and a task it must complete by
applying both skills.

**Test 1 — Reviewer test (catches anti-patterns).**
Hand the subagent a short, deliberately bad snippet in {LANGUAGE} containing
at least four base-skill violations: a swallowed error, log-and-rethrow, an
interpolated log message, an "ERROR" level on a 4xx user error, no cause
preservation, no error code. Prompt:
> "Read both skills, then review the snippet. List every violation you find,
> citing the skill rule by section name. Under 250 words."

Score: count cited violations. A well-written skill produces >= 4 distinct
citations with section names. If the subagent misses obvious violations, the
skill's anti-pattern table is not specific enough — fix and re-test.

**Test 2 — Author test (writes from scratch).**
Give the subagent a tight spec: *"Write a function `loadConfig(path)` in
{LANGUAGE} using only the libraries declared in the skill's Library Stack
table. Define its error type and one log call. Apply both skills."* No bad
seed code — just the spec.

Score: read the output. Does it have a stable code field on the error type?
Does it preserve cause when wrapping I/O errors? Does the log call use
structured fields? Does it use OTel-style field names? If any of these miss,
the skill's positive examples are under-specified — add more concrete code.

**Test 3 — Log-level test (taxonomy).**
Give the subagent five scenarios and ask for the log level + one-line
justification for each:
1. Database connection refused at startup (process cannot continue)
2. Card declined returning 402 to the client
3. Retry succeeded on attempt 3 of 5
4. Background job completed processing 12,000 items
5. Deprecated config field still in use

Expected answers (per base rules):
1. FATAL (then exit) — process cannot continue
2. INFO or DEBUG — normal traffic, not service breakage
3. WARN — leading indicator worth knowing about
4. INFO — notable state change worth keeping in production
5. WARN — actionable in aggregate

Score: 5/5 must match. If the subagent picks ERROR for #2, the skill's log
level rules need stronger language about user errors vs. service errors.

**Checkpoint:** Print the phase tracker (Phase 6 ✅) and a results table:

| Test | Pass / Fail | Notes |
|---|---|---|
| Reviewer (anti-patterns caught) | ... | ... |
| Author (writes clean code) | ... | ... |
| Log-level taxonomy | ... | ... |

If any test fails, iterate on the skill and re-run that specific test (no
need to redo passing tests). Tell the user the results before Phase 7:
> "Subagent tests: [X passed, Y failed]. [If failures] I've patched the
> following sections: [...]. Re-running failed tests."

Do not proceed to Phase 7 until all three tests pass.

---

## Phase 7 — Review & package

**Goal:** Incorporate any user feedback and produce the final packaged `.skill`
file.

Apply any changes the user requests. Then run a self-review against this
checklist before packaging:

- [ ] `diagnostics-base` named in the description AND the opening body banner
      (do not add a `compatibility:` frontmatter key — it is not recognized)
- [ ] Opening `REQUIRED: Read diagnostics-base first` instruction
- [ ] Library decision tree present
- [ ] Every code example is real syntax (not pseudocode)
- [ ] Log level rules consistent with base skill
- [ ] Cause-chaining pattern shown for chosen libraries
- [ ] Structured logging example uses OTel field names
- [ ] Single-log-at-boundary rule shown in context of chosen framework
- [ ] Security / redaction pattern shown
- [ ] Checklist addendum present
- [ ] No base-skill rules contradicted or weakened
- [ ] All three Phase 6 subagent tests passed

Once all items pass, package using the skill-creator's package script:
```bash
# Copy scripts to a writable location if needed
cp -r /mnt/skills/examples/skill-creator/scripts /tmp/skill-creator-scripts
cd /tmp && python3 -m skill-creator-scripts.package_skill /tmp/diagnostics-{lang}
# or, if the above module path fails:
python3 /mnt/skills/examples/skill-creator/scripts/package_skill.py /tmp/diagnostics-{lang}
```

Present the `.skill` file to the user and confirm install instructions:
> "Install this by dropping `diagnostics-{lang}.skill` into your Claude skills
> directory alongside `diagnostics-base.skill`. Both skills will be loaded
> together whenever you work on error handling or logging in {Language}."

Print the final phase tracker (all ✅) and close the workflow.

---

## Error recovery

If the user bails out mid-workflow or says "skip" for a phase:
- Skip only non-critical phases (Phase 2 if no codebase exists is fine to skip)
- Phase 4 decisions are required — the skill cannot be generated without them
- If the user skips Phase 4, ask for the minimum: logging library + application type

If web search returns thin results (Phase 3):
- Fall back to training knowledge for the language
- Note clearly in the generated skill which library recommendations are from
  search (with date) vs. from training knowledge (may be stale)

If `diagnostics-base` is not installed:
- Stop immediately at the opening check
- Do not attempt to inline the base skill content
- The dependency is intentional — the skills are designed to be used together

---
> Source: [tarqd/diagnostics-skill](https://github.com/tarqd/diagnostics-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
