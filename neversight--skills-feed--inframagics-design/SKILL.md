---
name: inframagics-design
description: Product design guide for Inframagics - the agent-native workspace that replaces enterprise systems + operations teams. Use when designing, reviewing, or improving Inframagics interfaces, features, or architecture. Use when this capability is needed.
metadata:
  author: neversight
---

# Inframagics Design Guide

**Product**: Inframagics — Agent-native workspace for the next unicorns

**Demo**: https://inframagics.com

**Trigger**: When designing, reviewing, or improving Inframagics features, interfaces, or architecture.

**Key Reference**: `Product/inframagics-roles-capabilities.md` — Roles, capabilities, MVP scope, and full design philosophy

---

## Design Philosophy: Claude Code for Non-Engineers

### Core Analogy

Inframagics mirrors the Claude Code experience for non-technical users:

| Claude Code | Inframagics |
|-------------|-------------|
| Terminal/CLI interface | Chat interface (Genspark-style) |
| User mode: Write code, commit | User mode: Submit requests, execute tasks |
| Admin mode: Create rules/MCP/skills | Admin mode: Create policies, SOPs, workflows |
| System evolves as you use it | System learns and improves over time |

**Key difference**: Inframagics is for people extremely uncomfortable with terminal/CLI.

### Unified Interface, Role-Based AI

**Same UI for all roles** — Only the AI behavior changes:

| Role | AI Focus |
|------|----------|
| **Admin** | Policies, SOPs, settings, system configuration |
| **User** | Clarify request → Reason → Retrieve policies → Execute |
| **Manager** | Team oversight, approvals, delegation |

### Self-Improving System

```
User friction → Admin creates policy → System improves → Future users benefit
```

This virtuous cycle is why Inframagics replaces the ops team, not just the tools.

---

## Role Framework (Summary)

| Role | Scale | Priority | Description |
|------|-------|----------|-------------|
| **Admin** | 10+ | P0/MVP | System owner, policy creator, approver |
| **User** | 10+ | P0/MVP | Request submitter, information seeker |
| **Manager** | 30+ | P1 | Team approver, delegator |
| **Policy Owner** | 100+ | P2 | Domain specialist |
| **External** | 300+ | P3 | Vendors, auditors |

**MVP Focus**: Admin + User, Finance (Expenses) only

---

## 1. The Paradigm Shift

### Traditional Enterprise Model
```
Policy Makers → SOPs → Operational Staff → ERP/CRM → End Users
     ↓              ↓           ↓              ↓
  Executives    Documents    Humans      Software
  define       interpret    execute      records
```

**Problems**:
- SI fees: 6-7 figures for setup
- Policy changes: Weeks + SI involvement
- Ops team: 10-50 people interpreting rules
- Knowledge: Scattered across systems/heads

### Inframagics Model
```
Policy Makers → Policies (in system) → AI Agent → End Users
     ↓                   ↓                 ↓
  Executives         Natural           Executes
  define in         language,          policies,
  natural           instant            handles
  language          changes            requests
```

**Value**:
- Setup: Self-service, 4-5 figures max
- Policy changes: Instant, no SI needed
- Ops team: Eliminated or minimal
- Knowledge: Organized, retrievable, contextual

---

## 2. Target Audience: Baby-Corns

**Definition**: Next unicorns — fast-growing startups that will need enterprise systems but don't want enterprise baggage.

### Baby-Corn Characteristics

| Trait | Implication for Design |
|-------|------------------------|
| **Growing fast** | System must scale without reimplementation |
| **Lean teams** | No dedicated ops staff; everyone wears hats |
| **Tech-savvy founders** | Expect modern UX, no tolerance for legacy |
| **Cash-conscious** | Won't pay 6-figure SI fees |
| **Agile culture** | Need instant policy changes, not IT tickets |
| **AI-native thinking** | Expect AI to do the work, not assist |

### NOT Our Target

