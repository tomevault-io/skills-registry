---
name: proposal
description: This skill produces **evidence-backed proposals** for technical decisions. Unlike the design skill (which defines architecture), this skill focuses on **proving or disproving** whether a specific approach, library, pattern, or technology is the right choice. Use when this capability is needed.
metadata:
  author: verbalist
---
---
name: proposal
description: Evidence-based proposal skill for evaluating technical decisions, library choices, and architectural changes. Every proposal requires proofs from codebase analysis, web sources, and opinions from big tech and frontier companies. Checks third-party maintainability, funding, bus factor, and long-term risks. Use this skill before making significant technical decisions.
---

# Proposal

Create evidence-based technical proposals with mandatory proof gathering from multiple sources. Every proposal must be backed by codebase analysis, web research, industry opinions, and long-term risk assessment.

## Overview

This skill produces **evidence-backed proposals** for technical decisions. Unlike the design skill (which defines architecture), this skill focuses on **proving or disproving** whether a specific approach, library, pattern, or technology is the right choice.

Every proposal answers: "Should we do X? Here is the evidence."

**Position in SDLC workflow:**

```
User Input --> requirements --> [proposal] --> design --> classify --> plan --> implement
                                    |
                                    +--> Can also be invoked standalone
```

## When to Use This Skill

### Required When

1. **New Third-Party Dependency** - Adding any library, framework, or service
2. **Technology Change** - Switching from one tool/pattern to another
3. **Architecture Decision** - Choosing between multiple valid approaches
4. **Migration Justification** - Proving a migration is worth the effort
5. **Build vs Buy** - Deciding whether to implement in-house or use external

### Skip When

- The decision is already made and approved
- Using well-established internal patterns
- Trivial dependency additions (e.g., adding a type package)
- Bug fixes or patches that don't introduce new tech

## Variables

- `branch_name`: Current git branch name (auto-detected)
- `descriptive_name`: $1 - Short name for the proposal (e.g., "tanstack-table", "redis-caching")
- `proposal_subject`: $2 (optional) - What is being proposed (e.g., "Replace date-fns with Temporal API")

## Instructions

### Step 1: Define the Proposal

Clearly state:
- **What** is being proposed
- **Why** the current approach is insufficient (or what gap exists)
- **Scope** of impact

### Step 2: Analyze the Codebase (PROOF: Codebase)

Search the actual codebase for evidence:

1. **Current usage patterns** - How is this area handled today?
2. **Affected surface area** - How many files, modules, tests would change?
3. **Existing dependencies** - What's already in `package.json`? Any overlap?
4. **Internal patterns** - Does the codebase already have conventions for this?
5. **Technical debt** - What pain points exist in the current approach?

```
PROOF REQUIREMENTS:
- List specific files and line numbers
- Show actual code snippets as evidence
- Count occurrences (e.g., "47 files import date-fns")
- Identify breaking change surface
```

### Step 3: Research Web Sources (PROOF: Web)

Gather data from authoritative web sources:

1. **Official documentation** - Current status, roadmap, breaking changes
2. **npm/bundlephobia** - Download trends, bundle size, dependencies
3. **GitHub repository** - Stars, issues, PRs, last commit, contributor count
4. **Benchmarks** - Performance comparisons from credible sources
5. **Security advisories** - Known CVEs, security audit history
6. **Community discussion** - Reddit, HN, Twitter/X, Discord sentiment

```
PROOF REQUIREMENTS:
- Include URLs for every claim
- Cite specific numbers (downloads/week, bundle size in KB)
- Reference dated benchmarks (no older than 18 months)
- Note any conflicting information found
```

### Step 4: Gather Industry Opinions (PROOF: Industry)

Research what established companies use and recommend:

#### Big Tech (pick at least 3 relevant)
- **Google** - What do they use/recommend? (Angular, Go decisions, AIP standards)
- **Meta** - What do they use/build? (React, Relay, internal tools)
- **Amazon/AWS** - What services/patterns do they promote?
- **Microsoft** - What do they invest in? (TypeScript, VS Code ecosystem)
- **Apple** - Relevant platform decisions
- **Netflix** - Architecture patterns, OSS contributions
- **Uber** - Infrastructure choices, OSS projects

#### Frontier Companies (pick at least 3 relevant)
- **Vercel** - Next.js ecosystem, edge computing, Turbopack
- **Cloudflare** - Workers, edge patterns, performance
- **Supabase** - Database, auth, real-time patterns
- **PlanetScale** - Database scaling, migration patterns
- **Fly.io** - Deployment, distributed systems
- **Railway/Render** - Platform patterns
- **Linear** - Product engineering practices
- **Stripe** - API design, SDK patterns
- **Tailwind Labs** - CSS/UI patterns
- **tRPC/Effect** - TypeScript ecosystem patterns

