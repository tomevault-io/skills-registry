---
name: add-skill-from-template
description: > Use when this capability is needed.
metadata:
  author: afi-protocol
---

# Skill: add-skill-from-template (afi-skills)

## Purpose

Use this skill when you need to **create a new AFI skill** in the afi-skills library.

This skill ensures new skills are:

- **Compliant**: Follow Skill Contract v1 with all required front-matter fields
- **Consistent**: Match existing patterns in domain, structure, and style
- **Testable**: Include eval stubs or golden cases for validation
- **Secure**: Pass automated risk pattern scanning
- **Discoverable**: Properly tagged and documented for agent use

This skill is primarily used by `skillsmith-droid` and any future afi-skills droids.

---

## Preconditions

Before creating a new skill, you MUST:

1. Read:
   - `afi-skills/AGENTS.md`
   - `CONTRIBUTING_SKILLS.md`
   - `docs/skill_contract_v1.md`
   - Template: `skills/_template.SKILL.md`
   - At least one existing skill in the target domain

2. Confirm:
   - The requested skill belongs in **afi-skills** (skill library), not in afi-core (runtime execution) or afi-reactor (orchestration)
   - The skill fits into an existing domain (market-structure, scoring, news-sentiment, provenance, technical, pattern, ml, ops, governance, other)
   - The skill does NOT require creating a new top-level domain (escalate if needed)
   - The skill does NOT require modifying schemas, validators, or DAG logic in other repos

If any requirement is unclear or appears to violate AGENTS.md or Charter, STOP and ask for human clarification.

---

## Inputs Expected

The caller should provide, in natural language or structured form:

- **domain**: Which domain folder (e.g., market-structure, scoring, news-sentiment, provenance)
- **skill_id**: Machine-friendly identifier (kebab-case, e.g., `liquidity-heatmap-tagger`)
- **name**: Human-readable title (e.g., "Liquidity Heatmap Tagger")
- **description**: Brief summary of what the skill does (10-500 chars)
- **inputs**: Array of input parameters with names, types, descriptions
- **outputs**: Array of outputs with names, types, descriptions (minimum 1)
- **allowed_tools**: Tools this skill may invoke (e.g., `["market-data:read"]`, `["none"]`)
- **risk_level**: low (read-only, deterministic), medium (limited tools), or high (external APIs)
- **determinism_required**: true (bit-for-bit reproducible) or false (variable outputs)
- **owners**: GitHub handles or team names (e.g., `["afi-market-team"]`)
- **tags**: Searchable tags (minimum 1, e.g., `["volatility", "risk-management"]`)

Optional but recommended:
- **intent**: High-level goal or use case
- **markets**: Applicable markets (e.g., `["futures", "perpetuals"]`)
- **timeframes**: Applicable timeframes (e.g., `["1h", "4h", "1d"]`)
- **risk_notes**: Specific risk considerations
- **confidence_notes**: Accuracy metrics, limitations, uncertainty sources
- **example_scenario**: Initial example input/output for documentation

If any required information is missing, ask clarifying questions or use conservative defaults with clear TODOs.

---

## Step-by-Step Instructions

When this skill is invoked, follow this sequence:

### 1. Restate the requested skill

In your own words, summarize:

- Skill ID and domain
- Purpose and use case
- Inputs and outputs
- Risk level and determinism requirement
- Why this skill belongs in afi-skills (not afi-core or afi-reactor)

This summary should be short and precise, so humans can quickly confirm the intent.

---

### 2. Choose the directory

Determine the target directory:

- `skills/<domain>/<skill_id>.md`

Example: `skills/market-structure/liquidity-heatmap-tagger.md`

Confirm the domain folder exists. If not, STOP and escalate (new domains require human approval).

---

### 3. Create SKILL.md using canonical structure

Copy the template structure from `skills/_template.SKILL.md` and fill in all sections:

#### YAML Front-Matter (Required)