- **Legacy enterprises**: Have sunk costs in SAP/Oracle, change-averse
- **SMBs with no growth**: Don't need policies, just basic tools
- **Industries with rigid compliance**: Healthcare, banking (initially)

### Design Implication

Every feature should pass the "baby-corn test":
> "Would a 50-person startup with no ops team be able to use this without training or consultants?"

---

## 3. Three Core Capabilities

### Capability 1: Policies & SOPs

**What it replaces**: Policy manuals, training documents, approval workflows configured by SIs

**How it works**:
```
Policy Maker says: "Purchases over $5000 need VP approval"
                          ↓
        System interprets + confirms understanding
                          ↓
        Policy active immediately, enforced by agent
                          ↓
        End user makes request → Agent applies policy
```

**Key Design Principles**:

1. **Natural language in, structured execution out**
   - Input: "New vendors need finance approval"
   - System: Parses, confirms, stores structured rule
   - Never ask policy maker to fill forms

2. **Confirmation before activation**
   - Show: "I understood this as: [structured interpretation]"
   - Allow: Edit, adjust, test before going live
   - Never: Silently activate possibly wrong interpretation

3. **Instant changes, zero downtime**
   - Policy maker edits policy → Live immediately
   - No IT ticket, no deployment, no SI call

4. **Conflict detection**
   - When new policy conflicts with existing, surface it
   - "This overlaps with [other policy]. How should I prioritize?"

5. **Execution visibility**
   - Every policy application logged
   - Policy maker can see: "This policy triggered 47 times this week"

**Interface Pattern**:
```
┌─────────────────────────────────────────────────────────┐
│  Define a policy...                              🎤     │
│  ─────────────────────────────────────────────────────  │
│  "Expenses over $500 require manager approval"          │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  I'll create this policy:                               │
│                                                         │
│  Type: APPROVAL                                         │
│  Trigger: expense.amount > 500                          │
│  Action: Require approval from reporting_manager        │
│                                                         │
│  [Edit]  [Test with example]  [Activate]               │
└─────────────────────────────────────────────────────────┘
```

---

### Capability 2: Request Handling

**What it replaces**: Ops team that receives requests, interprets policies, executes tasks

**How it works**:
```
End user says: "I need to expense this $800 dinner with client"
                          ↓
        Agent checks policies: "Expenses > $500 need approval"
                          ↓
        Agent: "I'll submit this for manager approval.
                Adding it to Sarah's queue. You'll be notified."
                          ↓
        Sarah approves → Agent completes expense submission
```

**Key Design Principles**:

1. **Intent-based, not form-based**
   - User states what they want
   - Agent figures out how to do it
   - Never: "Please fill out form XYZ"

2. **Policy application is transparent**
   - "This needs approval because [policy X]"
   - User understands why, not just blocked

3. **Agent handles mechanics, human handles judgment**
   - Agent: Data entry, routing, notifications
   - Human: Approval decisions, exceptions

4. **Proactive, not reactive**
   - Agent: "Your expense report has 3 items waiting. Should I submit?"
   - Not: User must remember to check status

5. **Graceful escalation**
   - If agent can't handle, escalate to human clearly
   - "I'm not sure how to handle [X]. Let me connect you with [person]."

**Interface Pattern**:
```
┌─────────────────────────────────────────────────────────┐
│  What do you need?                               🎤     │
│  ─────────────────────────────────────────────────────  │
│  "Bill Acme Corp $15,000 for the consulting project"    │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  📋 Creating invoice for Acme Corp                      │
│                                                         │
│  Amount: $15,000                                        │
│  Description: Consulting project                        │
│  Due: Net 30 (per your policy for this client)         │
│                                                         │
│  ⚠️ This exceeds $10,000 - requires Finance approval    │
│     I'll route to CFO after you confirm.               │
│                                                         │
│  [Confirm & Route]  [Edit details]  [Cancel]           │
└─────────────────────────────────────────────────────────┘
```

