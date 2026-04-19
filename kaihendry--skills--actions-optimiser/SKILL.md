---
name: actions-optimiser
description: This skill should be used when the user asks to "optimise GitHub Actions", "improve my workflow", "make actions better", "review workflow usage", "how are others using this action", "find best practices for GitHub Actions", or wants to gather real-world context on how GitHub Actions are used to improve their workflows. Use when this capability is needed.
metadata:
  author: kaihendry
---

# GitHub Actions Optimiser

Gather real-world usage context for GitHub Actions and recommend improvements to workflow files.

## Process

### Step 1: Search for How an Action Is Used

When a GitHub workflow in `.github/workflows/` uses an action such as:

    uses: hashicorp/setup-terraform@v3

Search for real-world usage with the pre-installed `gh` CLI:

    gh search code "setup-terraform path:.github/workflows"

### Step 2: View Search Results with Context

Use the pattern `/repos/{owner}/{repo}/contents/{path}` to look up at least five results in parallel. For example:

    gh api repos/Sofiane-Truman/cloud-foundation-fabric/contents/.github/workflows/linting.yml | jq -r '.content' | base64 -d | less

### Step 3: Check Authoritative Usage

Given the earlier example of `hashicorp/setup-terraform@v3`, the canonical source repository is `https://github.com/hashicorp/setup-terraform`. Study the README.md for official guidance.

### Step 4: Plan Optimisations

Identify how the GitHub workflow can be improved. Prefer the latest version of actions and keep to defaults where possible. Find and explore novel yet succinct usages from the GitHub search results.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kaihendry) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
