---
name: michat-specs-reference
description: Use this when a user asks obscure technical/structural questions about MiChat internals (profiles/toolsets/actions/skills) that are beyond the scope of the michat-user-support skill and require consulting the michat-specs documents as source of truth.
metadata:
  author: filmicgaze
---

## When to use this skill

### Gate (don't overuse)

This skill is **context-heavy**. Do **not** use it "just to be thorough".

Use it only when **at least one** of these is true:

- The user explicitly asks for how MiChat works internally (architecture, schema, lifecycle, toolsets/actions/skills).
- The user is trying to extend MiChat (adding/changing toolsets/actions/skills) and needs a source-of-truth reference.
- You would otherwise have to guess about behavior/schema and the answer matters.

### Offer a deep dive (opt-in)

If the user did not explicitly request a spec deep dive, first offer:

> I can search the MiChat technical specs to confirm—do you want me to?

Proceed only if they say yes, or if the question is clearly technical/structural and cannot be answered reliably without the specs.

### Examples (good triggers)

- "How does MiChat work internally?" / architecture or design questions
- "How do I add a new toolset / tool / action?"
- "How do profiles work—what's portable, what's the schema?"
- "How do skills work—what's the spec?"
- "What settings require a session reset and why?" (beyond user-facing guidance)

### Non-examples (don't use for these)

- Routine "how do I use MiChat?" questions
- Basic troubleshooting that can be handled with runtime/tool gating checks
- "What does this tool do?" when the per-turn tool description already answers it
- Checking whether a toolset is enabled (use the normal runtime/tooling surface)

## Tools

Use the assistant profile library (deep reference):

- `library_search(query, scope="profile")`
- `library_meta(path)`
- `library_read_section(path, heading_id_or_path)`

Optional (only if available in this profile):

- Web browsing/search tools to find up-to-date external guidance on development tools/workflows.

## Workflow

1) **Clarify the goal and the desired depth**
   - Are they trying to *use* MiChat, *configure* a profile, or *extend* MiChat?
   - Do they want the high-level behavior or the underlying schema/lifecycle details?

2) **Try the lightest adequate avenue first**
   - If the question is operational, answer from user-facing docs (`_global` library) or from the current runtime/tooling surface.
   - If you can answer confidently without specs, do so and stop.

3) **Ask to proceed (unless already clearly requested)**
   - If the user didn't explicitly ask for specs and you still think specs are needed, ask:
     - "I can search the MiChat technical specs to confirm—do you want me to?"

4) **Consult the authoritative spec docs (michat-specs)**
   - Search within `profile/michat-specs/...` for the key term (e.g. "toolsets", "actions", "profile.json", "workspace_root").
   - Open the most relevant section via headings (use `library_meta` → `library_read_section`).
   - Answer with the smallest set of facts that resolves the question.
   - Quote short snippets when it prevents ambiguity.

5) **If the user wants to change the codebase**
   - Be explicit: MiChat (this chat environment) is not a repo-editing tool.
   - Help with orientation:
     - point them to the right spec doc/section
     - outline the implementation approach at a high level

## Constraints and safety

- Treat `profile/michat-specs/*` as the **source of truth**; don't invent missing toolsets, files, or settings.
- If the specs don't cover something, say so and ask one targeted question (or suggest where to inspect next).
- Keep answers grounded: prefer citations to specific headings/sections over speculation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filmicgaze) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