```
PROOF REQUIREMENTS:
- Cite actual blog posts, talks, or documentation
- Note whether company USES it in production vs merely recommends
- Identify if company has CONTRIBUTED to the technology
- Flag any company that has MIGRATED AWAY and why
```

### Step 5: Evaluate Third-Party Maintainability (CRITICAL)

For every proposed external dependency, assess:

#### Ownership & Governance
- **Who maintains it?** - Individual, company, foundation, community
- **Corporate backing** - Is it backed by a company? Which one? Are they profitable?
- **Governance model** - BDFL, committee, foundation (Apache, Linux, OpenJS)
- **License** - MIT, Apache-2.0, GPL, BSL - is it compatible?

#### Health Metrics
- **Bus factor** - How many core contributors? What if the top 1-2 leave?
- **Commit frequency** - Daily? Weekly? Last commit date?
- **Issue response time** - How fast are issues triaged?
- **Release cadence** - Regular releases or sporadic?
- **Breaking change history** - How often do major versions drop? Migration effort?

#### Funding & Sustainability
- **Funding model** - VC-backed, open-core, donations, corporate sponsor
- **Revenue source** - How does the project sustain itself?
- **OpenCollective/GitHub Sponsors** - What's the funding level?
- **Risk of rug-pull** - Could the license change? (see: HashiCorp, Elastic, Redis)

#### Ecosystem & Alternatives
- **Ecosystem size** - Plugins, integrations, community packages
- **Alternative options** - What else could we use? Why not those?
- **Migration path** - If this dies, how hard is it to switch?

```
MAINTAINABILITY VERDICT:
Rate each dependency: SAFE / ACCEPTABLE / RISKY / AVOID
- SAFE: Corporate-backed, foundation-governed, large contributor base
- ACCEPTABLE: Active community, multiple maintainers, stable funding
- RISKY: Single maintainer, no funding, sporadic updates
- AVOID: Abandoned, license concerns, known instability
```

### Step 6: Analyze Long-Term Risks

Assess risks over a 2-5 year horizon:

#### Technical Risks
- **Lock-in** - How coupled would we become? Can we swap later?
- **Compatibility** - Will it work with our stack in 2 years?
- **Performance at scale** - Will it hold up as data/traffic grows?
- **Upgrade burden** - How painful are major version upgrades historically?

#### Business Risks
- **Vendor dependency** - Single point of failure?
- **License change risk** - History of relicensing in this space?
- **Cost trajectory** - If paid, how have prices changed?
- **Talent availability** - Can we hire developers who know this?

#### Ecosystem Risks
- **Standards alignment** - Is this moving toward or away from web standards?
- **Framework coupling** - Is it tied to a specific framework that could fall out of favor?
- **Deprecation signals** - Any signs the maintainers are losing interest?

```
RISK MATRIX:
| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
```

### Step 7: Write the Proposal

Compile all evidence into the Proposal Format below.

Save to: `docs/proposals/{descriptive-name}/{descriptive-name}-proposal.md`

Research artifacts save to: `docs/proposals/{descriptive-name}/{descriptive-name}-research.md`

### Step 8: Create Footprint and Update State

Document the proposal process and update state for workflow continuity.

## Proposal Format

