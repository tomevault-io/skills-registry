---
name: using-elixir-skills
description: Use when the user works on .ex or .exs files, mentions Elixir/Phoenix/Ecto/OTP, the project has mix.exs, or asks which skill to use or for help with Elixir. Routes to the correct thinking skill BEFORE exploring code. Triggers on implement, add, fix, refactor in Elixir projects.
metadata:
  author: gissandrogama
---

# Using Elixir Skills

**Resumo (pt-BR):** Em tarefas Elixir/Phoenix/OTP, invoque a skill correta ANTES de explorar o código ou implementar. As skills definem como explorar e o que procurar.

## The Rule

```
Elixir/Phoenix/OTP task → Invoke skill FIRST → Then explore/research → Then write code
```

**Skills come before exploration.** The skills tell you what patterns to look for, what questions to ask, and what anti-patterns to avoid. Exploring without the skill means you don't know what you're looking for.

## Skill Triggers

| Trigger Phrases / Topics | Skill to Invoke |
|-------------------------|-----------------|
| code, implement, write, design, architecture, structure, pattern | `elixir-thinking` |
| LiveView, Plug, PubSub, mount, channel, socket, component | `phoenix-thinking` |
| context, schema, Ecto, changeset, preload, Repo, migration | `ecto-thinking` |
| GenServer, supervisor, Task, ETS, bottleneck, Broadway | `otp-thinking` |
| Oban, workflow, job queue, cascade, graft, background job, async job | `oban-thinking` |

## Red Flags

These thoughts mean STOP—invoke the skill first:

| Thought | Reality |
|---------|---------|
| "Let me explore the codebase first" | Skills tell you WHAT to look for. Invoke first. |
| "Let me understand the code first" | Skills guide understanding. Invoke first. |
| "But first, let me..." | No. Skills first. Always. |
| "I'll add a process to organize this" | Processes are for runtime, not organization. |
| "GenServer is the Elixir way" | GenServer is a bottleneck by design. |
| "I'll query in mount" | mount is called twice. |
| "Task.async is simpler" | Use Task.Supervisor in production. |
| "I know Elixir well enough" | These skills contain paradigm shifts. Invoke them. |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
