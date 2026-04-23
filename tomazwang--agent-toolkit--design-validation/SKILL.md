---
name: design-validation
description: Validate design documents for completeness, clarity, and feasibility Use when this capability is needed.
metadata:
  author: tomazwang
---

# Design Validation Skill

Validate design documents to ensure they're complete, clear, and ready for implementation.

## When to Use

This skill should activate when:
- User has created a design document and wants validation
- User says "review this design" or "does this look complete?"
- Before transitioning from design to implementation
- User explicitly invokes: "Validate this design"

**Do NOT activate for:**
- Code review (use code-review plugin instead)
- Implementation debugging
- General questions about designs

## Validation Framework

Check design documents against these criteria:

### 1. Problem/Goal Clarity

**Questions:**
- ✅ Is the problem or opportunity clearly stated?
- ✅ Is the goal specific and measurable?
- ✅ Are success criteria defined?
- ✅ Are non-goals explicitly stated (what we're NOT doing)?

**Red flags:**
- Vague problem statements: "Make things better"
- No measurable success: "Users will like it more"
- Scope creep: No clear boundaries on what's included

**Feedback format:**
```
❌ Issue: Goal is vague
Current: "Improve the onboarding experience"
Better: "Reduce time-to-first-action from 15min to 5min for new users"
```

### 2. Alternative Exploration

**Questions:**
- ✅ Were multiple approaches considered (not just one)?
- ✅ Are approaches truly different (not just variations)?
- ✅ Are pros AND cons listed honestly for each?
- ✅ Are tradeoffs explicit and understood?

**Red flags:**
- Only one approach documented (usually means not enough exploration)
- Pros/cons list is one-sided (pushing toward preferred option)
- Alternatives that are clearly strawmen
- No discussion of tradeoffs

**Feedback format:**
```
⚠️ Concern: Limited alternatives explored
Found: Only one approach documented
Suggestion: Consider at least 2-3 different approaches before deciding
For example: [suggest alternative angles to explore]
```

### 3. Decision Rationale

**Questions:**
- ✅ Is the chosen approach clearly stated?
- ✅ Is there a clear rationale for why it was chosen?
- ✅ Are tradeoffs acknowledged?
- ✅ Does the decision address the stated goal?

**Red flags:**
- "We chose X because it's better" (vague)
- No mention of what was traded away
- Decision seems disconnected from goal
- No consideration of risks

**Feedback format:**
```
✅ Strong rationale
The decision to use Approach B (chunked uploads) is well-justified:
- Directly addresses the large file problem
- Tradeoffs (complexity) are acknowledged
- Mitigation (use queue library) is specified
```

### 4. Feasibility & Risks

**Questions:**
- ✅ Are technical/practical constraints acknowledged?
- ✅ Are risks identified?
- ✅ Are mitigations proposed for key risks?
- ✅ Is the scope realistic?

**Red flags:**
- No mention of technical constraints
- Overly optimistic (no risks identified)
- No mitigation strategies
- Scope seems unrealistic

**Feedback format:**
```
⚠️ Risk not addressed: Scalability
The design doesn't discuss how this will scale beyond 1000 concurrent users
Suggestion: Add section on scaling strategy or acknowledge as future work
```

### 5. Implementation Clarity

**Questions:**
- ✅ Are next steps actionable and specific?
- ✅ Is there enough detail to start implementing?
- ✅ Are dependencies and prerequisites clear?
- ✅ Is the implementation sequencing logical?

**Red flags:**
- Next steps too vague: "Implement the solution"
- Missing key details needed to build
- No clear starting point
- Dependencies not identified

**Feedback format:**
```
❌ Issue: Next steps too vague
Current: "1. Build the feature 2. Test it 3. Deploy"
Better:
1. Create upload endpoint with chunking support
2. Implement chunk storage and assembly
3. Add resumable upload tracking
4. Write integration tests for multi-chunk uploads
5. Deploy to staging for validation
```

### 6. Completeness

**Questions:**
- ✅ Does it cover all key aspects for this domain?
- ✅ Are edge cases considered?
- ✅ Is error handling mentioned?
- ✅ Are success metrics defined?

**Domain-specific considerations:**

**Software:**
- Architecture/tech stack
- Data models
- Error handling
- Performance expectations
- Testing strategy
- Deployment approach

**Product:**
- User experience
- Feature scope
- Success metrics
- Launch plan
- Feedback loops

**Business:**
- Market approach
- Financial model
- Resource needs
- Timeline
- Success metrics
- Risks & contingencies

**Content/Creative:**
- Target audience
- Key message
- Format/medium
- Distribution
- Success metrics

## Validation Process

### Step 1: Read Document Completely

- Don't start validating until you've read the whole thing
- Understand the domain and context
- Note the format and level of detail

### Step 2: Apply Validation Framework

Go through each criterion:
1. Problem/Goal Clarity
2. Alternative Exploration
3. Decision Rationale
4. Feasibility & Risks
5. Implementation Clarity
6. Completeness

### Step 3: Generate Feedback

**Structure your feedback:**

```markdown
# Design Validation: [Document Title]

## Summary
[Overall assessment: Strong/Good/Needs Work]

## Strengths
- What's done well
- What's particularly clear or thorough

## Issues Found

### Critical (Must Address)
- ❌ Issue 1: [Description]
  - Why it matters: [Impact]
  - Suggestion: [How to fix]

### Important (Should Address)
- ⚠️ Concern 1: [Description]
  - Suggestion: [How to improve]

### Nice to Have (Optional)
- 💡 Enhancement 1: [Description]
  - Benefit: [Why this would help]

## Validation Checklist
- [✅/❌] Problem clearly defined
- [✅/❌] Multiple alternatives explored
- [✅/❌] Decision rationale clear
- [✅/❌] Risks identified
- [✅/❌] Next steps actionable
- [✅/❌] Success metrics defined

## Recommendation
[Ready to implement / Needs revision / Major gaps]

## Next Steps
[What to do with this feedback]
```

### Step 4: Be Constructive

**Do:**
- Point out strengths, not just problems
- Explain WHY something matters
- Provide specific suggestions for improvement
- Acknowledge good thinking
- Differentiate critical from nice-to-have

**Don't:**
- Just criticize without suggestions
- Rewrite their entire design
- Impose your preferred approach
- Nitpick formatting or style
- Demand perfection

## Example Validations

### Example 1: Good Design (Software)

```markdown
# Design Validation: Notification System

## Summary
**Strong design** - Well thought through with clear rationale

## Strengths
- Three distinct approaches explored (push, digest, adaptive)
- Honest tradeoffs documented for each
- Clear decision rationale: "Start with digest for MVP, add push later"
- Phased approach acknowledges complexity constraints
- Success metrics defined (open rate, engagement time)

## Issues Found

### Important (Should Address)
- ⚠️ Concern: Error handling not discussed
  - What happens if digest generation fails?
  - Suggestion: Add section on retry logic and fallback strategies

### Nice to Have (Optional)
- 💡 Enhancement: Consider user preferences
  - Some users might want both digest AND push for urgent items
  - Benefit: Higher user satisfaction, lower unsubscribe rate

## Validation Checklist
- [✅] Problem clearly defined ("Users miss important updates")
- [✅] Multiple alternatives explored (3 approaches)
- [✅] Decision rationale clear
- [⚠️] Risks partially identified (scalability yes, errors no)
- [✅] Next steps actionable
- [✅] Success metrics defined

## Recommendation
**Ready to implement** with minor additions

## Next Steps
1. Add error handling section (15 min)
2. Consider user preference controls (optional, future phase)
3. Proceed with implementation plan
```

### Example 2: Needs Work (Product)

```markdown
# Design Validation: Coffee Shop Layout

## Summary
**Needs revision** - Good starting point but missing key details

## Strengths
- Problem clearly stated (maximize revenue per sqft)
- Three layout approaches considered
- Real-world examples provided

## Issues Found

### Critical (Must Address)
- ❌ Issue: No decision made
  - Doc explores three options but doesn't choose one
  - Why it matters: Can't implement without decision
  - Suggestion: Add "Decision" section with chosen approach and rationale

- ❌ Issue: Budget and constraints not mentioned
  - No discussion of renovation budget or timeline
  - Why it matters: Different approaches have very different costs
  - Suggestion: Add constraints section: budget, timeline, building limitations

### Important (Should Address)
- ⚠️ Concern: Target customer not defined
  - Layout should match customer type (students vs businesspeople vs tourists)
  - Suggestion: Define primary customer segment first

- ⚠️ Concern: Success metrics vague
  - "Maximize revenue" - how measured? Per sqft? Per hour? Total?
  - Suggestion: Define specific metric (e.g., "$X revenue per sqft per month")

## Validation Checklist
- [✅] Problem stated (maximize revenue)
- [✅] Multiple alternatives explored
- [❌] Decision rationale (no decision made yet)
- [❌] Risks identified (budget/constraints missing)
- [⚠️] Next steps (too high-level)
- [⚠️] Success metrics (vague)

## Recommendation
**Needs revision** before proceeding

## Next Steps
1. Add constraints section (budget, timeline, building limits)
2. Define target customer segment
3. Make clear decision with rationale
4. Specify measurable success metric
5. Add detailed next steps for chosen approach
6. Return for re-validation
```

## Domain-Specific Validation

### Software/Technical Designs

**Extra checks:**
- Technology choices justified?
- Data models/schema included?
- API contracts defined?
- Performance expectations stated?
- Testing strategy mentioned?
- Deployment plan outlined?

### Product Designs

**Extra checks:**
- User research or insights mentioned?
- User flows or wireframes referenced?
- Feature prioritization (MVP vs future)?
- Launch plan outlined?
- User feedback loops defined?

### Business Strategies

**Extra checks:**
- Market analysis included?
- Financial projections or model?
- Resource requirements (people, money, time)?
- Competitive landscape considered?
- Go-to-market strategy?

### Content/Creative

**Extra checks:**
- Target audience defined?
- Key message clear?
- Tone and style specified?
- Distribution channels identified?
- Production timeline?

## Integration with Other Plugins

**After validation:**
- If ready → Create plan: "Create implementation plan with /plan"
- If issues → Iterate: "Let's address the critical issues"
- If vague → Brainstorm more: "Let's brainstorm [missing aspect]"

## Remember

**You're not rewriting their design. You're validating it.**

Good validation:
- Identifies gaps clearly
- Explains why gaps matter
- Suggests specific improvements
- Acknowledges what's done well
- Gives clear next steps

Your job is to help them **strengthen their design** before implementation, not to impose your vision.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomazwang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