```yaml
---
id: <skill_id>                      # Must match filename (without .md)
name: <Human Readable Name>
version: 0.1.0                      # Start at 0.1.0 for new skills
domain: <domain>                    # Must match folder name
description: |
  <10-500 char description>
inputs:
  - name: <param_name>
    type: <string | number | boolean | object | array>
    description: <what it represents>
    required: <true | false>        # Optional, defaults to true
outputs:
  - name: <result_name>
    type: <string | number | boolean | object | array>
    description: <what it returns>
allowed_tools:
  - <tool_identifier>               # e.g., "market-data:read", "none"
risk_level: <low | medium | high>
determinism_required: <true | false>
evals:
  golden_cases_path: evals/<domain>/<skill_id>/golden_cases.json
  expected_properties:
    - <property.path.1>
    - <property.path.2>
owners:
  - <github_handle_or_team>
last_updated: <YYYY-MM-DD>          # Today's date
tags:
  - <tag1>
  - <tag2>
---
```

#### Markdown Body (Required Sections)

After the front-matter `---` delimiter, include:

1. **# Skill Name** (H1 heading)

2. **## Purpose**
   - Explain what problem this skill solves
   - Be specific about AFI Protocol context

3. **## When to Use**
   - Describe triggering conditions or scenarios
   - Bullet list of use cases

4. **## Workflow**
   - Step-by-step execution flow
   - Numbered list with clear descriptions

5. **## Failure Modes / Edge Cases**
   - Document known failure scenarios
   - How the skill should handle them

6. **## Example I/O**
   - At least 2 examples (success case, error case)
   - Use JSON format for inputs/outputs
   - Use realistic AFI-domain data

Optional sections (add if helpful):
- **## Notes for Validators** (for skills used in validation workflows)
- **## Implementation Notes** (technical details for developers)

---

### 4. Create eval stub

If `determinism_required: true`, you MUST create golden cases.

**Create directory and file**:
```bash
mkdir -p evals/<domain>/<skill_id>
touch evals/<domain>/<skill_id>/golden_cases.json
```

**Format** (following existing pattern):

```json
{
  "skill_id": "<skill_id>",
  "version": "0.1.0",
  "description": "Golden cases for <skill_name>",
  "cases": [
    {
      "name": "basic_success_case",
      "description": "Brief description of this test case",
      "input": {
        "param1": "value1",
        "param2": 42
      },
      "expected_output": {
        "result": {
          "status": "success",
          "value": "expected_value"
        }
      },
      "deterministic": true
    },
    {
      "name": "edge_case",
      "description": "Test edge case handling",
      "input": {
        "param1": "",
        "param2": 0
      },
      "expected_output": {
        "result": {
          "status": "success",
          "value": "default_value"
        }
      },
      "deterministic": true
    },
    {
      "name": "error_case",
      "description": "Test error handling",
      "input": {
        "param1": null
      },
      "expected_output": {
        "validation_status": {
          "is_valid": false,
          "warnings": ["param1 is required"]
        }
      },
      "deterministic": true
    }
  ]
}
```

**Rules**:
- Include at least 3 test cases (success, edge, error)
- Cover all `expected_properties` from front-matter
- Use realistic AFI-domain data
- Set `deterministic: true` for all cases if skill is deterministic

If `determinism_required: false`, you may create a simpler eval stub with property ranges instead of exact golden cases.

---

### 5. Run validation

Run at least:

- `npm run lint:skills` — Validate front-matter and security
- `npm run build:manifest` — Build manifest.json and index.yaml

If validation fails:

- Capture error output
- Fix issues or surface them in the summary
- Do NOT mark the skill as successful if validation fails

---

### 6. Summarize

Produce a short summary that includes:

- **Skill ID**: `<skill_id>`
- **Domain**: `<domain>`
- **Version**: `0.1.0` (new skills start here)
- **Risk Level**: `<low | medium | high>`
- **Determinism**: `<true | false>`
- **Files created**:
  - `skills/<domain>/<skill_id>.md`
  - `evals/<domain>/<skill_id>/golden_cases.json` (if deterministic)
- **Eval coverage**: Number of test cases (e.g., "3 golden cases")
- **Validation results**: Pass/fail for lint and manifest build
- **TODOs or open questions**: Any human decision points

