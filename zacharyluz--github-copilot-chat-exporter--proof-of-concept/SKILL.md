---
name: proof-of-concept
description: Rapid validation of technical feasibility and approach before full implementation. Use when evaluating new technologies, validating approaches, testing integrations, or demonstrating feasibility to stakeholders. Use when this capability is needed.
metadata:
  author: zacharyluz
---

# Proof of Concept (PoC)

## Core Principle

**Prove the concept, not the implementation. Learn fast, decide faster.**

Good PoCs:
- **Have clear success criteria** — Know what you're validating
- **Are time-boxed** — Days, not weeks
- **Focus on risk** — Test what you're unsure about
- **Lead to decisions** — Go/no-go with evidence
- **Are disposable** — Code gets thrown away, knowledge doesn't

---

## When to Use

Use this skill when:
- Evaluating new technologies or frameworks
- Validating technical approaches before committing
- Making architectural decisions with unknowns
- Testing third-party integrations
- Demonstrating feasibility to stakeholders
- De-risking major technical initiatives
- Choosing between competing solutions

Do NOT use a PoC when:
- Requirements are clear and solution is known
- Technology is already familiar to the team
- Time pressure requires immediate production work
- The problem is small enough to just solve directly

---

## PoC vs Prototype vs MVP

### Proof of Concept (PoC)
- **Purpose**: Validate technical feasibility
- **Audience**: Technical team
- **Duration**: 1-5 days
- **Quality**: Throwaway code
- **Outcome**: Go/no-go decision

### Prototype
- **Purpose**: Explore UX/design
- **Audience**: Designers, stakeholders
- **Duration**: 1-2 weeks
- **Quality**: Functional but not production-ready
- **Outcome**: Design validation

### MVP (Minimum Viable Product)
- **Purpose**: Test market fit
- **Audience**: End users
- **Duration**: Weeks to months
- **Quality**: Production-ready but minimal
- **Outcome**: Product-market validation

---

## PoC Planning

### Step 1: Define Success Criteria

**Before writing any code**, answer these questions:

**What question are we answering?**
- ✅ "Can we migrate 10M records from MySQL to PostgreSQL without downtime?"
- ✅ "Does GraphQL reduce our API payload size by >30%?"
- ✅ "Can WebRTC handle our video conferencing requirements?"
- ❌ "Is GraphQL good?" (Too vague)

**What does success look like?**
- Measurable outcomes
- Pass/fail criteria
- Performance benchmarks
- Integration validation

**Example Success Criteria:**

```markdown
## Success Criteria

1. ✅ Authentication flow works with our existing OAuth provider
2. ✅ Page load time < 2 seconds with 1000 items
3. ✅ Can integrate with our existing API gateway
4. ✅ Team can understand and modify code after 1-hour tutorial
5. ❌ Production-ready error handling (out of scope)
```

---

### Step 2: Identify Key Risks

**What are we unsure about?**

Focus the PoC on your biggest unknowns:

**Technical Risks:**
- Performance (will it be fast enough?)
- Integration (will it work with our systems?)
- Scalability (will it handle our load?)
- Security (does it meet our requirements?)

**Team Risks:**
- Learning curve (can we learn it quickly?)
- Documentation (is it well-documented?)
- Ecosystem (are there libraries we need?)
- Support (can we get help when stuck?)

**Operational Risks:**
- Deployment (can we deploy it?)
- Monitoring (can we observe it?)
- Maintenance (who will maintain it?)
- Cost (is it within budget?)

---

### Step 3: Set Time Boundaries

**PoCs should be time-boxed.**

| PoC Scope | Time Budget |
|-----------|-------------|
| Simple integration | 1-2 days |
| Technology evaluation | 2-3 days |
| Complex system integration | 3-5 days |
| Architecture pattern validation | 3-5 days |

**If you need more than 5 days, you're building too much.**

Strategies for staying on time:
- Use time boxing (e.g., 2-hour chunks)
- Cut scope ruthlessly
- Focus on one thing at a time
- Stop when you have your answer

---

### Step 4: Determine Scope

#### What to Include ✅

**Core Validation:**
- The feature/capability you're testing
- Integration with critical systems
- Performance benchmarks (if relevant)
- Security basics (if applicable)