---

### Capability 3: Knowledge Organization & Retrieval

**What it replaces**: Confluence, SharePoint, Notion sprawl; tribal knowledge in people's heads

**How it works**:
```
User asks: "What's our policy on remote work?"
                          ↓
        Agent searches knowledge base
                          ↓
        Agent: "Remote work policy (updated March 2024):
                - 3 days/week in office minimum
                - Core hours 10am-3pm
                - Equipment stipend $500/year
                Want me to show the full policy or help with something specific?"
```

**Key Design Principles**:

1. **Conversational retrieval, not search results**
   - Don't show: 47 matching documents
   - Show: The answer, with source citation

2. **Knowledge capture from policies**
   - Policies automatically become queryable knowledge
   - "What's the approval limit for travel?" → Finds from policy

3. **Context-aware answers**
   - Agent knows who's asking
   - Different answer for manager vs. new hire

4. **Living documentation**
   - When policy changes, knowledge updates automatically
   - No stale documentation problem

5. **Capture tribal knowledge**
   - "How do we usually handle X?" can be saved as knowledge
   - Turn one-off answers into reusable knowledge

**Interface Pattern**:
```
┌─────────────────────────────────────────────────────────┐
│  Ask anything...                                 🎤     │
│  ─────────────────────────────────────────────────────  │
│  "How do I get reimbursed for conference travel?"       │
└─────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────┐
│  For conference travel reimbursement:                   │
│                                                         │
│  1. Submit expense within 30 days of travel            │
│  2. Include receipts for anything over $25             │
│  3. Per diem is $75/day for meals (no receipts needed) │
│  4. Pre-approval required for international travel     │
│                                                         │
│  📎 Source: Travel Policy (updated Nov 2024)           │
│                                                         │
│  Should I start an expense report for you?             │
│  [Yes, start expense]  [Show full policy]              │
└─────────────────────────────────────────────────────────┘
```

---

## 4. Value Proposition Checklist

Every feature must deliver on at least one:

### ✅ Eliminates SI Dependency
- [ ] Can policy maker configure without consultant?
- [ ] Is setup self-service?
- [ ] Can changes be made without IT ticket?

### ✅ Shrinks Operations Team
- [ ] Does this replace a human task?
- [ ] Is the agent doing work, not just assisting?
- [ ] Would a 50-person company NOT need an ops hire for this?

### ✅ Instant Policy Changes
- [ ] Can policy be changed in natural language?
- [ ] Is the change live immediately?
- [ ] Is there no deployment/release cycle?

### ✅ Knowledge Always Available
- [ ] Is information retrievable conversationally?
- [ ] Is there a single source of truth?
- [ ] Does it eliminate "ask Bob, he knows"?

