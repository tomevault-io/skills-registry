---
name: accessibility-readability
description: Ensure textbook content is accessible, readable, and understandable for learners of all skill levels. Use when reviewing content for clarity, adding explanations for beginners, or improving content accessibility. Use when this capability is needed.
metadata:
  author: maneeshanif
---

# Accessibility & Readability Skill

## Instructions

### 1. Reading Level Guidelines

| Audience | Flesch Score | Characteristics |
|----------|--------------|-----------------|
| Beginner | 60-70 | Short sentences, common words, many examples |
| Intermediate | 50-60 | Technical terms with definitions, moderate complexity |
| Advanced | 40-50 | Assumes prior knowledge, concise explanations |

### 2. Explain Technical Terms

**First mention** of any technical term MUST include:

```mdx
**ROS 2** (Robot Operating System 2) is a middleware framework that provides 
tools and libraries for building robot applications. Think of it as the 
"nervous system" that lets different parts of a robot communicate.
```

### 3. Glossary Integration

Create `docs/glossary.mdx` with all terms:

```mdx
## R

### ROS 2
**Robot Operating System 2** — A middleware framework for robotics development.
- **Used in**: Modules 1, 3, 4
- **Prerequisite for**: Understanding node communication
- **Related**: Topics, Services, Actions

### rclpy
**ROS Client Library for Python** — Python bindings for ROS 2.
- **Installation**: `pip install rclpy`
- **Import**: `import rclpy`
```

Link to glossary on first use:
```mdx
We'll use [ROS 2](/docs/glossary#ros-2) to control our robot.
```

### 4. Multi-Level Explanations

Provide layered depth for complex topics:

```mdx
## What is a ROS 2 Node?

### 🟢 Simple Explanation (Beginner)
A node is like a worker in a factory. Each worker (node) has one job, 
and they communicate by passing messages to each other.

### 🟡 Technical Explanation (Intermediate)
A node is an independent process that performs computation. Nodes 
communicate via topics (pub/sub), services (request/response), or 
actions (long-running tasks with feedback).

### 🔴 Deep Dive (Advanced)
Nodes are executables within a ROS 2 package. They use DDS (Data 
Distribution Service) for communication, with QoS (Quality of Service) 
policies controlling reliability, durability, and history.
```

### 5. Prerequisite Mapping

Every chapter must clearly state what knowledge is assumed:

```mdx
## 📋 Prerequisites

**Required Knowledge:**
- ✅ Basic Python (variables, functions, classes)
- ✅ Command line basics (cd, ls, mkdir)
- ✅ [Chapter 1.1 — Introduction to Physical AI](/docs/module-1/week-1-2/)

**Helpful but Optional:**
- ⭕ Linux system administration
- ⭕ Previous robotics experience

**You'll Learn Here:**
- 🆕 ROS 2 node creation
- 🆕 Publisher/subscriber pattern
```

### 6. Code Accessibility

Always explain code step-by-step:

```mdx
```python
# Step 1: Import the ROS 2 Python library
import rclpy
from rclpy.node import Node

# Step 2: Create a class that inherits from Node
class MyNode(Node):
    def __init__(self):
        # Step 3: Initialize the parent class with a node name
        super().__init__('my_node')  # 'my_node' is visible in ROS tools
        
        # Step 4: Log a message to confirm the node started
        self.get_logger().info('Node has started!')
```

**Line-by-line breakdown:**
| Line | Purpose |
|------|---------|
| `import rclpy` | Load the ROS 2 Python library |
| `from rclpy.node import Node` | Import the base Node class |
| `class MyNode(Node)` | Create our custom node |
| `super().__init__('my_node')` | Register with ROS 2 |
| `self.get_logger().info(...)` | Print to ROS 2 logging system |
```

### 7. Visual Learning Aids

For each major concept, include:

1. **Analogy** — Real-world comparison
2. **Diagram** — Visual representation
3. **Code** — Working example
4. **Exercise** — Hands-on practice

```mdx
## Understanding Topics

### 🎯 Analogy
Topics are like radio channels. Publishers broadcast on a channel, 
and any subscriber tuned to that channel receives the message.

### 📊 Diagram
```mermaid
graph LR
    P[Publisher] -->|broadcasts| T[/topic_name]
    T -->|receives| S1[Subscriber 1]
    T -->|receives| S2[Subscriber 2]
```

### 💻 Code Example
[See code block above]

### ✍️ Exercise
Create a publisher that sends your name every second.
```

### 8. Accessibility Standards

**Images:**
```mdx
![Diagram showing ROS 2 node communication with arrows between publisher and subscriber nodes](/img/ros2-nodes.svg)
```

**Color contrast:** Don't rely solely on color
```mdx
✅ Good: "The green checkmarks (✅) indicate success"
❌ Bad: "Green items are complete"
```

**Keyboard navigation:** Ensure all interactive elements are accessible

### 9. Progress Indicators

Help learners track their journey:

```mdx
## 📍 Your Progress

| Module | Status | Completion |
|--------|--------|------------|
| Module 1: ROS 2 | 🟢 Current | 60% |
| Module 2: Simulation | ⚪ Upcoming | 0% |
| Module 3: Isaac | ⚪ Upcoming | 0% |
| Module 4: VLA | ⚪ Upcoming | 0% |

**Estimated time to complete this chapter:** 45 minutes
```

### 10. Recap Sections

End each major section with a quick recap:

```mdx
## 🔄 Quick Recap

**What we covered:**
- ✅ Created a ROS 2 node using Python
- ✅ Published messages to a topic
- ✅ Subscribed to receive messages

**Key commands:**
```bash
ros2 run my_package my_node    # Run a node
ros2 topic list                 # List all topics
ros2 topic echo /topic_name    # View messages
```

**Common mistakes to avoid:**
- ❌ Forgetting to call `rclpy.init()` before creating nodes
- ❌ Not spinning the node (`rclpy.spin(node)`)
```

## Validation Checklist

- [ ] All technical terms defined on first use
- [ ] Glossary entries for domain-specific terms
- [ ] Multi-level explanations for complex topics
- [ ] Prerequisites clearly stated
- [ ] Code explained line-by-line for beginners
- [ ] Images have descriptive alt text
- [ ] Estimated reading time provided
- [ ] Quick recaps at section ends

## Definition of Done

- Content passes readability check for target audience
- All technical terms linked to glossary
- Beginner/intermediate/advanced sections where appropriate
- Every code block has explanatory comments
- Accessibility standards met (alt text, contrast, etc.)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maneeshanif) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