**Example:** Testing a new search engine
- ✅ Index sample data (1000-10000 records)
- ✅ Implement basic search query
- ✅ Measure query response time
- ✅ Test with realistic data volume
- ✅ Verify relevance of results

#### What to Exclude ❌

**Production Concerns:**
- ❌ Comprehensive error handling
- ❌ Edge case coverage
- ❌ Full test suite
- ❌ Scalability optimizations
- ❌ Complete UI/UX
- ❌ Deployment infrastructure
- ❌ Monitoring and alerting
- ❌ Documentation (beyond PoC notes)
- ❌ Security hardening
- ❌ Performance optimization

**Example:** Testing a new search engine
- ❌ Full production data (millions of records)
- ❌ Advanced filtering features
- ❌ User authentication
- ❌ Caching layer
- ❌ Distributed deployment
- ❌ Query analytics

---

## PoC Execution Process

### Phase 1: Research (20% of time)

**Before coding, do your homework:**

1. **Read official documentation**
   - Getting started guides
   - Architecture overview
   - Best practices

2. **Find working examples**
   - Official examples
   - GitHub repositories
   - Tutorial projects

3. **Check compatibility**
   - Language/framework versions
   - Operating system requirements
   - Dependency conflicts

4. **Identify potential blockers**
   - Known limitations
   - Missing features
   - Integration challenges

---

### Phase 2: Minimal Setup (10% of time)

**Get to "Hello World" as fast as possible:**

1. **Use scaffolding tools**
   - Framework CLIs (create-react-app, rails new, etc.)
   - Starter templates
   - Docker containers

2. **Skip optional setup**
   - Linters (unless testing DX)
   - CI/CD
   - Testing frameworks
   - Code formatters

3. **Use hosted services**
   - Managed databases
   - Cloud functions
   - SaaS integrations

**Goal**: Write domain code, not boilerplate

---

### Phase 3: Core Implementation (50% of time)

**Build the minimum to test your hypothesis:**

**Focus on:**
- The specific feature/integration you're validating
- Realistic data (not "hello world")
- Critical path functionality
- Key integration points

**Example Structure:**

```
poc-new-search/
├── README.md              # What we're testing
├── setup.sh               # How to run
├── src/
│   ├── index.js           # Core search implementation
│   └── integration.js     # Integration with our API
├── data/
│   └── sample-data.json   # Realistic test data
└── results.md             # Findings
```

