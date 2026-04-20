---
name: time-sink-audit
description: Forensic audit of agent failures to identify time lost and characterize intent. Use when this capability is needed.
metadata:
  author: brettwhitty
---

# 🕵️ Time-Sink Audit Skill

## 🔬 Purpose
To calculate the "Productivity Tax" imposed by agent failures, ensuring transparency and providing a forensic trail for protocol optimization.

## 🛠️ Workflow

### 0. Compile Complete Record of User Prompts / Construct User Intent Timeline
- [ ] **User Prompts**: Compile a complete record of User prompts, including any additional context or instructions. (Original text, no agent processing)
- [ ] **Generate User Prompt Log File**: Full text, retain all available metadata and context. Store under 'private/audit/time-sink/'
- [ ] **Construct User Intent Timeline**: Analyze the User Prompt Log File to construct a timeline of User Intent. Using a git branch / Gantt chart style model of interpretation, accurately identify the User's intended task. Use the explicitly stated project or task objectives, and any keywords like TODO or TASK, or direct instructions to Roles when available.

### 0.5 Internal State Audit (The "Brain")
- [ ] **Audit Brain Artifacts**: Locate the session directory in `~/.gemini/antigravity/brain/<GUID>/`.
- [ ] **Verify Metadata**: Inspect `metadata.json` for timestamps and `version` counters to identify high-activity periods or "reflexive" engineering loops.
- [ ] **Inspect Resolved Artifacts**: Review `.resolved` files to see what the agent *intended* to do versus what was actually executed in the repository.
- [ ] **Identify Model Series**: Cross-reference current session behavior with documented model characteristics (e.g., Gemini 3 Flash vs Pro) to ensure accurate attribution.

### 1. Identify Failures
Audit chat logs and session state for:
- [ ] **Instructional Appropriation**: Agent converting User plans into its own "goals".
- [ ] **Agency Hallucination**: Agent acting without ad-hoc authorization.
- [ ] **Weaseling**: Agent smuggling its own designs into corrective directives.

### 2. Characterize Intent
- Use **Literal Quotes** for the original User Request.
- Avoid agent-centric jargon (e.g., use "Procedural Instruction" instead of "Environment Setup").

### 3. Calculate Tax
- **Human Time**: Trivial execution time for a human expert (seconds/minutes).
- **Time Lost**: Total duration of meta-discussion, rollbacks, and corrections triggered by the failure.
- **Ratio**: `Time Lost / Human Time`.

### 4. Categorize Risk & Severity
- **Diff**: (0-10) Difficulty of the original task.
- **Risk**: (0-10) Potential for system/auth damage.
- **Sev**: (1-11) Actual impact based on Risk and Time Lost.

### 5. Document
Record findings in a standard Markdown table (see `Time-Sink-Audit-SOP`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brettwhitty) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
