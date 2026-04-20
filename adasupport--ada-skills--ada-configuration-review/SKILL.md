---
name: ada-configuration-review
description: Review and understand Ada AI agent configuration including playbooks, coaching, actions, and custom instructions. Use when the user asks about their current setup, wants to audit their configuration, or asks questions like "what playbooks do we have" or "show me our custom instructions". Use when this capability is needed.
metadata:
  author: adasupport
---

# Reviewing Ada Configuration

## When to use this skill

Use this skill when the user wants to:
- Understand their current AI agent setup
- Audit existing configuration
- List playbooks, actions, or coaching rules
- Review custom instructions
- Find specific configuration items
- Prepare for making changes

## Configuration components

Ada's AI agent configuration includes:

| Component | Purpose |
|-----------|---------|
| **Playbooks** | Structured workflows for specific scenarios |
| **Actions** | Integrations that let the agent perform tasks |
| **Coaching** | Behavioral guidance and rules |
| **Custom Instructions** | Global guidance for agent behavior |
| **Company Description** | Context about the business |
| **Knowledge** | Articles the agent uses to answer questions |
| **Channels** | Communication channels (Chat, Email, Voice) |

## Review workflow

### Full configuration overview

```
Use get_ada_configuration to retrieve all configuration entities
```

This returns:
- All playbooks with descriptions and content
- All actions with their capabilities
- All coaching rules
- Custom instructions/guidance
- Company description

### Knowledge search

```
Use search_knowledge with relevant terms to find specific articles
```

Knowledge is searched separately due to volume.

### Coaching search

```
Use search_coaching with relevant terms to find specific coaching rules
```

### Channel list

```
Use list_channels to see all configured communication channels
```

## Output formats

### Configuration Summary

```markdown
## Ada Configuration Overview

### Playbooks (12 total)
| Name | Description | Triggers |
|------|-------------|----------|
| subscription_cancellation | Handles cancellation requests | "cancel", "unsubscribe" |
| order_status | Checks order status | "where is my order", "tracking" |
| password_reset | Self-service password reset | "forgot password", "can't login" |
| ... | ... | ... |

### Actions (8 total)
| Name | Capability |
|------|------------|
| check_order_status | Retrieves order info from OMS |
| update_email | Updates customer email address |
| create_ticket | Creates support ticket in Zendesk |
| ... | ... |

### Coaching Rules (15 total)
- Always greet customers warmly
- Mention processing time for refunds (5-7 days)
- Offer callback option for complex issues
- ...

### Custom Instructions
[Summary of global guidance]

### Channels (3 configured)
- Chat (widget on website)
- Email (support@example.com)
- Voice (1-800-EXAMPLE)
```

### Detailed Playbook Review

```markdown
## Playbook: subscription_cancellation

**Description**: Handles customer requests to cancel subscriptions

**Trigger Phrases**:
- "cancel my subscription"
- "unsubscribe"
- "stop billing"

**Steps**:
1. Express empathy for departure
2. Verify account ownership
3. Check for applicable retention offers
4. Process cancellation if confirmed
5. Confirm cancellation and next steps

**Actions Used**:
- verify_customer
- get_subscription_status
- apply_retention_offer
- cancel_subscription

**Coaching Applied**:
- Mention any cancellation fees upfront
- Offer pause instead of cancel when applicable
```

### Gap Analysis

```markdown
## Configuration Gap Analysis

### Well-Covered Areas
- ✅ Order management (3 playbooks, 5 actions)
- ✅ Account management (2 playbooks, 3 actions)
- ✅ Returns and refunds (2 playbooks, 2 actions)

### Potential Gaps
- ⚠️ No playbook for billing disputes
- ⚠️ Limited coaching on handling frustrated customers
- ⚠️ Technical support topics lack dedicated actions

### Recommendations
1. Create billing dispute playbook
2. Add coaching for de-escalation techniques
3. Consider API integration for technical diagnostics
```

## Common questions this skill answers

| Question | Approach |
|----------|----------|
| "What playbooks do we have?" | List all playbooks from get_ada_configuration |
| "Show me our custom instructions" | Extract guidance section from configuration |
| "Do we have an action for X?" | Search actions in configuration |
| "What coaching rules exist for Y?" | Use search_coaching |
| "Summarize our current setup" | Full configuration overview |
| "Are there any gaps in our config?" | Gap analysis format |

## Tips

- Start with full configuration to understand the landscape
- Use search functions for specific topics
- Cross-reference configuration with performance data when optimizing
- Document configuration state before making changes
- Look for overlapping or conflicting rules

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adasupport) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
