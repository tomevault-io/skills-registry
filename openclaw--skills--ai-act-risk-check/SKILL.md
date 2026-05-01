---
name: ai-act-risk-check
description: **Description:** Quickly assesses a preliminary risk classification for an AI system based on the high-risk categories listed in Annex III of the EU AI Act (focusing on biometrics, critical infrastructure, education, employment, essential services, law enforcement, and justice). Use when this capability is needed.
metadata:
  author: openclaw
---
# SKILL.md - AI Act Risk Check

## `ai-act-risk-check`

**Description:** Quickly assesses a preliminary risk classification for an AI system based on the high-risk categories listed in Annex III of the EU AI Act (focusing on biometrics, critical infrastructure, education, employment, essential services, law enforcement, and justice).

**Usage:**
\`\`\`bash
ai-act-risk-check "Our system is an AI algorithm that screens job applications based on predicted performance metrics."
\`\`\`

**Output:** A determination of HIGH-RISK or LOW-RISK, along with the relevant Annex III category (if high-risk).

**Dependencies:** None (uses pure shell and `oracle` via `exec` for inference).

**Execution Logic:** Passes the user's description to an LLM for classification against the hard-coded Annex III criteria.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