```md
# Proposal: <title>

## Metadata

branch_name: `{branch-name}`
descriptive_name: `{descriptive-name}`
created: `{ISO 8601 timestamp}`
status: `draft` | `reviewed` | `approved` | `rejected`
decision: `pending` | `accepted` | `rejected` | `deferred`

## Executive Summary

**Proposal**: <one-sentence description of what is proposed>
**Recommendation**: <ADOPT / TRIAL / DEFER / REJECT>
**Confidence**: <HIGH / MEDIUM / LOW>
**Impact**: <HIGH / MEDIUM / LOW>

<3-5 sentence summary of the proposal and recommendation with key reasoning>

---

## 1. Problem Statement

### Current State
<what exists today, with codebase evidence>

### Pain Points
<specific problems, with evidence from codebase and team experience>

### Desired State
<what we want to achieve>

---

## 2. Proposed Solution

### Description
<detailed description of the proposal>

### Alternatives Considered

#### Alternative A: <name>
- **Description**: <what it is>
- **Pros**: <list>
- **Cons**: <list>
- **Why not chosen**: <specific reasoning>

#### Alternative B: <name>
...

#### Alternative C: Do Nothing
- **Description**: Keep current approach
- **Pros**: No migration cost, no risk
- **Cons**: <list current pain points>
- **Why not chosen**: <or why this is actually fine>

---

## 3. Evidence: Codebase Analysis

### Current Usage
<how this area is currently handled in the codebase>

### Affected Surface Area
- **Files impacted**: {count}
- **Modules affected**: {list}
- **Test files needing updates**: {count}

### Code Evidence
<specific file:line references, code snippets, grep results>

### Dependency Overlap
<existing deps that relate, potential conflicts>

---

## 4. Evidence: Web Sources

### Official Status
- **Latest version**: {version}
- **Release date**: {date}
- **Documentation quality**: {assessment}
- **Roadmap**: {key upcoming changes}

### Adoption Metrics
- **npm weekly downloads**: {number}
- **GitHub stars**: {number}
- **Bundle size**: {size} (minified + gzipped)
- **Dependencies**: {count} ({list critical ones})

### Benchmarks
<performance data with source URLs>

### Security
- **Known CVEs**: {count}
- **Last security audit**: {date or "none"}
- **Snyk/Socket score**: {if available}

### Community Sentiment
<summarized opinions from developer communities, with links>

---

## 5. Evidence: Industry Opinions

### Big Tech Usage

| Company | Uses It? | In Production? | Notes |
|---------|----------|----------------|-------|
| Google | {yes/no} | {yes/no} | {details} |
| Meta | {yes/no} | {yes/no} | {details} |
| Amazon | {yes/no} | {yes/no} | {details} |
| Microsoft | {yes/no} | {yes/no} | {details} |
| Netflix | {yes/no} | {yes/no} | {details} |

### Frontier Company Usage

| Company | Uses It? | In Production? | Notes |
|---------|----------|----------------|-------|
| {company} | {yes/no} | {yes/no} | {details} |

### Notable Endorsements or Rejections
<specific blog posts, talks, or statements from industry leaders>

### Migration Stories
<companies that adopted or abandoned this technology, with outcomes>

---

## 6. Third-Party Maintainability Assessment

### Ownership & Governance

| Aspect | Details |
|--------|---------|
| **Maintainer** | {individual/company/foundation} |
| **Corporate Backing** | {company name or "none"} |
| **Governance Model** | {BDFL/committee/foundation} |
| **License** | {license type} |
| **License Compatible** | {yes/no - with our stack} |

### Health Metrics

| Metric | Value | Assessment |
|--------|-------|------------|
| **Core Contributors** | {count} | {good/concerning} |
| **Bus Factor** | {number} | {safe/risky} |
| **Last Commit** | {date} | {active/stale} |
| **Open Issues** | {count} | {manageable/overwhelming} |
| **Issue Response Time** | {avg time} | {fast/slow} |
| **Release Cadence** | {frequency} | {regular/sporadic} |
| **Breaking Changes (last 3y)** | {count major versions} | {stable/volatile} |

### Funding & Sustainability

| Aspect | Details |
|--------|---------|
| **Funding Model** | {VC/open-core/donations/corporate} |
| **Revenue Source** | {description} |
| **Annual Funding** | {amount if public} |
| **Rug-Pull Risk** | {low/medium/high - with reasoning} |

### Maintainability Verdict

**Rating**: {SAFE / ACCEPTABLE / RISKY / AVOID}

<paragraph explaining the rating with specific evidence>

---

## 7. Long-Term Risk Analysis (2-5 Year Horizon)

### Risk Matrix

| Risk | Probability | Impact | Timeframe | Mitigation |
|------|-------------|--------|-----------|------------|
| {risk} | H/M/L | H/M/L | {when} | {strategy} |

### Technical Risk Details
<detailed analysis of each technical risk>

### Business Risk Details
<detailed analysis of each business risk>

### Ecosystem Risk Details
<detailed analysis of ecosystem trajectory>

### Worst-Case Scenario
<what happens if this goes wrong? How do we recover?>

---

## 8. Implementation Considerations

### Migration Effort
- **Estimated scope**: {small/medium/large}
- **Files to change**: {count}
- **Breaking changes to our code**: {list}
- **Rollback strategy**: {how to undo if it fails}

### Proof of Concept
<outline a minimal PoC to validate the proposal before full commitment>

### Adoption Strategy
- **Phase 1**: {initial adoption scope}
- **Phase 2**: {broader rollout}
- **Phase 3**: {full migration, if applicable}

---

## 9. Decision

### Recommendation: {ADOPT / TRIAL / DEFER / REJECT}

**ADOPT**: Proceed with full implementation
**TRIAL**: Implement PoC first, then decide
**DEFER**: Revisit in {timeframe} when {condition}
**REJECT**: Do not proceed because {reason}

### Conditions for Revisiting
<what would change this decision?>

### Decision Rationale
<final summary tying all evidence together>

---

## 10. References

### Web Sources
- [{title}]({url}) - {brief note}

### Blog Posts & Talks
- [{title}]({url}) - {company/author}, {date}

### Codebase References
- `{file:line}` - {what it shows}

### Related Proposals
- `docs/proposals/{name}/{name}-proposal.md` - {relation}
```

