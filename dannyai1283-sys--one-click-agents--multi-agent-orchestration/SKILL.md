---
name: multi-agent-orchestration
description: Coordinate multiple AI agents for complex task execution. Includes task distribution, result aggregation, agent communication protocols, and workflow orchestration patterns. Use when this capability is needed.
metadata:
  author: dannyai1283-sys
---

# Multi-Agent Orchestration

Coordinate multiple AI agents to work together on complex tasks. This template provides patterns for task distribution, inter-agent communication, and result aggregation.

## When to Use

- Complex tasks requiring multiple specialized agents
- Parallel processing of independent subtasks
- Review and approval workflows
- Multi-step research or analysis tasks
- Load balancing across agent instances
- Fault-tolerant agent execution

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Orchestrator                             │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐          │
│  │   Planner   │  │  Router     │  │  Monitor    │          │
│  └─────────────┘  └─────────────┘  └─────────────┘          │
└────────────────────┬────────────────────────────────────────┘
                     │
        ┌────────────┼────────────┐
        │            │            │
   ┌────▼────┐  ┌────▼────┐  ┌────▼────┐
   │ Agent A │  │ Agent B │  │ Agent C │
   │(Research│  │ (Code)  │  │(Review) │
   └────┬────┘  └────┬────┘  └────┬────┘
        │            │            │
        └────────────┼────────────┘
                     │
              ┌──────▼──────┐
              │ Aggregator  │
              └─────────────┘
```

## Quick Start

```bash
# Setup orchestration environment
./setup.sh

# Start the orchestration server
./orchestrator.sh start

# Submit a task
./orchestrator.sh submit "Analyze this codebase for security issues"

# Check agent status
./orchestrator.sh status
```

## Agent Types

### Research Agent
```python
# agents/research_agent.py
class ResearchAgent(BaseAgent):
    """Agent specialized in research and information gathering"""
    
    def __init__(self):
        super().__init__(
            name="researcher",
            capabilities=["web_search", "document_analysis", "summarization"]
        )
    
    async def execute(self, task: Task) -> Result:
        # Research workflow
        search_results = await self.search(task.query)
        analysis = await self.analyze(search_results)
        return Result(
            data=analysis,
            confidence=self.calculate_confidence(analysis)
        )
```

### Code Agent
```python
# agents/code_agent.py
class CodeAgent(BaseAgent):
    """Agent specialized in code generation and analysis"""
    
    def __init__(self):
        super().__init__(
            name="coder",
            capabilities=["code_generation", "refactoring", "debugging"]
        )
    
    async def execute(self, task: Task) -> Result:
        # Code workflow
        context = await self.gather_context(task)
        code = await self.generate(task.specification, context)
        tests = await self.generate_tests(code)
        return Result(
            code=code,
            tests=tests,
            documentation=await self.document(code)
        )
```

### Review Agent
```python
# agents/review_agent.py
class ReviewAgent(BaseAgent):
    """Agent specialized in code review and quality assurance"""
    
    def __init__(self):
        super().__init__(
            name="reviewer",
            capabilities=["code_review", "security_audit", "performance_analysis"]
        )
    
    async def execute(self, task: Task) -> Result:
        # Review workflow
        issues = await self.find_issues(task.code)
        suggestions = await self.suggest_improvements(task.code)
        return Result(
            approved=len(issues.critical) == 0,
            issues=issues,
            suggestions=suggestions
        )
```

## Task Distribution

### Simple Round-Robin
```python
# orchestrator/distributors.py
class RoundRobinDistributor:
    """Distributes tasks evenly across agents"""
    
    def __init__(self, agents: List[Agent]):
        self.agents = agents
        self.current_index = 0
    
    def distribute(self, task: Task) -> Agent:
        agent = self.agents[self.current_index]
        self.current_index = (self.current_index + 1) % len(self.agents)
        return agent
```

### Capability-Based
```python
class CapabilityDistributor:
    """Routes tasks to agents based on required capabilities"""
    
    def __init__(self, agents: List[Agent]):
        self.agents = agents
        self.capability_map = self._build_capability_map()
    
    def distribute(self, task: Task) -> Agent:
        required = task.required_capabilities
        candidates = [
            agent for agent in self.agents
            if all(cap in agent.capabilities for cap in required)
        ]
        # Select least busy agent
        return min(candidates, key=lambda a: a.pending_tasks)
```

### Load-Balanced
```python
class LoadBalancedDistributor:
    """Distributes tasks based on agent load"""
    
    def distribute(self, task: Task) -> Agent:
        return min(
            self.agents,
            key=lambda a: (
                a.queue_depth / a.max_concurrent,
                a.avg_response_time
            )
        )
```

## Communication Patterns

### Message Bus
```python
# communication/message_bus.py
class MessageBus:
    """Pub/sub communication between agents"""
    
    def __init__(self):
        self.subscribers: Dict[str, List[Callable]] = {}
    
    def subscribe(self, topic: str, handler: Callable):
        self.subscribers.setdefault(topic, []).append(handler)
    
    async def publish(self, topic: str, message: Message):
        handlers = self.subscribers.get(topic, [])
        await asyncio.gather(*[h(message) for h in handlers])
```

### Direct Messaging
```python
class DirectMessenger:
    """Point-to-point agent communication"""
    
    def __init__(self):
        self.mailboxes: Dict[str, asyncio.Queue] = {}
    
    def register(self, agent_id: str):
        self.mailboxes[agent_id] = asyncio.Queue()
    
    async def send(self, to: str, message: Message):
        await self.mailboxes[to].put(message)
    
    async def receive(self, agent_id: str) -> Message:
        return await self.mailboxes[agent_id].get()
```

### Shared State
```python
class SharedState:
    """Distributed state management for agents"""
    
    def __init__(self):
        self._state: Dict[str, Any] = {}
        self._locks: Dict[str, asyncio.Lock] = {}
    
    async def get(self, key: str) -> Any:
        return self._state.get(key)
    
    async def set(self, key: str, value: Any):
        async with self._locks.setdefault(key, asyncio.Lock()):
            self._state[key] = value
    
    async def update(self, key: str, updater: Callable):
        async with self._locks.setdefault(key, asyncio.Lock()):
            self._state[key] = updater(self._state.get(key))
```

## Workflow Patterns

### Sequential Pipeline
```python
# workflows/pipeline.py
class SequentialPipeline:
    """Execute agents in sequence, passing output to next"""
    
    def __init__(self, agents: List[Agent]):
        self.agents = agents
    
    async def execute(self, initial_input: Any) -> Result:
        data = initial_input
        for agent in self.agents:
            result = await agent.execute(Task(data=data))
            data = result.data
        return data

# Usage
pipeline = SequentialPipeline([
    ResearchAgent(),
    CodeAgent(),
    ReviewAgent()
])
result = await pipeline.execute("Build a REST API")
```

### Parallel Map-Reduce
```python
class MapReduceWorkflow:
    """Execute agents in parallel, then aggregate results"""
    
    async def execute(
        self,
        items: List[Any],
        mapper: Agent,
        reducer: Agent
    ) -> Result:
        # Map phase - parallel execution
        map_tasks = [mapper.execute(Task(data=item)) for item in items]
        map_results = await asyncio.gather(*map_tasks)
        
        # Reduce phase - aggregate results
        return await reducer.execute(
            Task(data=[r.data for r in map_results])
        )
```

### Consensus Workflow
```python
class ConsensusWorkflow:
    """Multiple agents vote on a decision"""
    
    def __init__(self, agents: List[Agent], threshold: float = 0.6):
        self.agents = agents
        self.threshold = threshold
    
    async def execute(self, task: Task) -> Result:
        # Get opinions from all agents
        opinions = await asyncio.gather(*[
            agent.execute(task) for agent in self.agents
        ])
        
        # Aggregate votes
        votes = defaultdict(float)
        for opinion in opinions:
            votes[opinion.decision] += opinion.confidence
        
        total = sum(votes.values())
        winner = max(votes.items(), key=lambda x: x[1])
        
        return Result(
            decision=winner[0],
            confidence=winner[1] / total,
            consensus=winner[1] / total >= self.threshold
        )
```

## Task Examples

### Code Review Workflow
```python
# Submit a code review task
workflow = SequentialPipeline([
    CodeAgent(),       # Generate code
    ReviewAgent(),     # Review code
    ResearchAgent()    # Research improvements
])

result = await workflow.execute({
    "specification": "Create a user authentication module",
    "language": "python",
    "framework": "fastapi"
})
```

### Research Synthesis
```python
# Parallel research with synthesis
async def research_topic(topic: str):
    # Multiple agents research different aspects
    aspects = ["history", "current_state", "future_trends", "criticisms"]
    
    research_agents = [ResearchAgent() for _ in aspects]
    tasks = [
        Task(query=f"{topic} {aspect}")
        for aspect in aspects
    ]
    
    # Execute in parallel
    results = await asyncio.gather(*[
        agent.execute(task)
        for agent, task in zip(research_agents, tasks)
    ])
    
    # Synthesize results
    synthesizer = SynthesisAgent()
    return await synthesizer.execute(
        Task(data=[r.data for r in results])
    )
```

## Monitoring & Observability

### Agent Metrics
```python
# monitoring/metrics.py
class AgentMetrics:
    """Track agent performance and health"""
    
    def __init__(self):
        self.task_count = Counter()
        self.task_duration = Histogram()
        self.error_rate = Gauge()
        self.queue_depth = Gauge()
    
    def record_task_start(self, agent_id: str):
        self.task_count.labels(agent=agent_id).inc()
        self.task_duration.labels(agent=agent_id).start()
    
    def record_task_complete(self, agent_id: str, success: bool):
        duration = self.task_duration.labels(agent=agent_id).observe()
        if not success:
            self.error_rate.labels(agent=agent_id).inc()
```

### Health Checks
```python
class HealthChecker:
    """Monitor agent health and restart if needed"""
    
    async def check_health(self, agent: Agent) -> HealthStatus:
        try:
            response = await asyncio.wait_for(
                agent.ping(),
                timeout=5.0
            )
            return HealthStatus.HEALTHY
        except asyncio.TimeoutError:
            return HealthStatus.UNRESPONSIVE
        except Exception:
            return HealthStatus.UNHEALTHY
```

## Fault Tolerance

### Retry Logic
```python
# resilience/retry.py
class RetryPolicy:
    """Retry failed tasks with backoff"""
    
    def __init__(
        self,
        max_retries: int = 3,
        base_delay: float = 1.0,
        max_delay: float = 60.0
    ):
        self.max_retries = max_retries
        self.base_delay = base_delay
        self.max_delay = max_delay
    
    async def execute(self, task: Callable) -> Result:
        for attempt in range(self.max_retries + 1):
            try:
                return await task()
            except Exception as e:
                if attempt == self.max_retries:
                    raise
                delay = min(
                    self.base_delay * (2 ** attempt),
                    self.max_delay
                )
                await asyncio.sleep(delay)
```

### Circuit Breaker
```python
class CircuitBreaker:
    """Prevent cascading failures"""
    
    def __init__(
        self,
        failure_threshold: int = 5,
        recovery_timeout: float = 30.0
    ):
        self.failure_threshold = failure_threshold
        self.recovery_timeout = recovery_timeout
        self.failure_count = 0
        self.state = CircuitState.CLOSED
        self.last_failure_time = None
    
    async def call(self, func: Callable) -> Result:
        if self.state == CircuitState.OPEN:
            if time.time() - self.last_failure_time > self.recovery_timeout:
                self.state = CircuitState.HALF_OPEN
            else:
                raise CircuitBreakerOpen()
        
        try:
            result = await func()
            self.on_success()
            return result
        except Exception as e:
            self.on_failure()
            raise
```

## Best Practices

1. **Idempotent Tasks** - Agents should handle duplicate executions gracefully
2. **Timeouts** - Always set timeouts for agent operations
3. **Stateless Design** - Keep agents stateless, use shared state for persistence
4. **Observability** - Log all agent interactions and decisions
5. **Graceful Degradation** - Handle agent failures without breaking the workflow
6. **Resource Limits** - Limit concurrent tasks per agent
7. **Security** - Isolate agent execution environments

## Integration with OpenClaw

```bash
# Start orchestration
openclaw agents start

# Submit task to agent swarm
openclaw agents submit "Review pull request #123"

# Check agent status
openclaw agents status

# Scale agent pool
openclaw agents scale coder --replicas 3
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dannyai1283-sys) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
