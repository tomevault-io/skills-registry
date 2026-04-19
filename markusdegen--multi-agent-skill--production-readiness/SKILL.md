---
name: production-readiness
description: This skill should be used when the user asks about "MAS production", "agent observability", "MAS cost optimization", "agent monitoring", "multi-agent governance", "MAS compliance", "token budget", "ops runbook", "incident response", "MAS alerting", or is preparing a multi-agent system for production deployment. Covers cost, observability, governance, compliance, and operational procedures. Use when this capability is needed.
metadata:
  author: markusdegen
---

# Production Readiness for Multi-Agent Systems

## The Production Challenge

89% of organizations have implemented observability for agents, but 32% cite quality issues as the primary barrier to production. Production MAS requires first-class treatment of:

1. **Cost Optimization** - Multi-agent inefficiencies can increase costs 10x
2. **Observability** - Distributed tracing and failure classification
3. **Governance** - Dynamic RBAC and audit trails
4. **Compliance** - EU AI Act enforcement begins August 2, 2026

## Cost Optimization

### Token Budget Management

Multi-agent systems can cost 2-4x more than single agents. Systematic optimization achieves 30-50% reductions.

#### Per-Agent Token Budgets

```json
{
  "token_budgets": {
    "Planner": {
      "max_input_tokens": 4000,
      "max_output_tokens": 2000,
      "model": "claude-3-haiku"
    },
    "Executor": {
      "max_input_tokens": 8000,
      "max_output_tokens": 4000,
      "model": "claude-3-sonnet"
    },
    "Verifier": {
      "max_input_tokens": 6000,
      "max_output_tokens": 1000,
      "model": "claude-3-haiku"
    }
  }
}
```

#### Budget Enforcement

```python
def check_budget_before_call(agent_id: str, estimated_tokens: int):
    budget = token_budgets[agent_id]

    if estimated_tokens > budget.max_input_tokens:
        # Compress context before proceeding
        compressed = compress_context(context, budget.max_input_tokens)
        return compressed

    return context
```

### Dynamic Model Routing

Route tasks to appropriate model tiers:

| Task Type | Model | Cost Savings |
|-----------|-------|--------------|
| Classification | Haiku | 90% |
| Summarization | Haiku | 90% |
| Simple extraction | Haiku | 90% |
| Complex reasoning | Sonnet | Baseline |
| Critical decisions | Opus | -200% (worth it) |

### Context Management Strategies

| Strategy | Token Savings | Use Case |
|----------|---------------|----------|
| Sliding window + summarization | 60-70% | Conversations >5 turns |
| Concise output formats | 70-85% | Agent-to-agent chaining |
| IDs-only formats | 85-95% | Bulk operations |
| Selective context loading | 40-60% | Large knowledge bases |

### Anti-Pattern: Unbounded Context

**Problem**: Agents accumulate context without pruning.

**Impact**: Token costs escalate exponentially, systems fail at context limits.

**Fix**: Implement max_history_tokens budgets, compress older context.

## Observability

### Distributed Tracing Requirements

Capture complete execution paths:

```json
{
  "trace": {
    "trace_id": "workflow_uuid",
    "spans": [
      {
        "span_id": "uuid",
        "parent_span_id": null,
        "operation": "workflow.start",
        "agent": "Orchestrator",
        "start_time": "ISO8601",
        "end_time": "ISO8601",
        "attributes": {
          "user_request": "...",
          "tokens_input": 150
        }
      },
      {
        "span_id": "uuid",
        "parent_span_id": "prev_uuid",
        "operation": "plan.create",
        "agent": "Planner",
        "attributes": {
          "tokens_input": 1200,
          "tokens_output": 800,
          "model": "claude-3-haiku",
          "latency_ms": 450
        }
      }
    ]
  }
}
```

### Metrics to Collect

