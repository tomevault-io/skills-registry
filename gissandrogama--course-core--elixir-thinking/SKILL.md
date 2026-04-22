---
name: elixir-thinking
description: Apply when implementing features in Elixir, refactoring modules, choosing GenServer vs functions, structuring code, using pipe operator, error handling, concurrency, or when the user mentions protocols, behaviours, pattern matching, with, comprehensions, structs, or coming from OOP. Contains paradigm-shifting insights for Elixir. Use when this capability is needed.
metadata:
  author: gissandrogama
---

# Elixir Thinking

**Resumo (pt-BR):** Guia mental para escrever Elixir evitando padrões OOP. Regra de ouro: nenhum processo sem necessidade de runtime (estado mutável, concorrência ou isolamento de falha).

Mental shifts required before writing Elixir. These contradict conventional OOP patterns.

## The Iron Law

```
NO PROCESS WITHOUT A RUNTIME REASON
```

Before creating a GenServer, Agent, or any process, answer YES to at least one:
1. Do I need mutable state persisting across calls?
2. Do I need concurrent execution?
3. Do I need fault isolation?

**All three are NO?** Use plain functions. Modules organize code; processes manage runtime.

## The Three Decoupled Dimensions

OOP couples behavior, state, and mutability together. Elixir decouples them:

| OOP Dimension | Elixir Equivalent |
|---------------|-------------------|
| Behavior | Modules (functions) |
| State | Data (structs, maps) |
| Mutability | Processes (GenServer) |

Pick only what you need. "I only need data and functions" = no process needed.

## "Let It Crash" = "Let It Heal"

- Handle expected errors explicitly (`{:ok, _}` / `{:error, _}`)
- Let unexpected errors crash → supervisor restarts

## Control Flow

**Pattern matching first:** Match on function heads instead of `if/else` or `case` in bodies. `%{}` matches ANY map—use `map_size(map) == 0` guard for empty maps. Avoid nested `case`—refactor to single `case`, `with`, or separate functions.

**Error handling:** Use `{:ok, result}` / `{:error, reason}` for operations that can fail. Avoid raising exceptions for control flow. Use `with` for chaining `{:ok, _}` / `{:error, _}` operations.

**Be explicit:** Avoid `_ -> nil` catch-alls. Avoid `value && value.field` nil-punning. Prefer `with` when combining `{:ok, nil}` and `{:ok, value}` cases.

## Polymorphism

| For Polymorphism Over... | Use |
|--------------------------|-----|
| Modules | Behaviors |
| Data | Protocols |
| Processes | Message passing |

Use the simplest abstraction: pattern matching → anonymous functions → behaviors → protocols → message passing.

## Defaults and Options

Use `/3` variants (`Keyword.get/3`, `Map.get/3`) instead of case statements branching on `nil`. Don't create helper functions to merge config defaults; inline the fallback.

## Idioms

- Process dictionary is typically unidiomatic—pass state explicitly
- Reserve `is_thing` names for guards only
- Use structs over maps when shape is known
- Prepend to lists `[new | list]` not `list ++ [new]`
- Use `dbg/1` for debugging
- Use built-in `JSON` module (Elixir 1.18+) instead of Jason

## Testing

**Test behavior, not implementation.** Test use cases / public API. **Test your code, not the framework.** Keep tests async; fix coupling if you need `async: false`.

## Red Flags - STOP and Reconsider

- Creating process without answering the three questions
- Using GenServer for stateless operations
- Wrapping a library in a process "for safety"
- One process per entity without runtime justification
- Reaching for protocols when pattern matching works

**Any of these? Re-read The Iron Law.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gissandrogama) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