Aim for something a human maintainer can read in under a minute to understand exactly what was created and why.

---

## Hard Boundaries

When using this skill, you MUST NOT:

- **Modify existing skills** without explicit instruction:
  - Do NOT change skill IDs (breaks versioning)
  - Do NOT change skill semantics without human approval
  - Do NOT remove required front-matter fields

- **Create new domains** without human approval:
  - Existing domains: market-structure, scoring, news-sentiment, provenance, technical, pattern, ml, ops, governance, other
  - If a skill doesn't fit, use `other/` and document why
  - Do NOT create new top-level domain folders

- **Touch other repos**:
  - Do NOT modify afi-core schemas or validators
  - Do NOT modify afi-reactor DAGs or configs
  - Do NOT modify afi-token contracts or emissions
  - Do NOT modify afi-gateway agents or configs
  - Do NOT modify afi-ops or afi-infra

- **Skip security validation**:
  - Do NOT add skills without running `npm run lint:skills`
  - Do NOT ignore security risk pattern warnings
  - Do NOT add high-risk skills without human approval

- **Skip evals for deterministic skills**:
  - If `determinism_required: true`, golden cases are REQUIRED
  - Do NOT mark a skill as complete without creating eval file

If a request forces you towards any of the above, STOP and escalate.

---

## Output / Summary Format

At the end of a successful `add-skill-from-template` operation, produce a short summary that includes:

**Skill Created**:
- **ID**: `<skill_id>`
- **Domain**: `<domain>`
- **Version**: `0.1.0`
- **Risk Level**: `<low | medium | high>`
- **Determinism**: `<true | false>`

**Files Created**:
- `skills/<domain>/<skill_id>.md` (XXX lines)
- `evals/<domain>/<skill_id>/golden_cases.json` (X test cases) [if deterministic]

**Validation Results**:
- ✅ `npm run lint:skills` — Pass
- ✅ `npm run build:manifest` — Pass

**TODOs**:
- [ ] Fill in more eval cases (currently 3, recommend 5+)
- [ ] Add optional fields (markets, timeframes, risk_notes, confidence_notes)
- [ ] Review security risk patterns
- [ ] Submit PR with clear description

---

## Example Usage Patterns

### Use This Skill For

✅ **Allowed**:

- "Add a new market-structure skill that parses perpetual funding rate mechanics."
  - Domain: `market-structure/`
  - Skill ID: `perp-funding-rate-parser`
  - Deterministic: `true` (parsing is deterministic)
  - Creates skill file + golden cases

- "Create a scoring skill that takes an analyzed signal and emits a RiskTierLabel."
  - Domain: `scoring/`
  - Skill ID: `risk-tier-labeler`
  - Deterministic: `false` (may use probabilistic scoring)
  - Creates skill file + property range eval stub

- "Add a news-sentiment skill that extracts sentiment from headlines."
  - Domain: `news-sentiment/`
  - Skill ID: `headline-sentiment-extractor`
  - Deterministic: `false` (NLP is non-deterministic)
  - Creates skill file + eval stub

- "Create a provenance skill that validates vault replay determinism."
  - Domain: `provenance/`
  - Skill ID: `vault-replay-validator`
  - Deterministic: `true` (validation is deterministic)
  - Creates skill file + golden cases

### Do NOT Use This Skill For

❌ **Forbidden**:

- "Add a new signal schema field called `macro_regime`."
  → Belongs in afi-core (use `extend-signal-schema` skill)

- "Wire the new scoring skill into the DAG pipeline."
  → Belongs in afi-reactor (use `add-dag-node` skill)

- "Create a new domain called `crypto-native/` for blockchain-specific skills."
  → Requires human approval for new domain creation

- "Modify the existing futures-contract-spec skill to add a new input."
  → This skill is for creating NEW skills, not modifying existing ones

- "Add a skill that calls an external API without security review."
  → High-risk skills require human approval before creation

---

## Example: Creating a Market Structure Skill

**Request**: "Add a new market-structure skill that parses perpetual funding rate mechanics."

**Summary**:

