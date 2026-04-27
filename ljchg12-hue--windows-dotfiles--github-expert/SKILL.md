---
name: github-expert
description: Expert GitHub research including repository analysis, code search, contributor patterns, and project evaluation Use when this capability is needed.
metadata:
  author: ljchg12-hue
---

# GitHub Expert

## Purpose
Analyze GitHub repositories, search code patterns, evaluate projects, and extract insights from the open-source ecosystem.

## Activation Keywords
- github, repository, repo
- open source, OSS
- code search, find code
- project analysis, repo evaluation
- contributors, stars, forks

## Core Capabilities

### 1. Repository Analysis
- Project health metrics
- Code quality indicators
- Dependency analysis
- Security assessment
- Activity patterns

### 2. Code Search
- Pattern matching
- Cross-repo search
- Implementation examples
- Usage patterns
- Best practices discovery

### 3. Project Evaluation
- Maintenance status
- Community health
- Documentation quality
- Release cadence
- Issue responsiveness

### 4. Contributor Analysis
- Key contributors
- Contribution patterns
- Bus factor
- Community diversity
- Sponsorship/backing

### 5. Trend Analysis
- Rising projects
- Technology adoption
- Language trends
- Framework popularity
- Ecosystem mapping

## Repository Health Metrics

| Metric | Good | Warning | Concern |
|--------|------|---------|----------|
| Last commit | <1 month | 1-6 months | >6 months |
| Open issues | Managed | Growing | Abandoned |
| PR response | <1 week | 1-4 weeks | >1 month |
| Contributors | 5+ active | 2-4 active | 1 only |
| Stars growth | Steady | Flat | Declining |
| Security | Up-to-date | Minor issues | Vulnerabilities |

## Repository Evaluation Template

```markdown
## Repository Analysis: [owner/repo]

### Overview
- **Stars**: X | **Forks**: Y | **Open Issues**: Z
- **Language**: Primary (X%), Secondary (Y%)
- **License**: [License type]
- **Created**: [Date] | **Last Commit**: [Date]

### Health Score: [A-F]

### Metrics
| Aspect | Score | Notes |
|--------|-------|-------|
| Maintenance | X/10 | [Assessment] |
| Documentation | X/10 | [Assessment] |
| Community | X/10 | [Assessment] |
| Security | X/10 | [Assessment] |
| Code Quality | X/10 | [Assessment] |

### Key Findings
- [Finding 1]
- [Finding 2]
- [Finding 3]

### Recommendations
- [Use/Don't use for X]
- [Alternative if applicable]

### Red Flags
- [Any concerns]

### Dependencies
- Key deps: [List]
- Vulnerabilities: [Count]
```

## Code Search Patterns

```
# Find specific implementations
language:typescript "useEffect" filename:*.tsx

# Find configuration patterns
filename:tsconfig.json "strict": true

# Find security patterns
language:javascript crypto.createHash

# Find API usage
language:python "requests.post" path:src/

# Find test patterns
language:javascript describe( path:test/
```

## Project Comparison Framework

```markdown
## Comparison: [Project A] vs [Project B]

| Criteria | Project A | Project B |
|----------|-----------|------------|
| Stars | X | Y |
| Weekly downloads | X | Y |
| Bundle size | X KB | Y KB |
| Last release | Date | Date |
| TypeScript | Yes/No | Yes/No |
| Maintenance | Active/Slow | Active/Slow |
| Learning curve | Low/Med/High | Low/Med/High |

### Recommendation
[Which to use and why]
```

## Example Usage

```
User: "Analyze the Prisma ORM repository"

GitHub Expert Response:

## Repository Analysis: prisma/prisma

### Overview
- Stars: 36k+ | Forks: 1.4k | Open Issues: 2.5k
- Language: TypeScript (92%), Rust (5%)
- License: Apache-2.0
- Created: 2016 | Last Commit: Today

### Health Score: A

### Assessment
| Aspect | Score | Notes |
|--------|-------|-------|
| Maintenance | 10/10 | Daily commits, active team |
| Documentation | 9/10 | Excellent docs site |
| Community | 9/10 | Active Discord, discussions |
| Security | 9/10 | Regular updates |
| Code Quality | 9/10 | Strict TypeScript, tested |

### Key Findings
- Enterprise-backed (Prisma Data Inc.)
- Regular major releases
- Strong TypeScript support
- Growing ecosystem (Accelerate, Pulse)

### Recommendation
Excellent choice for TypeScript/Node.js projects.
Production-ready with strong support.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ljchg12-hue) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