## Relevant Files

Research these before writing the proposal:

- `package.json` (root) - Current workspace dependencies
- `**/package.json` - Service/package dependencies
- Lock file - Exact versions in use
- Source modules - Existing implementations
- Architecture docs - Current architecture

## Proof Quality Standards

### What Counts as Proof

| Source Type | Acceptable | Not Acceptable |
|-------------|-----------|----------------|
| **Codebase** | File:line references, grep counts, dependency analysis | "I think we use it somewhere" |
| **Web** | URLs with dates, specific numbers | "It's popular" |
| **Industry** | Blog posts, conference talks, public repos | "Google probably uses it" |
| **Benchmarks** | Reproducible tests with methodology | "It's faster" |
| **Maintainability** | GitHub metrics, funding pages, governance docs | "It seems maintained" |

### Evidence Hierarchy

1. **Primary**: Direct codebase evidence, official docs, GitHub data
2. **Secondary**: Benchmarks, blog posts from maintainers, company engineering blogs
3. **Tertiary**: Community opinions, Reddit threads, Twitter discussions
4. **Inadmissible**: Unsourced claims, outdated benchmarks (>18 months), marketing material

## Best Practices

### 1. Be Honest About Uncertainty

If evidence is unclear or contradictory, say so. A proposal that honestly says "the evidence is mixed" is more valuable than one that cherry-picks.

### 2. Steel-Man the Alternatives

Present alternatives in their strongest form. If you can't articulate why someone would choose the alternative, you haven't researched enough.

### 3. Quantify Where Possible

- "47 files import lodash" > "many files use lodash"
- "2.3KB gzipped" > "small bundle size"
- "14 core contributors" > "active community"

### 4. Check for Recency

Technology moves fast. Verify:
- Is that benchmark still valid with the latest version?
- Did the project change ownership recently?
- Has the license changed?

### 5. Consider Your Specific Context

A library that's great for one stack might not fit yours. Always evaluate through the lens of your actual architecture.

---

## Footprint and State Management

After creating the proposal, you MUST create a footprint and update the state file.

### Footprint Creation

Create a footprint file at: `agentic/{branch-name}/footprints/foot-proposal-{descriptive-name}.md`

**Footprint Template:**

```markdown
# Proposal Footprint: {descriptive-name}

**Date**: {ISO 8601 timestamp}
**Subject**: {what was proposed}
**Type**: Proposal / Technical Decision

## Research Summary

### Codebase Analysis
- **Files examined**: {count}
- **Key findings**: {list}
- **Current pain points identified**: {count}

### Web Research
- **Sources consulted**: {count}
- **URLs referenced**: {count}
- **Key data points**: {list}

### Industry Research
- **Big tech opinions gathered**: {count}
- **Frontier company opinions gathered**: {count}
- **Notable endorsements**: {list}
- **Notable rejections**: {list}

### Maintainability Assessment
- **Dependencies evaluated**: {count}
- **Ratings**: {list with verdicts}
- **Critical concerns**: {list or "none"}

## Proposal Result

**Proposal File**: docs/proposals/{descriptive-name}/{descriptive-name}-proposal.md
**Recommendation**: {ADOPT / TRIAL / DEFER / REJECT}
**Confidence**: {HIGH / MEDIUM / LOW}
**Maintainability Verdict**: {SAFE / ACCEPTABLE / RISKY / AVOID}

**Key Evidence Points**:
{top 3-5 most compelling evidence items}

**Key Risks**:
{top 3 risks with mitigation strategies}

## Next Steps

**If ADOPT/TRIAL**: Proceed to /design or /classify
**If DEFER**: Revisit conditions: {conditions}
**If REJECT**: Alternative recommendation: {alternative}
```

### State File Update

Create or update the state file at: `agentic/{branch-name}/state.json`

