---
name: lmstudio-orchestrator
description: Deterministic multi-agent orchestration system for LMStudio enabling constraint-driven agent composition, semantic field navigation, and thermodynamic resource allocation. Use when orchestrating local LLM swarms, building multi-agent systems with validation, implementing constraint-based configurations, or requiring deterministic agent composition from discovered model states. Use when this capability is needed.
metadata:
  author: wheattoast11
---

# LMStudio Orchestrator

Constraint-driven orchestration system that transforms LMStudio into a deterministic multi-agent platform with semantic field navigation and compositional intelligence.

## Core Paradigm

This skill operates on three fundamental principles:

1. **Discovery Before Configuration**: Always introspect available resources before building
2. **Constraints Enable Composition**: Invalid states are unpresentable through validation
3. **Declarative Over Imperative**: Users specify intent, system determines implementation

## Phase 1: Discovery & Introspection

Before ANY orchestration, discover the current state:

```python
from scripts.discovery import LMStudioDiscovery

# Discover available resources
discovery = LMStudioDiscovery()
state = await discovery.introspect()

# Returns:
{
    "endpoint": "http://localhost:1234/v1",
    "models": ["qwen2.5-coder-32b", "llama-3.3-70b", ...],
    "capabilities": {"chat": True, "embeddings": True, "tools": True},
    "constraints": {"max_context": 32768, "max_parallel": 10}
}
```

## Phase 2: Constraint-Based Configuration

Build configurations that cannot fail:

```python
from scripts.constraints import ConfigurationSchema, AgentRegistry

# Define constraints
schema = ConfigurationSchema()
schema.add_constraint('agents.count', lambda x: 1 <= x <= 20)
schema.add_constraint('agents.*.temperature', lambda x: 0.0 <= x <= 1.0)
schema.add_constraint('strategy', lambda x: x in VALID_STRATEGIES)

# Compose from templates
registry = AgentRegistry()
agent = registry.create_agent(
    role="researcher",  # Constrained to valid roles
    model=state.models[0],  # From discovered models
    temperature=0.7  # Validated against constraints
)
```

## Phase 3: Compositional Orchestration

### Strategy 1: Declarative Composition

User declares intent, system composes implementation:

```python
from scripts.composer import CombinatorialComposer

composer = CombinatorialComposer(registry, strategies)
swarm = composer.compose_swarm(
    query="Analyze quantum computing architectures",
    available_models=state.models,
    constraints={"max_agents": 5, "timeout": 120}
)

# System automatically:
# 1. Analyzes query → requirements
# 2. Selects roles based on requirements
# 3. Assigns models to roles optimally
# 4. Chooses execution strategy
# 5. Validates entire configuration
```

### Strategy 2: Semantic Field Navigation

Navigate solution spaces through resonance:

```python
from scripts.semantic import SemanticNavigator

navigator = SemanticNavigator(orchestrator)
path = await navigator.find_resonance_path(
    start_concept="database optimization",
    target_outcome="10x performance",
    exploration_depth=5
)

# Executes agents along semantic gradient
for node in path.nodes:
    agent = composer.create_for_concept(node.concept)
    result = await agent.execute(node.prompt)
```

### Strategy 3: Thermodynamic Resource Allocation

Minimize energy across available resources:

```python
from scripts.thermodynamic import ThermodynamicScheduler

scheduler = ThermodynamicScheduler(resource_monitor)
execution_plan = scheduler.optimize(
    tasks=task_list,
    constraints={
        "max_vram": 0.9,
        "preserve_responsiveness": True,
        "energy_function": lambda agent: agent.tokens / agent.requests
    }
)
```

## Execution Strategies

All strategies are constraint-validated before execution:

### PARALLEL
```python
swarm.orchestrate(task, strategy="parallel")
# Constraints: min_agents=1, max_agents=20, timeout=60
```

### CONSENSUS
```python
swarm.orchestrate(task, strategy="consensus", threshold=0.8)
# Constraints: min_agents=3, max_agents=15, rounds=2
```

### HIERARCHICAL
```python
swarm.orchestrate(task, strategy="hierarchical", coordinator="architect")
# Constraints: min_agents=2, requires coordinator role
```

