---
name: ada-conversation-simulation
description: Test and validate Ada AI agent responses before making changes live. Use when the user wants to simulate conversations, test how the agent responds, validate configuration changes, or preview behavior for specific scenarios. Use when this capability is needed.
metadata:
  author: adasupport
---

# Testing Ada Agent Responses

## When to use this skill

Use this skill when the user wants to:
- Test how the agent responds to specific messages
- Validate configuration changes before going live
- Preview behavior for particular scenarios
- Debug unexpected agent responses
- Compare expected vs actual behavior

## Prerequisites

- Access to the conversation simulation beta
- If `simulate_conversation` tool is not available, contact your Customer Solutions Consultant

## Simulation workflow

### Step 1: Discover available channels

First, identify which channels can be simulated:

```
Use list_channels to see available communication channels (Chat, Email, Voice, etc.)
```

Note the channel IDs for use in simulation.

### Step 2: Understand current configuration

Before testing, review what the agent is working with:

```
Use get_ada_configuration to see:
- Playbooks that might be triggered
- Coaching rules that apply
- Available actions
- Custom instructions
```

### Step 3: Run simulation

Send test messages to see agent responses:

```
Use simulate_conversation with:
- channel_id: From list_channels
- message: The customer message to test
```

The simulation:
- Does NOT affect real conversations
- Does NOT impact analytics
- Returns the agent's actual response

### Step 4: Analyze the response

Evaluate the agent's response for:
- **Accuracy**: Is the information correct?
- **Completeness**: Did it answer the full question?
- **Tone**: Is the response appropriately empathetic?
- **Actions**: Did it use the right actions/playbooks?
- **Handoff**: Did it correctly decide to resolve or hand off?

### Step 5: Iterate if needed

If the response isn't as expected:

```
1. Use search_knowledge to check if relevant content exists
2. Use search_coaching to see applicable coaching rules
3. Review get_ada_configuration for playbook/action issues
4. Identify what needs to change
5. Re-simulate after changes are made
```

## Common simulation scenarios

### Testing knowledge coverage

```
"What is your return policy?"
"How do I reset my password?"
"What are your business hours?"
```

Verify the agent retrieves and presents correct information.

### Testing playbook triggers

```
"I want to cancel my subscription"
"I need to speak to a manager"
"Can you transfer me to a human?"
```

Verify the right playbook activates.

### Testing action execution

```
"What's the status of my order #12345?"
"Can you update my email address?"
"Please cancel my appointment"
```

Verify actions are attempted correctly.

### Testing edge cases

```
"I want a refund but I don't have my receipt and it's been 45 days"
"Your product broke and I'm very upset"
"This is the third time I'm asking about this"
```

Verify graceful handling of complex scenarios.

### Testing handoff behavior

```
"Let me talk to a real person"
"This chatbot is useless"
"I have a legal question about my contract"
```

Verify appropriate handoff triggers.

## Output format

```markdown
## Simulation Result

**Channel**: Chat (channel_id: abc123)
**Test Message**: "I want to cancel my subscription"

### Agent Response
[Full agent response text]

### Analysis
- ✅ Correct playbook triggered (subscription_cancellation)
- ✅ Appropriate empathy shown
- ⚠️ Didn't ask for account verification
- ❌ Missing mention of cancellation fee

### Recommendations
1. Add coaching: "Always verify account before processing cancellations"
2. Update playbook to mention applicable fees
```

## Batch testing

For comprehensive validation, test multiple scenarios:

```markdown
## Test Suite: Return Policy Updates

| Scenario | Message | Expected | Result |
|----------|---------|----------|--------|
| Simple return | "How do I return this?" | Return instructions | ✅ Pass |
| Time limit | "Can I return after 30 days?" | Policy explanation | ✅ Pass |
| Exchange | "Can I exchange for different size?" | Exchange process | ⚠️ Incomplete |
| No receipt | "I lost my receipt" | Alternative options | ❌ Fail |

### Failed Scenarios to Address
1. Exchange flow needs more detail in knowledge article
2. No-receipt scenario not covered - add coaching rule
```

## Tips for effective testing

- Test both happy paths and edge cases
- Simulate actual customer language (informal, frustrated, etc.)
- Test across different channels if behavior should vary
- Document test cases for regression testing after changes
- Compare simulated responses to real conversation outcomes
- Use simulation to validate fixes before deploying

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adasupport) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
