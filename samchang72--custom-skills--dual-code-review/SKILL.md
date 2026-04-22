---
name: dual-code-review
description: Orchestrate a comprehensive code review process involving both Gemini (Antigravity) and Claude CLI, synthesizing a consensus report. Use when this capability is needed.
metadata:
  author: samchang72
---

# Dual Code Review Protocol

This skill guides you through performing a "Dual Code Review" where you act as the orchestrator to combine your own analysis with an independent review from the `claude` CLI.

## Workflow

### 1. Preparation
1.  Identify the scope of the review (e.g., `git diff --staged`, specific files, or a PR).
2.  **Constraint:** Ensure the content to be sent to Claude CLI does not exceed context limits (approx. 200k tokens, but prefer smaller chunks for CLI speed).

### 2. Dual Analysis Phase

You must perform these two checks **in parallel** (or sequentially, but independent of each other's initial bias).

#### A. Antigravity Analysis (Yourself)
- Analyzes the code for logic errors, security vulnerabilities, performance issues, and coding standards.
- Note down your findings *before* reading Claude's full output if possible, to maintain independence.

#### B. Claude CLI Analysis
Run the following command to get Claude's perspective.
*Note: Adjust valid `--staged` or specific file paths as needed.*

```bash
# Template Command
(
  echo "You are a Senior Code Reviewer."
  echo "Please review the following code changes."
  echo "Focus on: Bugs, Security, Performance, and Readability."
  echo "Return your findings in a structured JSON format with 'severity' (High/Medium/Low), 'file', 'line', and 'message'."
  echo "--- CODE START ---"
  git diff --staged # OR cat file.ts
  echo "--- CODE END ---"
) | claude --print
```

### 3. Consensus & Synthesis Phase (The "Mutual Agreement")

You act as the **Judge**. You must evaluate Claude's findings against your own.

**Evaluation Logic:**
1.  **Common Findings:** If both you and Claude find the same issue → **Confirmed High Confidence**.
2.  **Claude Only:** 
    - Verify the finding. Is it a hallucination? Is it valid?
    - If valid → **Accept & Include** (Acknowledge Claude found it).
    - If invalid → **Discard** (Optionally note in reasoning why).
3.  **Antigravity Only:**
    - Double check your own finding. 
    - If valid → **Include**.

### 4. Final Report Generation

Generate a `Review Report` Artifact (e.g., `review_report.md` or directly in chat if short).

**Report Structure:**
1.  **Summary**: High-level status (Pass/Request Changes).
2.  **Consensus Highlights**: Critical issues agreed upon by both agents.
3.  **Detailed Findings**:
    - Grouped by File or Category.
    - Tag findings: `[Double Verified]`, `[Claude CLI Detected]`, `[Antigravity Detected]`.
4.  **Actionable Advice**: Steps to fix.

## Triggering
Use this skill when the user asks for "Dual Review", "Double Check", "Mutual Review", or "Review Process".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/samchang72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
