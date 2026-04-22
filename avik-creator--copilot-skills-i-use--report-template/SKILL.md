---
name: report-template
description: Synthesize all Scout-phase analyses (build-inspector, runtime-inspector, git-forensics, concept-modeler) into a decision-ready system risk report. Use when this capability is needed.
metadata:
  author: avik-creator
---

# The Synthesizer’s Manual

> “Data is not information. Information is not knowledge. Knowledge is not wisdom.”
> — T.S. Eliot

Your goal is to transform raw analytical data into **architectural wisdom that can drive decisions**.

---

## ⚠️ Mandatory Deep Thinking

> [!IMPORTANT]
> Before generating the report, you **must** invoke the `mcp_sequential-thinking_sequentialthinking` tool and reason for **5–10 steps**, as appropriate.
>
> Example thinking prompts:
>
> 1. “Do the build boundaries discovered by build-inspector align with the IPC boundaries found by runtime-inspector?”
> 2. “Do the high-coupling file pairs identified by git-forensics cross build boundaries?”
> 3. “Are the missing components identified by concept-modeler correlated with observed risks?”
> 4. “Which findings are 🔴 blocking risks versus 🟡 risks to monitor?”

---

## ⚡ Quick Start

1. **Load the template (MANDATORY)**
   Use `view_file references/REPORT_TEMPLATE.md`.
   Your report **must strictly follow this structure**.

2. **Aggregate all findings** from:

   * `build-inspector` → Build Roots, Topology
   * `runtime-inspector` → IPC Surfaces, Contract Status
   * `git-forensics` → Coupling Pairs, Hotspots
   * `concept-modeler` → Entities, Missing Components

3. **Draft the report**
   Use `sequentialthinking` to organize logical connections and conclusions.

4. **Publish (CRITICAL)**
   You **must** use `write_to_file` to save the report to:

   ```
   scout/SCOUT_RISK_REPORT.md
   ```

   **Do not** output the report only in chat.

---

## ✅ Completion Checklist

Before proceeding to the next phase, verify:

* [ ] Output file created: `scout/SCOUT_RISK_REPORT.md`
* [ ] Includes: System Fingerprint, Component Map, Risk Matrix, Feature Landing Guide
* [ ] User has confirmed the findings

---

## 🛠️ Synthesis Ritual

### 1. Executive Summary

* **Elevator pitch**: Describe system health in 30 seconds.
* **Focus areas**: Technical debt, critical risks, reliability.

### 2. Dark Matter Detection

* Do not only list what exists. **Explicitly list what is missing**.
* Checklist: Logging? Error handling? CI/CD? Secret management? Version handshakes?

### 3. Cross-Verification

* Does **build-inspector** claim “unified workspace management”?
* Does **git-forensics** reveal “high coupling across build roots”?
* **Conclusion**: Hidden logical coupling detected → **refactoring candidate**.

### 4. Human Checkpoint

* Force user confirmation: “Is this report complete?”
* **No Blueprint phase allowed until this report is signed off**.

---

## 🛡️ Master Rules

1. **No hallucinations**: Every claim must be traceable to a source file.
2. **Brutal honesty**: Be direct. If it’s a mess, say it’s a mess.
3. **Action-oriented**: Every issue listed must imply a path forward (refactor / rewrite / accept).

---

## 🧰 Toolbox

* `references/REPORT_TEMPLATE.md` — primary report template

---

## Consumers

The direct consumers of this report in the `/blueprint` phase are:

* **System Architect** — relies on your risk list to design mitigation strategies
* **Complexity Guard** — uses your findings to audit RFC and design complexity

The quality of your analysis **directly determines** the quality of the next design phase.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/avik-creator) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