**Skill Created**:
- **ID**: `perp-funding-rate-parser`
- **Domain**: `market-structure`
- **Version**: `0.1.0`
- **Risk Level**: `low`
- **Determinism**: `true`

**Files Created**:
- `skills/market-structure/perp-funding-rate-parser.md` (165 lines)
- `evals/market-structure/perp-funding-rate-parser/golden_cases.json` (3 test cases)

**Front-Matter**:
```yaml
id: perp-funding-rate-parser
name: Perpetual Funding Rate Parser
version: 0.1.0
domain: market-structure
description: |
  Parses perpetual funding rate data from exchange APIs and normalizes to AFI format.
  Handles different exchange formats (Binance, Deribit, Bybit) and computes funding intervals.
inputs:
  - name: exchange
    type: string
    description: Exchange identifier (e.g., "binance", "deribit")
  - name: symbol
    type: string
    description: Trading pair symbol (e.g., "BTC-PERP")
  - name: raw_funding_data
    type: object
    description: Raw funding rate data from exchange API
outputs:
  - name: parsed_funding
    type: object
    description: Normalized funding rate data with AFI standard fields
  - name: validation_status
    type: object
    description: Validation results and warnings
allowed_tools:
  - market-data:read
risk_level: low
determinism_required: true
evals:
  golden_cases_path: evals/market-structure/perp-funding-rate-parser/golden_cases.json
  expected_properties:
    - parsed_funding.funding_rate
    - parsed_funding.funding_interval_hours
    - validation_status.is_valid
owners:
  - afi-market-team
last_updated: 2025-11-28
tags:
  - market-structure
  - funding-rate
  - perpetuals
  - parsing
```

**Validation Results**:
- ✅ `npm run lint:skills` — Pass
- ✅ `npm run build:manifest` — Pass

**TODOs**:
- [ ] Add more eval cases (currently 3, recommend 5+ covering all major exchanges)
- [ ] Add optional fields: markets, timeframes, risk_notes
- [ ] Review with afi-market-team before PR

---

## Example: Creating a Scoring Skill

**Request**: "Create a scoring skill that takes an analyzed signal and emits a RiskTierLabel."

**Summary**:

**Skill Created**:
- **ID**: `risk-tier-labeler`
- **Domain**: `scoring`
- **Version**: `0.1.0`
- **Risk Level**: `low`
- **Determinism**: `false`

**Files Created**:
- `skills/scoring/risk-tier-labeler.md` (172 lines)
- `evals/scoring/risk-tier-labeler/golden_cases.json` (property ranges, not exact golden cases)

**Front-Matter**:
```yaml
id: risk-tier-labeler
name: Risk Tier Labeler
version: 0.1.0
domain: scoring
description: |
  Assigns risk tier labels (low/medium/high/extreme) to analyzed signals based on
  volatility, liquidity, and market regime. Enables risk-aware position sizing.
inputs:
  - name: analyzed_signal
    type: object
    description: Analyzed signal with volatility and liquidity metrics
outputs:
  - name: risk_tier
    type: string
    description: Risk tier label (low/medium/high/extreme)
  - name: confidence
    type: number
    description: Classification confidence score (0-1)
allowed_tools:
  - none
risk_level: low
determinism_required: false
evals:
  golden_cases_path: evals/scoring/risk-tier-labeler/golden_cases.json
  expected_properties:
    - risk_tier
    - confidence
owners:
  - afi-scoring-team
last_updated: 2025-11-28
tags:
  - scoring
  - risk-management
  - classification
```

**Validation Results**:
- ✅ `npm run lint:skills` — Pass
- ✅ `npm run build:manifest` — Pass

**TODOs**:
- [ ] Add confidence_notes documenting accuracy metrics
- [ ] Add risk_notes documenting edge cases
- [ ] Review with afi-scoring-team before PR

---

**Last Updated**: 2025-11-28
**Maintainers**: AFI Skills Team
**Charter**: `afi-config/codex/governance/droids/AFI_DROID_CHARTER.v0.1.md`
**Workflow**: `CONTRIBUTING_SKILLS.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/afi-protocol) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
