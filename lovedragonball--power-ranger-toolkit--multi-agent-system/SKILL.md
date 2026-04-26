---
name: multi-agent-system
description: Design and orchestrate multi-agent AI systems with knowledge harvesting, agent collaboration, and learning loops. Use when working on PSI Engine or similar autonomous agent projects. Use when this capability is needed.
metadata:
  author: lovedragonball
---

# 🤖 Multi-Agent System Skill

## Use Cases
- Agent spawning & lifecycle management
- Knowledge harvesting from completed tasks
- Agent-to-agent communication
- Learning loop implementation

---

## Agent Architecture

```
┌─────────────────────────────────────────┐
│              Orchestrator               │
│  (Assign tasks, monitor, coordinate)    │
└────────────────┬────────────────────────┘
                 │
    ┌────────────┼────────────┐
    ▼            ▼            ▼
┌────────┐  ┌────────┐  ┌────────┐
│ Agent 1│  │ Agent 2│  │ Agent 3│
│ (Task) │  │ (Task) │  │ (Task) │
└────┬───┘  └────┬───┘  └────┬───┘
     │           │           │
     └───────────┴───────────┘
                 │
                 ▼
        ┌────────────────┐
        │ Knowledge Base │
        │   (ChromaDB)   │
        └────────────────┘
```

---

## Agent Lifecycle

### 1. Spawn Agent
```python
def spawn_agent(agent_id: str, task: str):
    # Create PTY for agent terminal
    master, slave = pty.openpty()
    
    # Spawn process
    process = subprocess.Popen(
        ['claude', '--task', task],
        stdin=slave,
        stdout=slave,
        stderr=slave,
        start_new_session=True
    )
    
    return {
        'id': agent_id,
        'process': process,
        'master_fd': master,
        'status': 'running'
    }
```

### 2. Monitor Agent
```python
def monitor_agent(agent):
    # Read output non-blocking
    ready, _, _ = select.select([agent['master_fd']], [], [], 0.1)
    if ready:
        output = os.read(agent['master_fd'], 4096).decode()
        return output
    return None
```

### 3. Harvest Knowledge
```python
def harvest_knowledge(completed_task):
    # Extract learnings
    learnings = {
        'task': completed_task['description'],
        'solution': completed_task['output'],
        'patterns': extract_patterns(completed_task['output']),
        'timestamp': datetime.now().isoformat()
    }
    
    # Store in vector DB
    collection.add(
        documents=[learnings['solution']],
        metadatas=[learnings],
        ids=[f"learning_{uuid.uuid4()}"]
    )
```

---

## ChromaDB Integration

### Setup
```python
import chromadb

client = chromadb.Client()
collection = client.get_or_create_collection("knowledge_base")
```

### Store
```python
collection.add(
    documents=["Solution text here"],
    metadatas=[{"source": "agent_1", "task": "debug"}],
    ids=["unique_id"]
)
```

### Query (RAG)
```python
results = collection.query(
    query_texts=["How to fix null pointer?"],
    n_results=5
)
```

---

## Learning Loop

```
┌──────────────┐
│ Agent runs   │
│    task      │
└──────┬───────┘
       ▼
┌──────────────┐
│ Task result  │
│  extracted   │
└──────┬───────┘
       ▼
┌──────────────┐
│  Knowledge   │  ← Store patterns, solutions
│  harvested   │
└──────┬───────┘
       ▼
┌──────────────┐
│  Next agent  │  ← Query relevant context
│ uses context │
└──────────────┘
```

---

## Decision Tree

```
Multi-agent task?
├── Need new agent? → spawn_agent()
├── Agent stuck? → Check PTY buffer, restart if needed
├── Task complete? → Harvest knowledge → ChromaDB
├── Similar task? → Query ChromaDB for context
└── Coordination? → Use message queue/shared state
```

---

## Common Issues

| ปัญหา | สาเหตุ | แก้ไข |
|-------|--------|-------|
| Agent 3 malfunction | PTY buffer full | Increase buffer / flush regularly |
| Terminal blank | Non-blocking read timing | Use select() with timeout |
| Busy false positive | Status not reset | Reset status after task complete |
| Knowledge not found | Wrong embedding | Tune ChromaDB collection settings |

---

## PSI Engine Specific

1. **PTY Manager**: Always close unused file descriptors
2. **Agent Status**: Use enum (IDLE, RUNNING, COMPLETE, ERROR)
3. **Harvest timing**: Only harvest after verified completion
4. **Context injection**: Limit to 5 most relevant results

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lovedragonball) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
