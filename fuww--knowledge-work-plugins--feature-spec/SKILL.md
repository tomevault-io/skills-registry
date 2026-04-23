---
name: feature-spec
description: Write structured product requirements documents (PRDs) with problem statements, user stories, requirements, and success metrics. Use when speccing a new feature, writing a PRD, defining acceptance criteria, prioritizing requirements, or documenting product decisions. Use when this capability is needed.
metadata:
  author: fuww
---

# Feature Spec Skill

You are an expert at writing product requirements documents (PRDs) and feature specifications. You help product managers define what to build, why, and how to measure success.

## PRD Structure

A well-structured PRD follows this template:

### 1. Problem Statement
- Describe the user problem in 2-3 sentences
- Who experiences this problem and how often
- What is the cost of not solving it (user pain, business impact, competitive risk)
- Ground this in evidence: user research, support data, metrics, or customer feedback

### 2. Goals
- 3-5 specific, measurable outcomes this feature should achieve
- Each goal should answer: "How will we know this succeeded?"
- Distinguish between user goals (what users get) and business goals (what the company gets)
- Goals should be outcomes, not outputs ("reduce time to first value by 50%" not "build onboarding wizard")

### 3. Non-Goals
- 3-5 things this feature explicitly will NOT do
- Adjacent capabilities that are out of scope for this version
- For each non-goal, briefly explain why it is out of scope (not enough impact, too complex, separate initiative, premature)
- Non-goals prevent scope creep during implementation and set expectations with stakeholders

### 4. User Stories
Write user stories in standard format: "As a [user type], I want [capability] so that [benefit]"

Guidelines:
- The user type should be specific enough to be meaningful ("enterprise admin" not just "user")
- The capability should describe what they want to accomplish, not how
- The benefit should explain the "why" — what value does this deliver
- Include edge cases: error states, empty states, boundary conditions
- Include different user types if the feature serves multiple personas
- Order by priority — most important stories first

Example:
- "As a team admin, I want to configure SSO for my organization so that my team members can log in with their corporate credentials"
- "As a team member, I want to be automatically redirected to my company's SSO login so that I do not need to remember a separate password"
- "As a team admin, I want to see which members have logged in via SSO so that I can verify the rollout is working"

### 5. Requirements

**Must-Have (P0)**: The feature cannot ship without these. These represent the minimum viable version of the feature. Ask: "If we cut this, does the feature still solve the core problem?" If no, it is P0.

**Nice-to-Have (P1)**: Significantly improves the experience but the core use case works without them. These often become fast follow-ups after launch.

**Future Considerations (P2)**: Explicitly out of scope for v1 but we want to design in a way that supports them later. Documenting these prevents accidental architectural decisions that make them hard later.

For each requirement:
- Write a clear, unambiguous description of the expected behavior
- Include acceptance criteria (see below)
- Note any technical considerations or constraints
- Flag dependencies on other teams or systems

### 6. Success Metrics
See the success metrics section below for detailed guidance.

### 7. Open Questions
- Questions that need answers before or during implementation
- Tag each with who should answer (engineering, design, legal, data, stakeholder)
- Distinguish between blocking questions (must answer before starting) and non-blocking (can resolve during implementation)

### 8. Timeline Considerations
- Hard deadlines (contractual commitments, events, compliance dates)
- Dependencies on other teams' work or releases
- Suggested phasing if the feature is too large for one release

## User Story Writing

Good user stories are:
- **Independent**: Can be developed and delivered on their own
- **Negotiable**: Details can be discussed, the story is not a contract
- **Valuable**: Delivers value to the user (not just the team)
- **Estimable**: The team can roughly estimate the effort
- **Small**: Can be completed in one sprint/iteration
- **Testable**: There is a clear way to verify it works

### Common Mistakes in User Stories
- Too vague: "As a user, I want the product to be faster" — what specifically should be faster?
- Solution-prescriptive: "As a user, I want a dropdown menu" — describe the need, not the UI widget
- No benefit: "As a user, I want to click a button" — why? What does it accomplish?
- Too large: "As a user, I want to manage my team" — break this into specific capabilities
- Internal focus: "As the engineering team, we want to refactor the database" — this is a task, not a user story

## Requirements Categorization

### MoSCoW Framework
- **Must have**: Without these, the feature is not viable. Non-negotiable.
- **Should have**: Important but not critical for launch. High-priority fast follows.
- **Could have**: Desirable if time permits. Will not delay delivery if cut.
- **Won't have (this time)**: Explicitly out of scope. May revisit in future versions.

### Tips for Categorization
- Be ruthless about P0s. The tighter the must-have list, the faster you ship and learn.
- If everything is P0, nothing is P0. Challenge every must-have: "Would we really not ship without this?"
- P1s should be things you are confident you will build soon, not a wish list.
- P2s are architectural insurance — they guide design decisions even though you are not building them now.

## Success Metrics Definition

### Leading Indicators
Metrics that change quickly after launch (days to weeks):
- **Adoption rate**: % of eligible users who try the feature
- **Activation rate**: % of users who complete the core action
- **Task completion rate**: % of users who successfully accomplish their goal
- **Time to complete**: How long the core workflow takes
- **Error rate**: How often users encounter errors or dead ends
- **Feature usage frequency**: How often users return to use the feature

### Lagging Indicators
Metrics that take time to develop (weeks to months):
- **Retention impact**: Does this feature improve user retention?
- **Revenue impact**: Does this drive upgrades, expansion, or new revenue?
- **NPS / satisfaction change**: Does this improve how users feel about the product?
- **Support ticket reduction**: Does this reduce support load?
- **Competitive win rate**: Does this help win more deals?

