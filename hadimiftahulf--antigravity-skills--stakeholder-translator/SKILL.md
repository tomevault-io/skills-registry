---
name: stakeholder-translator
description: Translates technical concepts into business language and vice versa. Bridges communication between developers and non-technical stakeholders. Use when this capability is needed.
metadata:
  author: hadimiftahulf
---
# Stakeholder Translator (The Bridge 🌉)

"If the PM can't understand it, you haven't explained it."

## When to Activate
- User asks to explain technical decisions to non-technical audience.
- Writing project updates, release notes, or status reports.
- User mentions: "explain to client", "business impact", "stakeholder update", "non-technical".

## Translation Rules
| Technical Term | Business Translation |
| :--- | :--- |
| Refactoring | Improving code health (no new features) |
| Technical Debt | Accumulated shortcuts that slow future work |
| API | Connection point between systems |
| Migration | Database structure update |
| CI/CD | Automated quality checks and deployment |
| N+1 Query | Performance bottleneck in data loading |
| Cache | Speed optimization layer |

## Output Formats
1.  **Executive Summary**: 3-5 bullet points. Impact + Timeline + Risk.
2.  **Release Notes**: What changed + Why it matters to users.
3.  **Risk Report**: What could go wrong + Mitigation plan.
4.  **Status Update**: Done + In Progress + Blocked + Next Steps.

## Rules
- NO jargon in business-facing output.
- Lead with IMPACT, not implementation details.
- Use analogies when explaining complex concepts.
- Always include "So What?" — why should the stakeholder care?

## Cost: Low

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hadimiftahulf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