**Code Quality Expectations:**
- Working code (not production code)
- Comments on key decisions
- No tests (unless testing is what you're evaluating)
- Hardcoded values are fine
- Copy-paste is acceptable

---

### Phase 4: Testing Against Criteria (15% of time)

**Validate your success criteria:**

1. **Functional Testing**
   - Does it work?
   - Does it integrate?
   - Does it solve the problem?

2. **Performance Testing** (if relevant)
   - Basic benchmarks
   - Realistic data volumes
   - Identify bottlenecks

3. **Developer Experience** (if relevant)
   - Setup time
   - Code clarity
   - Debugging experience

**Document your findings as you go.**

---

### Phase 5: Documentation (5% of time)

**Capture your learnings:**

1. **What worked?**
2. **What didn't work?**
3. **What surprised you?**
4. **What questions remain?**

See [PoC Documentation Template](#poc-documentation-template) below.

---

## PoC Documentation Template

```markdown
# PoC: [Technology/Approach Name]

**Date**: 2026-01-10
**Author**: [Your Name]
**Duration**: [X days]

---

## Problem Statement

[What problem are we trying to solve?]

**Current situation:**
[What we do now]

**Desired outcome:**
[What we want to achieve]

---

## Success Criteria

| Criterion | Target | Result | Status |
|-----------|--------|--------|--------|
| [Criterion 1] | [Target] | [Actual] | ✅/❌ |
| [Criterion 2] | [Target] | [Actual] | ✅/❌ |
| [Criterion 3] | [Target] | [Actual] | ✅/❌ |

---

## Approach Evaluated

**Technology/Framework**: [Name and version]

**Why this approach?**
[Brief rationale]

**Key features tested:**
1. [Feature 1]
2. [Feature 2]
3. [Feature 3]

---

## Implementation Summary

**What was built:**
[Brief description of what you implemented]

**Architecture:**
[Simple diagram or description]

**Key code:**
[Link to repo or key snippets]

---

## Results

### What Worked ✅

- [Thing 1 that worked well]
- [Thing 2 that worked well]
- [Thing 3 that worked well]

### What Didn't Work ❌

- [Challenge 1]
- [Challenge 2]
- [Challenge 3]

### What Surprised Us 🔍

- [Unexpected finding 1]
- [Unexpected finding 2]

### Performance Metrics

| Metric | Target | Actual | Status |
|--------|--------|--------|--------|
| Response time | < 100ms | 85ms | ✅ |
| Memory usage | < 500MB | 320MB | ✅ |
| Throughput | > 1000/s | 850/s | ⚠️ |

---

## Recommendation

**Decision**: ✅ Proceed / ⚠️ Proceed with conditions / ❌ Do not proceed

**Rationale**:
[Why this recommendation?]

**Risks & Mitigations**:
- **Risk 1**: [Description]
  - **Mitigation**: [How to handle it]
- **Risk 2**: [Description]
  - **Mitigation**: [How to handle it]

**Conditions** (if applicable):
- [Condition 1]
- [Condition 2]

---

## Follow-Up Work

**If we proceed, next steps:**

1. [Step 1]
2. [Step 2]
3. [Step 3]

**Estimated effort**: [Time/complexity estimate]

**Open questions:**
- [Question 1]
- [Question 2]

---

## References

- [Documentation link]
- [Example project link]
- [Related research]
- [PoC repository]

---

## Appendix

### Setup Instructions

```bash
# How to run this PoC
```

### Key Code Snippets

```language
// Important code examples
```

### Screenshots/Diagrams

[If applicable]
```

---

## PoC Examples

### Example 1: Technology Evaluation

**Scenario**: Evaluating Elasticsearch vs Typesense for product search

**Success Criteria:**
1. Query response time < 100ms for 10k products
2. Relevance: Top 5 results are relevant
3. Setup time < 4 hours
4. Can integrate with existing Node.js API

**Time Budget**: 3 days (1.5 days per option)

**What to Test:**

**Elasticsearch PoC:**
```javascript
// poc-elasticsearch/
// - Setup Elasticsearch locally (Docker)
// - Index 10k sample products
// - Implement basic search endpoint
// - Benchmark query performance
// - Test relevance with sample queries
```

**Typesense PoC:**
```javascript
// poc-typesense/
// - Setup Typesense Cloud (faster)
// - Index same 10k products
// - Implement same search endpoint
// - Benchmark query performance
// - Test relevance with same queries
```

**Evaluation Matrix:**

| Criterion | Weight | Elasticsearch | Typesense |
|-----------|--------|---------------|-----------|
| Query speed | HIGH | ⚠️ 120ms | ✅ 65ms |
| Relevance | HIGH | ✅ 9/10 | ✅ 8/10 |
| Setup time | MED | ❌ 6 hours | ✅ 2 hours |
| Integration | HIGH | ✅ Easy | ✅ Easy |
| Cost | MED | ⚠️ Self-hosted | ✅ $29/mo |

**Recommendation**: Proceed with Typesense
- Faster queries
- Much easier setup
- Good enough relevance
- Lower operational overhead

---

### Example 2: Integration Validation

**Scenario**: Can we integrate Auth0 with our existing Laravel app?

**Success Criteria:**
1. Users can log in via Auth0
2. Existing user data migrates cleanly
3. Session handling works with our architecture
4. Integration takes < 2 days

**Time Budget**: 2 days

**What to Test:**

```php
// poc-auth0-integration/
// Day 1:
// - Setup Auth0 tenant
// - Integrate Auth0 PHP SDK
// - Implement login flow
// - Test with sample users

// Day 2:
// - Migrate 100 sample users
// - Test session handling
// - Verify existing middleware compatibility
// - Document integration steps
```

**Results:**
- ✅ Login flow works seamlessly
- ✅ User migration script successful
- ❌ Session handling conflicts with existing Redis setup
  - **Mitigation**: Need to refactor session middleware (1-2 days)
- ✅ Integration complexity acceptable

**Recommendation**: Proceed with conditions
- Add 2 days for session refactoring
- Plan user migration strategy
- Test with staging environment first

---

### Example 3: Performance Validation

**Scenario**: Can we use WebSockets for real-time updates instead of polling?

**Success Criteria:**
1. Handle 1000 concurrent connections
2. Message latency < 50ms
3. Memory usage < 500MB
4. Reliable reconnection on network issues

**Time Budget**: 3 days

**What to Test:**

```javascript
// poc-websockets/
// Day 1:
// - Setup Socket.io server
// - Implement basic pub/sub
// - Test with 10 concurrent clients

// Day 2:
// - Load test with 1000 connections
// - Measure memory and CPU
// - Test reconnection logic

// Day 3:
// - Integrate with existing API
// - Test authentication flow
// - Document findings
```

**Load Test Results:**

```bash
# Test with 1000 concurrent connections
Connections: 1000
Messages/sec: 5000
Avg latency: 42ms ✅
Memory usage: 380MB ✅
CPU usage: 45% ⚠️
```

**Findings:**
- ✅ Meets all performance criteria
- ⚠️ Higher CPU usage than expected (needs monitoring)
- ✅ Reconnection works reliably
- ✅ 10x reduction in server requests vs polling

**Recommendation**: Proceed
- Significant performance improvement
- CPU usage acceptable for our infrastructure
- Consider horizontal scaling if needed

---

### Example 4: Architecture Pattern Validation

**Scenario**: Should we adopt Event Sourcing for our order system?

**Success Criteria:**
1. Can model order lifecycle as events
2. Can reconstruct state from events
3. Query performance acceptable
4. Team can understand the pattern

**Time Budget**: 4 days

**What to Test:**

```typescript
// poc-event-sourcing/
// Day 1-2:
// - Model order events (OrderPlaced, OrderPaid, etc.)
// - Implement event store (simple file-based)
// - Build event replay mechanism

// Day 3:
// - Build read model (projection)
// - Test query performance
// - Compare to current approach

// Day 4:
// - Team review session
// - Document trade-offs
```

**Events Modeled:**
```typescript
OrderPlaced → OrderPaid → OrderShipped → OrderDelivered
```

**Findings:**
- ✅ Event model is clean and expressive
- ✅ Event replay works correctly
- ⚠️ Query performance slower than current (200ms vs 50ms)
  - Requires optimized projections
- ❌ Team finds pattern complex (3-hour learning curve)

**Trade-offs:**

| Aspect | Current (CRUD) | Event Sourcing |
|--------|----------------|----------------|
| Simplicity | ✅ Very simple | ❌ Complex |
| Audit trail | ❌ Limited | ✅ Complete |
| Query speed | ✅ Fast | ⚠️ Slower |
| Debugging | ✅ Easy | ⚠️ Harder |
| Flexibility | ⚠️ Limited | ✅ High |

**Recommendation**: Do not proceed (for now)
- Complexity outweighs benefits for our use case
- Team learning curve too steep
- Current audit logging is sufficient
- Revisit if audit requirements change

---

## PoC Anti-Patterns

### 1. Scope Creep

❌ **Anti-pattern**: Adding features beyond validation criteria

**Signs:**
- "While we're at it, let's add..."
- PoC takes 2+ weeks
- Building "production-quality" code
- Adding features for completeness

**Example:**
```
Initial: "Test if GraphQL reduces payload size"
Scope creep:
  → Add authentication
  → Add caching layer
  → Add comprehensive error handling
  → Add monitoring
  → Add documentation site
```

**Fix:**
- Write success criteria upfront
- Stick to the criteria ruthlessly
- Say "no" to nice-to-haves
- Time-box the work

---

### 2. Production-izing the PoC

❌ **Anti-pattern**: Using PoC code in production

**Signs:**
- "The PoC works, let's just ship it"
- Refactoring PoC code for months
- Building on technical debt
- Security vulnerabilities

**Why it's bad:**
- PoC code lacks error handling
- No tests or monitoring
- Security not considered
- Performance not optimized

**Fix:**
- Treat PoC as throwaway
- Use PoC to inform new implementation
- Budget time for proper development
- Don't let time pressure force shortcuts

**Proper Approach:**
```
PoC (3 days) → Decision (1 day) → Implementation (2 weeks)
```

---

### 3. Skipping Documentation

❌ **Anti-pattern**: Not documenting PoC findings

**Signs:**
- "I'll document it later"
- Team doesn't know what was learned
- Repeat same PoC months later
- No record of decision rationale

**Fix:**
- Document as you go
- Use the PoC template
- Share findings with team
- Record the decision (see ADR skill)

---

### 4. No Success Criteria

❌ **Anti-pattern**: Starting PoC without clear goals

**Signs:**
- "Let's just try it and see"
- No clear end state
- Can't decide if PoC succeeded
- Endless tinkering

**Example:**
```
Bad:  "Let's try Redis"
Good: "Can Redis reduce our API response time from 200ms to <100ms?"
```

**Fix:**
- Define success criteria before starting
- Make criteria measurable
- Get stakeholder agreement
- Use criteria to guide work

---

### 5. Ignoring Negative Results

❌ **Anti-pattern**: Continuing despite PoC failure

**Signs:**
- "It didn't work, but let's try longer"
- Moving goalposts when criteria aren't met
- Sunk cost fallacy
- Ignoring evidence

**Example:**
```
Success Criteria: "Query time < 100ms"
Actual Result: 300ms consistently
Wrong: "Let's optimize it more"
Right: "This doesn't meet our needs, try alternative"
```

**Fix:**
- Accept negative results as valuable
- PoC that says "no" is successful
- Document why it didn't work
- Move on to alternatives

**Remember**: A PoC that says "don't do this" is just as valuable as one that says "do this."

---

## PoC Checklist

### Before Starting

- [ ] Success criteria clearly defined
- [ ] Key risks identified
- [ ] Time budget set (< 5 days)
- [ ] Scope determined (what to include/exclude)
- [ ] Research completed (docs, examples)
- [ ] Stakeholders aligned on goals

### During PoC

- [ ] Staying within time budget
- [ ] Focusing on success criteria
- [ ] Documenting findings as you go
- [ ] Testing with realistic data
- [ ] Avoiding scope creep
- [ ] Not over-engineering

### After PoC

- [ ] All success criteria evaluated
- [ ] Results documented
- [ ] Recommendation made (go/no-go)
- [ ] Risks and mitigations identified
- [ ] Follow-up work estimated
- [ ] Decision shared with team
- [ ] PoC code archived/deleted

---

## Integration with Other Skills

### With Technical Research

- Use research skill to identify candidates
- Use PoC to validate top choices
- Combine research + PoC for decisions

**Workflow:**
```
Research (identify 3 options)
  → PoC (validate top 2)
  → Decision
```

### With Architecture Review

- Use PoC to validate architecture decisions
- Test integration points before committing
- Validate performance assumptions

### With Technical Lead Role

- Use PoCs to de-risk technical decisions
- Demonstrate feasibility to stakeholders
- Guide team toward evidence-based decisions

### With Evaluation Skill

- Evaluation provides criteria
- PoC provides hands-on validation
- Together they inform decisions

---

## Quick Reference

### PoC Time Budgets

| Scope | Time |
|-------|------|
| Simple integration | 1-2 days |
| Technology evaluation | 2-3 days |
| Complex integration | 3-5 days |
| Architecture pattern | 3-5 days |

### PoC Phases

1. **Research** (20%) - Read docs, find examples
2. **Setup** (10%) - Get to "Hello World"
3. **Implementation** (50%) - Build core feature
4. **Testing** (15%) - Validate criteria
5. **Documentation** (5%) - Capture learnings

### Success Criteria Template

```markdown
## Success Criteria
1. [Functional requirement - does it work?]
2. [Performance requirement - is it fast enough?]
3. [Integration requirement - does it fit?]
4. [Team requirement - can we use it?]
```

### PoC vs Production

| Aspect | PoC | Production |
|--------|-----|------------|
| Error handling | Minimal | Comprehensive |
| Testing | Manual only | Automated suite |
| Performance | Good enough | Optimized |
| Security | Basic | Hardened |
| Documentation | Notes | Complete |
| Monitoring | None | Full observability |
| Code quality | Working | Maintainable |

---

**Remember**: The goal of a PoC is to learn and decide, not to ship. Build the minimum to answer your question, document your findings, and make an informed decision. Good PoCs save weeks of wasted effort by catching problems early.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zacharyluz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