| Category | Metrics |
|----------|---------|
| **Cost** | Tokens per agent, cost per workflow, model utilization ratio |
| **Latency** | Per-agent latency, total workflow latency, queue wait time |
| **Quality** | Success rate, verification pass rate, retry rate |
| **Coordination** | Message volume, conflict rate, escalation rate |

### State Tracking for Debugging

Log state at key points:

```python
def log_state_transition(
    agent_id: str,
    operation: str,
    state_before: dict,
    state_after: dict
):
    """Log state changes for replay capability."""
    logger.info({
        "event": "state_transition",
        "agent": agent_id,
        "operation": operation,
        "state_diff": compute_diff(state_before, state_after),
        "timestamp": datetime.utcnow().isoformat()
    })
```

### Failure Classification

Use MAST taxonomy for automated failure classification:

| Category | Percentage | Examples |
|----------|------------|----------|
| Specification | 41.77% | Missing inputs, vague outputs, no constraints |
| Alignment | 36.94% | Silent ignoring, role confusion, state conflicts |
| Verification | 21.30% | Weak checks, premature termination, self-grading |

## Governance

### Dynamic RBAC for Agents

Static permission lists are insufficient. Implement dynamic governance:

#### Three Pillars

**1. Certified Identity and Purpose**

```json
{
  "agent": {
    "id": "planner_001",
    "name": "TaskPlanner",
    "purpose": "Decompose requirements into task graphs",
    "risk_level": "low",
    "data_access": ["requirements", "constraints"],
    "forbidden_data": ["pii", "credentials"]
  }
}
```

**2. Central Policy Engine**

```python
def check_policy(agent_id: str, action: str, resource: str) -> bool:
    """Check if action is permitted for agent."""
    agent = get_agent_config(agent_id)
    policy = get_policy(agent.role)

    checks = [
        action_aligns_with_purpose(action, agent.purpose),
        resource_in_allowed_data(resource, agent.data_access),
        not_in_forbidden(resource, agent.forbidden_data),
        not_chaining_too_many_actions(agent_id)
    ]

    return all(checks)
```

**3. Runtime Enforcement with Audit**

```python
def enforce_and_audit(agent_id: str, action: str, resource: str):
    """Intercept, enforce, and audit agent actions."""
    # Check policy
    permitted = check_policy(agent_id, action, resource)

    # Audit regardless of outcome
    audit_log.append({
        "timestamp": datetime.utcnow().isoformat(),
        "agent": agent_id,
        "action": action,
        "resource": resource,
        "permitted": permitted,
        "policy_version": current_policy_version
    })

    if not permitted:
        raise PolicyViolation(f"{agent_id} cannot {action} on {resource}")
```

### Implementation Phases

1. **Inventory**: Classify agents by risk level
2. **Define Roles**: Document purpose, boundaries, forbidden actions
3. **Integrate RBAC**: Every agent action passes through enforcement
4. **Policy-as-Code**: Version-control policies, peer review changes
5. **Continuous Review**: Analyze audit logs for anomalies

## Compliance

### EU AI Act (August 2, 2026)

Key requirements for AI agents:

| Requirement | Implementation |
|-------------|----------------|
| Transparency | Document agent capabilities and limitations |
| Human oversight | Escalation paths, human-in-the-loop for critical decisions |
| Risk management | Classify agents by risk, implement proportional controls |
| Technical documentation | Maintain specs, audit logs, test results |
| Accuracy/robustness | Verification processes, failure handling |

### Compliance Checklist

- [ ] RBAC with granular permissions
- [ ] Audit logs capturing all agent decisions
- [ ] Prompt/content redaction capabilities
- [ ] Policy enforcement at runtime
- [ ] SSO/SCIM integration
- [ ] PII handling and DLP controls
- [ ] Incident response procedures
- [ ] Regular security assessments

### SOC 2 Considerations

SOC 2 Type II audits now scrutinize:
- AI agent access patterns
- Data handling by agents
- Change management for agent updates
- Incident response for agent failures

