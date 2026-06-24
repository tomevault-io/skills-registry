---
name: commit-impact-analysis
description: Automatically analyze git commits to predict which tests might fail and identify potential risks. Use this skill when the user pushes code, creates a PR, or asks about the impact of their changes. Use when this capability is needed.
metadata:
  author: raphaenterprises-ai
---

# Commit Impact Analysis

This skill analyzes code changes to predict:
- Which tests are likely to fail
- Security vulnerabilities introduced
- Performance impacts
- Risk score for the change

## When to Use

- User runs `git push` or `git commit`
- User asks "what might this break?"
- User creates or updates a PR
- User asks about test coverage for changes

## How It Works

1. Extract the git diff using the helper script
2. Identify changed files and affected components
3. Query the Argus API for:
   - Test impact graph (file → test mapping)
   - Historical failure patterns
   - Security scan results
4. Generate predictions and recommendations

## Implementation

Use the following Argus MCP tools:

1. **`argus_what_to_test`** - Analyzes git diff to identify which tests are affected
   - Input: `{ "diff": "<git diff output>", "files": ["file1.py", "file2.js"] }`
   - Returns: List of tests to run with priority and reasoning

2. **`argus_risk_scores`** - Calculates risk score for the changes
   - Input: `{ "commit": "<commit SHA or branch>" }`
   - Returns: Risk assessment with scores and deployment recommendation

The tools return:
- Predicted test failures with confidence scores
- Security vulnerabilities found
- Risk assessment (low/medium/high)
- Recommended actions

## Output Format

Present findings as a structured report:
1. 🧪 Test Predictions (table with test name, failure probability, reason)
2. 🔒 Security Findings (severity, location, recommendation)
3. 📊 Risk Score with deployment recommendation
4. 💡 Suggested Actions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raphaenterprises-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