**Red flags** (features that don't fit):
- "Requires initial setup workshop" ❌
- "Configure in admin settings" ❌
- "Integrate with your existing..." ❌ (v1 should be complete)
- "Training required for advanced features" ❌

---

## 5. Design Principles

### Principle 1: AI Does, Human Confirms

```
Wrong: AI assists human doing work
Right: AI does work, human confirms/approves

Wrong: "Here's a draft invoice for you to review and edit"
Right: "I created the invoice. It's ready to send. [Send] [Edit first]"
```

### Principle 2: Natural Language Everything

```
Wrong: Forms with fields
Right: Conversation that extracts what's needed

Wrong: "Select department: [dropdown]"
Right: "Who's this for?" → Agent figures out department
```

### Principle 3: Policy Before Rejection

```
Wrong: User submits → Error: "Exceeds limit"
Right: User types intent → "This needs approval because [policy]" → User decides to proceed

User should never be surprised by a policy.
```

### Principle 4: Zero Training for Basic Tasks

```
Wrong: "See documentation for how to submit expenses"
Right: "I need to expense lunch" → Agent handles everything

If a new employee can't accomplish basic tasks on day 1 without training, we've failed.
```

### Principle 5: Instant Feedback Loop

```
Wrong: Policy maker defines → Wait days → See if it works
Right: Policy maker defines → Test immediately → See execution in real-time
```

### Principle 6: Context is King

```
Wrong: Same interface for everyone
Right: Agent knows who you are, what you usually do, what's relevant

"Create invoice" for an AP clerk vs. CFO should feel different.
```

---

## 6. Interface Patterns

> **Full specification**: See `Product/inframagics-roles-capabilities.md`

### Unified Chat Interface (All Roles)

**Same UI structure for everyone** — inspired by Genspark:

```
┌──────┬────────────────────────────────────────────────┐
│  +   │                                                │
│ New  │          Inframagics                           │
├──────┤                                                │
│  🏠  │   ┌────────────────────────────────────────┐   │
│ Home │   │ Ask anything, request anything          │   │
├──────┤   │                        [📎] [🎤] [→]  │   │
│  💰  │   └────────────────────────────────────────┘   │
│ Fin  │                                                │
├──────┤   Quick Actions / Recent items                 │
│  👥  │                                                │
│ HR   │                                                │
├──────┤                                                │
│  📦  │                                                │
│ Proc │                                                │
├──────┤                                                │
│  💻  │                                                │
│ IT   │                                                │
├──────┤                                                │
│  ⚙️  │                                                │
│ Set  │                                                │
└──────┴────────────────────────────────────────────────┘
```

**Left sidebar** = Business functions directory (collapsible icons)
**Main area** = Universal chat input + context-aware actions

### Role-Based AI Behavior

**Admin AI** focuses on system configuration:
```
Admin: "Set expense limit to $500 for meals"
AI: ✓ Creating policy: Meal expenses capped at $500
    ✓ Applies to: All employees
    → Policy created. Notify employees?
```

**User AI** focuses on request execution:
```
User: "I had dinner with a client at Nobu, $450"
AI: ✓ Checking policy: Entertainment Expense Policy
    ✓ Amount $450 exceeds $200 → Manager approval required
    → Upload receipt? [📎 Attach]
```

**Manager AI** focuses on team oversight:
```
Manager: "What's pending?"
AI: ✓ 3 requests awaiting approval:
    1. Sarah - $450 client dinner (over policy)
    2. Mike - $89 supplies (auto-approvable)
    → Approve Mike's? [✓ Approve]
```

### Key UX Requirements

- **Single universal input** (text + voice + attachments)
- **Sidebar scopes context** (clicking Finance focuses AI on Finance)
- **Transparent reasoning** (show policy lookups, routing decisions)
- **Action buttons** in responses (not forms)
- **No separate admin/user URLs** — role determines AI behavior

### Anti-patterns to Avoid

- Form-based anything
- Module-based navigation (AP, AR, HR tabs)
- Separate admin/user interfaces
- Configuration tables and matrices
- Visual workflow builders
- Search results instead of answers

---

## 7. Anti-Patterns (Inframagics-Specific)

### "Requires Consultant to Configure"
**Symptom**: Feature needs expert setup before use.
**Fix**: Natural language configuration with smart defaults.

### "Ops Team Still Needed"
**Symptom**: Agent assists but human still does the work.
**Fix**: Agent does the work, human only approves/confirms.

### "Knowledge Scattered"
**Symptom**: Information in docs, chat, email, people's heads.
**Fix**: All knowledge in Inframagics, conversationally retrievable.

### "IT Ticket for Changes"
**Symptom**: Policy changes need admin/IT involvement.
**Fix**: Policy maker speaks change, it's live immediately.

### "Training Required"
**Symptom**: Users need onboarding to use basic features.
**Fix**: Intent-based interface that anyone can use immediately.

### "Enterprise UX Creep"
**Symptom**: Adding features that make sense for SAP but not baby-corns.
**Fix**: Apply baby-corn test: Would 50-person startup use this?

### "Configuration Over Convention"
**Symptom**: Lots of settings, options, customization.
**Fix**: Smart defaults that work for 90% of cases.

---

## 8. Competitive Positioning

### vs. Traditional ERP (SAP, Oracle, NetSuite)

| Dimension | Traditional ERP | Inframagics |
|-----------|-----------------|-------------|
| Setup | 6-12 months, 6-7 figures | Self-service, days |
| Policy changes | IT project, weeks | Natural language, instant |
| User training | Weeks of formal training | Zero training needed |
| Ops team | Required (10-50 people) | Eliminated |
| Interface | Transaction codes, forms | Conversational |

**Inframagics advantage**: For baby-corns, we're the only option that doesn't require becoming an "enterprise" to use enterprise software.

### vs. Modern SaaS (Monday, Notion, Asana)

| Dimension | Modern SaaS | Inframagics |
|-----------|-------------|-------------|
| Policy enforcement | Manual/honor system | Automatic, built-in |
| Approval workflows | Basic or bolt-on | Native, natural language |
| Knowledge retrieval | Search results | Conversational answers |
| Work execution | Human does it | Agent does it |

**Inframagics advantage**: SaaS tools organize work but don't do work. Inframagics agent actually executes.

### vs. AI Assistants (Copilots, ChatGPT)

| Dimension | AI Assistants | Inframagics |
|-----------|---------------|-------------|
| Policy awareness | None (generic) | Built-in, enforced |
| System of record | External | Native |
| Action capability | Suggests | Executes |
| Knowledge scope | General | Company-specific |

**Inframagics advantage**: Generic copilots don't know your policies or have permission to act. Inframagics is the system AND the agent.

---

## 9. Review Checklist

When reviewing Inframagics designs:

### Value Delivery
- [ ] Does this eliminate SI dependency?
- [ ] Does this shrink the ops team need?
- [ ] Can policies be changed instantly?
- [ ] Is knowledge retrievable conversationally?

### Baby-Corn Fit
- [ ] Would a 50-person startup use this?
- [ ] Is it usable without training?
- [ ] Is setup self-service?
- [ ] Does it avoid "enterprise bloat"?

### AI-Native Design
- [ ] Is AI doing the work (not just assisting)?
- [ ] Is input natural language (not forms)?
- [ ] Are policies shown before rejection?
- [ ] Is context used to personalize?

### Interface Quality
- [ ] Is there a single universal input?
- [ ] Are action options clear?
- [ ] Is status always visible?
- [ ] Can users accomplish goals in <30 seconds?

---

## 10. Feature Prioritization Framework

When deciding what to build:

### Must Have (P0)
- Directly eliminates ops team need
- Enables instant policy changes
- Core to "agent does work" promise

### Should Have (P1)
- Enhances core capabilities
- Improves baby-corn experience
- Requested by multiple prospects

### Nice to Have (P2)
- Edge cases
- Power user features
- Future enterprise needs

### Won't Build (v1)
- Requires SI to implement
- Only relevant for large enterprises
- Adds complexity without clear value
- "Because SAP has it"

---

## 11. Key Metrics

### Agent Effectiveness
- **Tasks completed by agent**: Target 80%+
- **Tasks requiring human intervention**: Target <20%
- **Policy violations caught pre-submission**: Target 95%+

### User Adoption
- **Time to first task completion**: Target <5 min
- **Tasks per user per week**: Growing week-over-week
- **Users needing support**: Target <10%

### Value Delivery
- **Ops team size**: Should be smaller than comparable companies
- **Policy change time**: Target <1 hour
- **Setup time**: Target <1 day for basic use

---

## 12. The Inframagics Promise

**To baby-corn founders**:

> "You'll never need a 50-person ops team. You'll never pay 7-figure SI fees. You'll never wait weeks for policy changes. Your AI agent handles operations. You focus on growth."

Every design decision should reinforce this promise.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