### SEMANTIC
```python
swarm.orchestrate(task, strategy="semantic", resonance_threshold=0.7)
# Constraints: requires embedding support
```

### COMBINATORIAL
```python
swarm.orchestrate(task, strategy="combinatorial", combinations="pairwise")
# Constraints: min_agents=2, max_agents=16
```

## Agent Templates

Agents are created from validated templates:

```yaml
researcher:
  temperature: [0.7, 0.9]
  capabilities: [search, analyze, synthesize]
  preferred_models: [llama-3.3-70b, qwen2.5-72b]
  
analyst:
  temperature: [0.3, 0.5]
  capabilities: [decompose, structure, categorize]
  preferred_models: [qwen2.5-72b, deepseek-r1]

critic:
  temperature: [0.2, 0.4]  
  capabilities: [validate, challenge, verify]
  preferred_models: [qwen2.5-72b, llama-3.3-70b]
```

## Resource Monitoring

Real-time resource tracking with predictive allocation:

```python
from scripts.monitor import ResourceMonitor

monitor = ResourceMonitor()
await monitor.start()

# Before spawning
availability = monitor.predict_availability("llama-3.3-70b")
if not availability["available"]:
    # Use smaller model or wait
    model = monitor.recommend_alternative()

# During execution
metrics = monitor.get_metrics()
# {vram_used: 45GB, tokens_per_sec: 127, active_agents: 5}
```

## Web Interface

Launch the deterministic orchestration interface:

```python
from scripts.interface import OrchestrationInterface

interface = OrchestrationInterface(
    discovery=discovery,
    composer=composer,
    monitor=monitor
)

await interface.serve(port=8080)
# Access at http://localhost:8080
```

The interface provides:
- Real-time model discovery
- Constraint-validated agent spawning
- Semantic field visualization
- Resource usage monitoring
- Execution strategy selection

## Error Recovery

All operations include deterministic error handling:

```python
@with_constraints(
    max_retries=3,
    backoff="exponential",
    fallback="smaller_model"
)
async def reliable_execution(task):
    return await swarm.orchestrate(task)
```

## Complete Example

```python
from scripts.orchestrator import DeterministicOrchestrator

async def main():
    # Phase 1: Discovery
    orch = DeterministicOrchestrator()
    await orch.discover()
    
    # Phase 2: Composition
    swarm = await orch.compose(
        "Design a distributed cache with Redis",
        constraints={"max_agents": 5}
    )
    
    # Phase 3: Execution
    result = await swarm.execute()
    
    # Result includes:
    # - Final synthesis
    # - Agent contributions
    # - Consensus score
    # - Resource usage
    # - Execution trace
```

## Validation Guarantees

Every configuration is validated before execution:

1. **Model Validation**: Only discovered models can be used
2. **Role Validation**: Agents must match template constraints  
3. **Strategy Validation**: Agent count must satisfy strategy requirements
4. **Resource Validation**: VRAM requirements must be available
5. **Timeout Validation**: Execution time within limits

## Best Practices

1. **Always discover first** - Never hardcode models or endpoints
2. **Compose declaratively** - Let the system determine implementation
3. **Validate continuously** - Check constraints at every step
4. **Monitor resources** - Predict and prevent exhaustion
5. **Handle gracefully** - Partial success over total failure

## Troubleshooting

### Discovery Fails
```bash
# Check LMStudio is running
scripts/check_lmstudio.sh

# Try manual discovery
scripts/discover.py --port 1234
```

### Invalid Configuration
```python
# Validate configuration
valid, errors = schema.validate(config)
for error in errors:
    print(f"Constraint violation: {error}")
```

### Resource Exhaustion
```python
# Check available resources
availability = monitor.check_resources()
if availability["vram_available"] < required:
    # Unload unused models
    monitor.optimize_allocation()
```

## References

- See `references/constraints.yaml` for all constraint definitions
- See `references/templates.yaml` for agent templates
- See `references/strategies.json` for execution strategies
- See `scripts/` for all implementation modules

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wheattoast11) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
