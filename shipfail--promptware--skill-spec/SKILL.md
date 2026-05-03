---
name: promptware-s-agent-skill-specification
description: Learn how to use SKILL by following this SKILL specification. Use when this capability is needed.
metadata:
  author: shipfail
---

# The SKILL.md Specification for PromptWar̊e ØS

PromptWar̊e ØS Agent Skills are based on the Anthropic Claude Agent Skills Spec, with below modifications and enhancements:

1. all `scripts`/`tools` MUST written in TypeScript with Deno as runtime. No exceptions. 
2. all `scripts`/`tools` MUST support standand CLI `--help` command and output the detailed usage description.
3. all `scripts`/`tools` will be executed with a remote-first way, no download needed. (use `deno https://url` to execute it directly)

## References

**UnDoc** is our ultimate documentation source of the truth.

- Learn full anthropic claude agent skills specification at <https://shipfail.github.io/undoc/un/claude-agent-skills-spec.md>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shipfail) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