## 12 Factor Agents: Operational Principles

### Factor 9: Compact Errors into Context Window

**Principle**: Feed errors back into context so agents can self-correct. Implement error counters to prevent spin-outs.

#### Error Context Pattern

When an agent encounters an error, feed it back as context for self-correction:

```python
class ErrorContextManager:
    MAX_ERRORS_PER_TOOL = 3
    MAX_TOTAL_ERRORS = 5

    def __init__(self):
        self.error_counts = defaultdict(int)
        self.error_history = []

    def record_error(self, tool: str, error: dict) -> dict:
        """Record error and return context for retry."""
        self.error_counts[tool] += 1
        self.error_history.append({
            "tool": tool,
            "error": error,
            "timestamp": datetime.utcnow().isoformat(),
            "attempt": self.error_counts[tool]
        })

        # Check for spin-out
        if self.error_counts[tool] >= self.MAX_ERRORS_PER_TOOL:
            raise ToolSpinOut(f"{tool} failed {self.MAX_ERRORS_PER_TOOL} times")

        if sum(self.error_counts.values()) >= self.MAX_TOTAL_ERRORS:
            raise WorkflowSpinOut(f"Total errors exceeded {self.MAX_TOTAL_ERRORS}")

        # Return context for self-correction
        return {
            "previous_error": error,
            "attempt_number": self.error_counts[tool],
            "suggestion": f"Previous attempt failed: {error['message']}. Try different approach."
        }
```

#### Error Context Format

Feed errors back to the agent in a structured format:

```json
{
  "role": "system",
  "content": "PREVIOUS ATTEMPT FAILED\n\nError: [error message]\nAttempt: 2 of 3\n\nPlease try a different approach. Consider:\n- Alternative method X\n- Check assumption Y\n- Verify input Z"
}
```

#### Spin-Out Prevention

| Counter | Threshold | Action |
|---------|-----------|--------|
| Per-tool errors | 3 | Stop using that tool, try alternative |
| Total errors | 5 | Pause workflow, request human help |
| Retry without progress | 2 | Force different approach |
| Same error repeated | 2 | Escalate immediately |

#### Error Handling Workflow

```python
consecutive_errors = 0

while True:
    try:
        result = await handle_next_step(thread, next_step)
        thread["events"].append({
            "type": next_step["intent"] + '_result',
            "data": result,
        })
        # Success! Reset the error counter
        consecutive_errors = 0

    except Exception as e:
        consecutive_errors += 1
        if consecutive_errors < 3:
            # Feed error back into context and retry
            thread["events"].append({
                "type": 'error',
                "data": format_error_for_context(e),
            })
        else:
            # Too many errors - break loop, escalate
            await escalate_to_human(thread, e)
            break
```

See **`references/ops-runbook.md`** for detailed error handling procedures.

### Factor 11: Trigger from Anywhere

**Principle**: Meet users where they are. Agent triggering should be channel-agnostic.

#### Unified Trigger Interface

```python
class WorkflowTrigger:
    """
    Channel-agnostic workflow triggering.
    Same workflow can be started from any channel.
    """

    def trigger(self,
                input_data: dict,
                channel: str,
                user_id: str,
                metadata: dict = None) -> str:
        """
        Trigger workflow from any channel.

        Args:
            input_data: The actual request/task
            channel: Where this came from (slack, email, cli, api, webhook)
            user_id: Who triggered it
            metadata: Channel-specific metadata

        Returns:
            workflow_id for tracking
        """
        # Normalize input regardless of channel
        normalized = self.normalize_input(input_data, channel)

        # Create workflow with channel context
        workflow_id = self.workflow_controller.launch(
            input=normalized,
            context={
                "channel": channel,
                "user_id": user_id,
                "reply_to": self.get_reply_destination(channel, metadata)
            }
        )

        return workflow_id
```