```json
{
  "latest_footprint": "agentic/{branch-name}/footprints/foot-proposal-{descriptive-name}.md",
  "latest_command": "proposal",
  "proposal_file_path": "docs/proposals/{descriptive-name}/{descriptive-name}-proposal.md",
  "next_command_metadata": {
    "command": "/design",
    "category": "proposal",
    "confidence": "{HIGH|MEDIUM|LOW}",
    "reasoning": "Proposal complete, ready for design/planning",
    "required_context": "docs/proposals/{descriptive-name}/{descriptive-name}-proposal.md"
  },
  "next_command": "/design",
  "proposal_timestamp": "{ISO 8601 timestamp}",
  "branch_name": "{branch-name}",
  "descriptive_name": "{descriptive-name}",
  "proposal_subject": "{subject}",
  "recommendation": "{ADOPT|TRIAL|DEFER|REJECT}",
  "maintainability_verdict": "{SAFE|ACCEPTABLE|RISKY|AVOID}",
  "evidence_summary": {
    "codebase_files_examined": "{count}",
    "web_sources_cited": "{count}",
    "industry_opinions_gathered": "{count}",
    "dependencies_evaluated": "{count}"
  }
}
```

---

## Required Actions

After completing the proposal, you MUST:

1. **Create Proposal Dir**: Create `docs/proposals/{descriptive-name}/` directory
2. **Create Proposal Doc**: Write to `docs/proposals/{descriptive-name}/{descriptive-name}-proposal.md`
3. **Create Research Doc**: Write research artifacts to `docs/proposals/{descriptive-name}/{descriptive-name}-research.md`
4. **Create Footprint**: Document process in `agentic/{branch-name}/footprints/foot-proposal-{descriptive-name}.md`
5. **Update State**: Create/update `agentic/{branch-name}/state.json`

## Report

Return a JSON object with the following structure:

```json
{
  "proposal_file_path": "docs/proposals/{descriptive-name}/{descriptive-name}-proposal.md",
  "research_file_path": "docs/proposals/{descriptive-name}/{descriptive-name}-research.md",
  "recommendation": "ADOPT | TRIAL | DEFER | REJECT",
  "confidence": "HIGH | MEDIUM | LOW",
  "maintainability_verdict": "SAFE | ACCEPTABLE | RISKY | AVOID",
  "evidence_counts": {
    "codebase_files": "{count}",
    "web_sources": "{count}",
    "industry_opinions": "{count}",
    "dependencies_evaluated": "{count}",
    "risks_identified": "{count}"
  },
  "key_finding": "{one-sentence most important finding}",
  "next_command": "/design | /classify | (none if rejected/deferred)",
  "footprint_path": "agentic/{branch-name}/footprints/foot-proposal-{descriptive-name}.md",
  "state_path": "agentic/{branch-name}/state.json"
}
```

---

## Examples

### Example 1: Library Proposal

```
/proposal tanstack-table "Replace ag-grid with TanStack Table for data grids"
```

Produces a full evidence document evaluating TanStack Table vs ag-grid with codebase impact, bundle size comparison, industry adoption, maintainability of both libraries, and long-term risk assessment.

### Example 2: Architecture Proposal

```
/proposal redis-caching "Add Redis caching layer for API responses"
```

Evaluates Redis vs alternatives (in-memory, SQLite cache, Cloudflare KV), checks who maintains the Redis Node client, assesses operational complexity, and recommends TRIAL with a PoC scope.

### Example 3: Migration Proposal

```
/proposal temporal-api "Replace date-fns with Temporal API"
```

Analyzes all 47 files using date-fns, checks Temporal API browser support, notes Google's TC39 championing, flags that Temporal polyfill is maintained by Igalia (funded by Bloomberg), and recommends DEFER until browser support reaches 90%.

---

## Integration with SDLC

### In Full SDLC Flow

```
requirements --> proposal --> design --> classify --> plan --> implement
```

The proposal provides evidence that the design skill can reference for architectural decisions.

### Standalone Usage

```
/proposal {name} "{subject}"
```

Can be used independently to evaluate any technical decision, even outside a feature workflow.

### Feeding into Design

The design skill should reference the proposal:

```md
## References
- `docs/proposals/{name}/{name}-proposal.md` - Evidence-based evaluation of {subject}
```

### Feeding into Planning

Planning skills can reference the proposal for implementation scope:

```md
## Relevant Files
- `docs/proposals/{name}/{name}-proposal.md` - Proposal with maintainability assessment and risk analysis
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/verbalist) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
