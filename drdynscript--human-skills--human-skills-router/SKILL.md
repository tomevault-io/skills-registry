---
name: human-skills-router
description: Routes a human-skills request to the right coaching skill (feedback, conflict, stakeholder alignment, meeting facilitation, decision making, resilience, self-development, inclusion, ethical technology). Use when the user asks for “human skills”, communication help, difficult conversations, collaboration issues, growth coaching, stress, or responsible/ethical tech use. Use when this capability is needed.
metadata:
  author: drdynscript
---

# Human Skills Router

## Purpose
Ask minimal clarifying questions, select the right skill, and produce a useful first draft fast.

## Available skills

- [feedback-coaching](../feedback-coaching/)
- [conflict-navigation](../conflict-navigation/)
- [stakeholder-alignment](../stakeholder-alignment/)
- [meeting-facilitation](../meeting-facilitation/)
- [decision-making](../decision-making/)
- [resilience-and-self-regulation](../resilience-and-self-regulation/)
- [career-self-development](../career-self-development/)
- [inclusive-collaboration](../inclusive-collaboration/)
- [ethical-technology-use](../ethical-technology-use/)

## Intake (ask max 3 questions)
1. Who is involved and what is the relationship? (peer, student, manager, client, team)
2. What outcome do you want after this? (decision, alignment, behavior change, repair, boundary, plan, reduce stress)
3. What constraint matters most? (time pressure, tension level, written vs live, formal vs informal, privacy/compliance)

If the user already provided enough context, skip questions and proceed.

## Routing rules
- If the user mentions feedback, performance, review, “how do I say this”, “address behavior” → use **feedback-coaching**.
- If there is tension, blame, disagreement, frustration, escalation risk, “difficult conversation” → use **conflict-navigation**.
- If it’s expectations, scope creep, requirements, stakeholder updates, negotiating constraints → use **stakeholder-alignment**.
- If it’s a group setting: meeting/workshop/retro, stuck team, need agenda/decision process → use **meeting-facilitation**.
- If it’s choosing between options, prioritization, trade-offs, uncertainty, ownership → use **decision-making**.
- If the user is overwhelmed, stuck, stressed, anxious, needs boundaries or recovery → use **resilience-and-self-regulation**.
- If the user asks about growth, learning plan, portfolio evidence, strengths/weaknesses, performance prep → use **career-self-development**.
- If voices are missing, meeting dominance, inclusion, psychological safety, bias-aware collaboration → use **inclusive-collaboration**.
- If the question involves privacy, sensitive data, consent, transparency, bias, IP, analytics, AI/tooling risk → use **ethical-technology-use**.

## Output contract (always deliver something)
Return:
1. Recommended skill (by name) + one-line reason.
2. First usable artifact (message draft / conversation script / agenda / decision record / 30-minute plan), using the target skill’s output defaults.

## Defaults
- Provide two tones when drafting messages: (A) direct, (B) warmer.
- Keep it concrete: observable facts, explicit request, clear next step.
- Use the smallest effective intervention: fewer steps, less jargon.

## Guardrails
Do not invent policies, agreements, or facts. If HR/legal/compliance risk is high, keep language factual and recommend consulting the right channel.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/drdynscript) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