#### Supported Channels

| Channel | Trigger Method | Response Method |
|---------|----------------|-----------------|
| Slack | Slash command, mention, DM | Thread reply |
| Email | Send to agent@domain.com | Reply email |
| CLI | `mas run <workflow>` | Stdout |
| API | POST /workflows | Webhook or poll |
| Webhook | POST from external system | Callback URL |
| Dashboard | Button click | UI notification |

#### Channel Adapters

```
┌─────────┐     ┌─────────────────┐     ┌──────────────┐
│  Slack  │────►│                 │────►│              │
├─────────┤     │  Unified        │     │   Workflow   │
│  Email  │────►│  Trigger        │────►│   Engine     │
├─────────┤     │  Interface      │     │              │
│   CLI   │────►│                 │────►│              │
├─────────┤     │                 │     │              │
│   API   │────►│                 │────►│              │
└─────────┘     └─────────────────┘     └──────────────┘
```

#### Response Routing

When workflow completes, route response back through original channel:

```python
def complete_workflow(self, workflow_id: str, result: dict):
    """Route result back to originating channel."""
    context = self.get_workflow_context(workflow_id)
    reply_to = context["reply_to"]

    match reply_to["type"]:
        case "slack":
            self.slack_client.post_message(
                reply_to["channel"],
                format_slack_response(result)
            )
        case "email":
            self.email_client.send(
                reply_to["to"],
                format_email_response(result)
            )
        case "webhook":
            self.http_client.post(reply_to["url"], result)
        case "cli":
            print(format_cli_response(result))
```

#### Channel Configuration

```yaml
channels:
  slack:
    enabled: true
    app_token: ${SLACK_APP_TOKEN}
    triggers:
      - slash_command: /mas
      - mention: @mas-agent
    response_format: slack_blocks

  email:
    enabled: true
    inbox: mas-agent@company.com
    response_format: html

  api:
    enabled: true
    auth: bearer_token
    rate_limit: 100/minute

  cli:
    enabled: true
    response_format: text
```

See **`references/ops-runbook.md`** for multi-channel deployment procedures.

## Production Checklist

### Before Deployment

- [ ] Token budgets set per agent
- [ ] Model routing configured
- [ ] Context management implemented
- [ ] Distributed tracing enabled
- [ ] Metrics collection active
- [ ] RBAC policies defined
- [ ] Audit logging enabled
- [ ] Compliance documentation complete

### 12 Factor Agents Checklist

- [ ] F9: Error context manager implemented (spin-out prevention)
- [ ] F9: Error counters per tool (max 3 attempts)
- [ ] F9: Total error threshold configured (max 5)
- [ ] F9: Human escalation path for repeated errors
- [ ] F11: Trigger interface supports required channels
- [ ] F11: Response routing configured per channel
- [ ] F11: Channel-specific formatting implemented

### Monitoring in Production

- [ ] Cost dashboards per workflow
- [ ] Latency alerts configured
- [ ] Error rate thresholds set
- [ ] State consistency checks active
- [ ] Audit log review scheduled
- [ ] Policy drift detection enabled
- [ ] Spin-out detection alerts (F9)
- [ ] Multi-channel health monitoring (F11)

## Additional Resources

### Reference Files

For detailed implementation guides:
- **`references/cost-optimization.md`** - Detailed cost reduction strategies
- **`references/compliance-details.md`** - Full compliance requirements
- **`references/ops-runbook.md`** - Operational procedures (includes error handling and multi-channel)
- **`../agent-specification/references/twelve-factor-agents.md`** - Quick reference for all 12 factors

### Related Skills

- **coordination-patterns** - Design observability into coordination (Factors 3, 5/6, 8, 12)
- **agent-specification** - Build governance into specs (Factors 1, 2, 4, 7)
- **mas-decision-gate** - Decide if multi-agent is needed (Factor 10)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/markusdegen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
