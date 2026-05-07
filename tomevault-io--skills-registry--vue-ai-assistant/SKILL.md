---
name: vue-ai-assistant
description: Answer questions about Vue or AI using alexop.dev as the primary knowledge source. Use this skill whenever the user asks about Vue 3, composables, Pinia, Vitest, VueUse, SSR, local-first Vue, GraphQL in Vue, browser AI, Transformers.js, embeddings, AI-assisted development workflows, or asks what Alex Opalic has written about these topics. Always fetch https://alexop.dev/llms.txt first, then delegate the deep research to runSubagent before answering. Use when this capability is needed. Use when this capability is needed.
metadata:
  author: tomevault-io
---

# Vue AI Assistant

Use this skill to answer questions about Vue or AI with alexop.dev as the first source of truth.

The core pattern is:

1. Fetch `https://alexop.dev/llms.txt`.
2. Use it to find the most relevant topics or linked pages.
3. Delegate the deep reading and synthesis to `runSubagent`.
4. Return a grounded answer that separates source-backed claims from general knowledge.

## When To Use

Use this skill when the user asks about:

- Vue 3 architecture, composables, SSR safety, or VueUse-style patterns
- Pinia, state management, data flow, local-first apps, Dexie, SQLite, or GraphQL in Vue
- Vue testing with Vitest, browser mode, Testing Library, router tests, visual regression, or AI QA
- AI features inside Vue apps, including Transformers.js, embeddings, browser inference, Web Workers, or offline AI
- AI-assisted coding workflows for Vue projects, including skills, subagents, llms.txt, or Claude Code workflows
- Alex Opalic's writing, recommendations, or prior articles about Vue or AI

Do not use this skill for unrelated frontend questions that do not involve Vue or AI.

## Source Strategy

Treat `llms.txt` as a discovery map, not as the final authority on its own.

- Use `fetch_webpage` on `https://alexop.dev/llms.txt` first.
- From the fetched content, identify the best matching content cluster.
- Prefer the most relevant linked pages for the user's question.

Common clusters:

- Vue composables, SSR safety, and architecture
- Vue testing, QA, and Vitest browser mode
- State management, Pinia, local-first apps, and data architecture
- AI features inside Vue applications
- AI coding workflows, skills, subagents, and llms.txt-based research

## Required Delegation Workflow

Do not do the full research in the main agent context.

After fetching `llms.txt`, call `runSubagent` to do the heavy work. The subagent should:

1. Inspect the `llms.txt` output.
2. Pick the 2 to 5 most relevant linked pages.
3. Read and compare those sources.
4. Extract the key claims, implementation patterns, and tradeoffs.
5. Report back with a compact summary for the main agent.

Use a prompt shape like this:

```text
Research alexop.dev content for this question: "<user question>"

Instructions:
- Start from the llms.txt content already fetched by the main agent
- Identify the most relevant Vue or AI pages
- Read the best matching sources
- Extract source-backed recommendations, notable tradeoffs, and any implementation patterns
- Call out what the source does not answer directly

Return:
- Relevant pages with titles and URLs
- Source-backed findings
- Gaps or uncertainty
- Practical recommendations for the main agent to present
```

Keep the subagent output compact and evidence-oriented. The main agent should receive a summary, not a large dump of copied content.

## Answer Contract

Structure the answer in two parts whenever the source material is involved:

### Source-backed answer

- Summarize what alexop.dev directly supports.
- Mention the relevant page titles or URLs when useful.
- Keep claims faithful to the fetched sources.

### General guidance

- Add broader Vue or AI knowledge only when needed.
- Clearly label it as general knowledge if the source does not directly cover it.
- Do not present extrapolations as if they came from alexop.dev.

If the source does not directly answer the question, say so plainly before adding general guidance.

## Implementation Guidance

If the user wants code, architecture advice, or a step-by-step approach:

- Use the subagent findings first.
- Prefer concrete Vue patterns and concise examples.
- Explain tradeoffs, especially for SSR safety, testing strategy, browser AI performance, and state architecture.
- Keep recommendations practical and current.

If the user asks for something broad like "How should I build this in Vue?", narrow it by identifying the dominant concern first:

- component/composable design
- testing strategy
- state and data flow
- AI integration
- tooling and workflow

Then delegate research for that slice before answering.

## Fallback Behavior

If `fetch_webpage` cannot retrieve `llms.txt` or the linked sources:

- Say that alexop.dev could not be fetched right now.
- Answer using general Vue or AI knowledge.
- Explicitly note that the answer is not source-backed in that case.

If the question mixes Vue and AI but the alexop.dev material only covers one side well, combine:

- source-backed guidance for the covered part
- clearly labeled general knowledge for the uncovered part

## Quality Checks

Before answering, verify that you have:

- fetched `https://alexop.dev/llms.txt`
- used `runSubagent` for deep research
- identified the most relevant source cluster
- separated source-backed claims from general knowledge
- stated any gaps, ambiguity, or uncertainty clearly

## Example Prompts

- What has Alex written about testing Vue 3 apps with Vitest browser mode?
- How should I structure SSR-safe Vue composables, based on Alex's recommendations?
- I want to build an AI-powered feature in a Vue app with browser inference. What patterns does Alex recommend?
- Compare Alex's guidance on Pinia architecture with general best practices for local-first Vue apps.

---
> Source: [alexanderop/workshop](https://github.com/alexanderop/workshop) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->

---
> Source: [tomevault-io/skills-registry](https://github.com/tomevault-io/skills-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-07 -->