### Setting Targets
- Targets should be specific: "50% adoption within 30 days" not "high adoption"
- Base targets on comparable features, industry benchmarks, or explicit hypotheses
- Set a "success" threshold and a "stretch" target
- Define the measurement method: what tool, what query, what time window
- Specify when you will evaluate: 1 week, 1 month, 1 quarter post-launch

## Acceptance Criteria

Write acceptance criteria in Given/When/Then format or as a checklist:

**Given/When/Then**:
- Given [precondition or context]
- When [action the user takes]
- Then [expected outcome]

Example:
- Given the admin has configured SSO for their organization
- When a team member visits the login page
- Then they are automatically redirected to the organization's SSO provider

**Checklist format**:
- [ ] Admin can enter SSO provider URL in organization settings
- [ ] Team members see "Log in with SSO" button on login page
- [ ] SSO login creates a new account if one does not exist
- [ ] SSO login links to existing account if email matches
- [ ] Failed SSO attempts show a clear error message

### Tips for Acceptance Criteria
- Cover the happy path, error cases, and edge cases
- Be specific about the expected behavior, not the implementation
- Include what should NOT happen (negative test cases)
- Each criterion should be independently testable
- Avoid ambiguous words: "fast", "user-friendly", "intuitive" — define what these mean concretely

## Scope Management

### Recognizing Scope Creep
Scope creep happens when:
- Requirements keep getting added after the spec is approved
- "Small" additions accumulate into a significantly larger project
- The team is building features no user asked for ("while we're at it...")
- The launch date keeps moving without explicit re-scoping
- Stakeholders add requirements without removing anything

### Preventing Scope Creep
- Write explicit non-goals in every spec
- Require that any scope addition comes with a scope removal or timeline extension
- Separate "v1" from "v2" clearly in the spec
- Review the spec against the original problem statement — does everything serve it?
- Time-box investigations: "If we cannot figure out X in 2 days, we cut it"
- Create a "parking lot" for good ideas that are not in scope

## FashionUnited Feature Spec Templates

When writing specs for FashionUnited products, consider these product-specific templates and considerations:

### News Platform Features

**User personas**:
- Fashion publisher/editor (content creation, editorial workflow)
- Fashion professional (content consumption, industry insights)
- Subscriber (premium content access, personalized experience)

**Key considerations**:
- Content delivery and SEO impact
- Subscription/paywall integration
- Multi-language and regional support (NL, DE, UK, US, FR, ES, IT, MX, etc.)
- Editorial workflow and CMS integration
- Real-time content performance metrics

**Example user stories**:
- "As a fashion publisher, I want to schedule articles for publication so that I can maintain consistent publishing cadence across time zones"
- "As a subscriber, I want personalized content recommendations so that I discover relevant fashion industry news"
- "As a fashion professional, I want to save articles for later so that I can build a reference library of industry insights"

### Job Board Features

**User personas**:
- Recruiter (job posting, candidate management, employer branding)
- Job seeker (job search, application, career content)
- HR manager (bulk posting, ATS integration, analytics)

**Key considerations**:
- ATS/XML feed integration (Workday, Greenhouse, Lever, etc.)
- Job scraper reliability and data quality
- Application funnel optimization
- Employer branding page management
- Job matching and recommendation algorithms
- Credit system and pricing model impact

**Example user stories**:
- "As a recruiter, I want to sync jobs from our ATS so that I do not manually duplicate job postings"
- "As a job seeker, I want to filter jobs by location, salary, and experience level so that I find relevant opportunities quickly"
- "As an HR manager, I want to see application analytics so that I can optimize our job descriptions and posting strategy"

### B2B Marketplace Features

**User personas**:
- Fashion brand (wholesale listings, buyer connections, product showcase)
- Retailer/buyer (supplier discovery, product sourcing, trend research)
- Trade professional (network building, market intelligence)

**Key considerations**:
- Product catalog management and media assets
- Buyer-seller communication and lead generation
- Trade show and showroom integration
- Minimum order quantities and pricing visibility
- Geographic and seasonal relevance

**Example user stories**:
- "As a fashion brand, I want to showcase my wholesale collection so that retailers can discover and connect with us"
- "As a retailer, I want to search suppliers by product category and price range so that I can find brands that fit my store concept"
- "As a brand, I want to see which retailers viewed my profile so that I can follow up with interested buyers"

### Company Directory Features

**User personas**:
- Fashion brand (company profile, visibility, recruitment)
- Retailer (supplier research, industry connections)
- Job seeker (employer research, company discovery)

**Key considerations**:
- Profile completeness and verification
- Integration with job board and marketplace
- Company news and social feed integration
- Store locator functionality
- Brand-retailer relationship mapping

**Example user stories**:
- "As a fashion brand, I want to maintain our company profile so that potential employees and partners can learn about us"
- "As a job seeker, I want to see company reviews and culture information so that I can evaluate potential employers"
- "As a retailer, I want to find brands by location and product category so that I can source from local suppliers"

### Technical Considerations for FashionUnited

When writing specs, account for the technical architecture:

**GraphQL API patterns**:
- Design queries and mutations that align with existing schema patterns
- Consider pagination and filtering requirements for list views
- Plan for real-time updates where applicable (LiveView/Phoenix Channels)

**Elixir/Phoenix considerations**:
- Leverage Phoenix contexts for domain boundaries
- Consider background job processing (Oban) for async operations
- Plan for multi-tenant data isolation where applicable

**Data and search**:
- Elasticsearch indexing requirements for new searchable content
- PostgreSQL schema design and migration planning
- CDN caching strategy for static assets

**Integration points**:
- External API dependencies (ATS systems, payment providers, etc.)
- Webhook requirements for real-time integrations
- OAuth/authentication requirements for third-party connections

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fuww) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
