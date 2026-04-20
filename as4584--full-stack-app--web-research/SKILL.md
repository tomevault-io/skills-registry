---
name: web-research
description: Use web search and fetching tools to gather the latest technical documentation and best practices. Use when this capability is needed.
metadata:
  author: as4584
---

# Web Research Skill

This skill allows you to stay current with external APIs and libraries by searching the web and reading documentation directly.

## Instructions

1.  **Search First**: When the user asks about an external service (like Stripe or Twilio) or a modern CSS technique, use the `search` tool (DuckDuckGo) to find the most recent information.
2.  **Fetch Docs**: Once you find a relevant URL, use the `fetch` tool to read the full page content. Do not rely on your training data for rapidly changing API specifications.
3.  **Video Summaries**: If a tutorial is only available on YouTube, use `youtube_transcript` to extract the key steps and implementation details.
4.  **Synthesize**: Combine the researched info into a high-quality implementation plan for the user.

## Tools to Use
- `search` (via DuckDuckGo)
- `fetch`
- `youtube_transcript`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/as4584) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
