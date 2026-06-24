---
name: pydantic-ai-agent-creation
description: 创建具有类型安全依赖项、结构化输出和正确配置的 PydanticAI 代理。这些代理可用于构建 AI 代理、创建聊天系统，或将大型语言模型（LLMs）与 Pydantic 的验证功能集成在一起。 Use when this capability is needed.
metadata:
  author: AgentWorkers
---
# 创建 PydanticAI 代理

## 快速入门

```python
from pydantic_ai import Agent

# Minimal agent (text output)
agent = Agent('openai:gpt-4o')
result = agent.run_sync('Hello!')
print(result.output)  # str
```

## 模型选择

模型字符串遵循 `provider:model-name` 的格式：

```python
# OpenAI
agent = Agent('openai:gpt-4o')
agent = Agent('openai:gpt-4o-mini')

# Anthropic
agent = Agent('anthropic:claude-sonnet-4-5')
agent = Agent('anthropic:claude-haiku-4-5')

# Google
agent = Agent('google-gla:gemini-2.0-flash')
agent = Agent('google-vertex:gemini-2.0-flash')

# Others: groq:, mistral:, cohere:, bedrock:, etc.
```

## 结构化输出

使用 Pydantic 模型来生成经过验证、具有数据类型的响应：

```python
from pydantic import BaseModel
from pydantic_ai import Agent

class CityInfo(BaseModel):
    city: str
    country: str
    population: int

agent = Agent('openai:gpt-4o', output_type=CityInfo)
result = agent.run_sync('Tell me about Paris')
print(result.output.city)  # "Paris"
print(result.output.population)  # int, validated
```

## 代理配置

```python
from pydantic_ai import Agent
from pydantic_ai.settings import ModelSettings

agent = Agent(
    'openai:gpt-4o',
    output_type=MyOutput,           # Structured output type
    deps_type=MyDeps,               # Dependency injection type
    instructions='You are helpful.',  # Static instructions
    retries=2,                      # Retry attempts for validation
    name='my-agent',                # For logging/tracing
    model_settings=ModelSettings(   # Provider settings
        temperature=0.7,
        max_tokens=1000
    ),
    end_strategy='early',           # How to handle tool calls with results
)
```

## 运行代理

有三种执行方法：

```python
# Async (preferred)
result = await agent.run('prompt', deps=my_deps)

# Sync (convenience)
result = agent.run_sync('prompt', deps=my_deps)

# Streaming
async with agent.run_stream('prompt') as response:
    async for chunk in response.stream_output():
        print(chunk, end='')
```

## 用户指令与系统提示

```python
# Instructions: Concatenated, for agent behavior
agent = Agent(
    'openai:gpt-4o',
    instructions='You are a helpful assistant. Be concise.'
)

# Dynamic instructions via decorator
@agent.instructions
def add_context(ctx: RunContext[MyDeps]) -> str:
    return f"User ID: {ctx.deps.user_id}"

# System prompts: Static, for model context
agent = Agent(
    'openai:gpt-4o',
    system_prompt=['You are an expert.', 'Always cite sources.']
)
```

## 常见模式

### 参数化代理（类型安全）

```python
from dataclasses import dataclass
from pydantic_ai import Agent, RunContext

@dataclass
class Deps:
    api_key: str
    user_id: int

agent: Agent[Deps, str] = Agent(
    'openai:gpt-4o',
    deps_type=Deps,
)

# deps is now required and type-checked
result = agent.run_sync('Hello', deps=Deps(api_key='...', user_id=123))
```

### 无依赖项（满足类型检查）

```python
# Option 1: Explicit type annotation
agent: Agent[None, str] = Agent('openai:gpt-4o')

# Option 2: Pass deps=None
result = agent.run_sync('Hello', deps=None)
```

## 验证步骤

在生产代码中使用代理之前，请按以下顺序执行这些验证步骤：

1. **简单测试** — 执行 `agent.run_sync('Reply with OK.')`（或在异步代码中执行 `await agent.run(...)`）。**通过条件**：调用成功完成且 `result.output` 存在。
2. **结构化输出** — 如果设置了 `output_type`，则需要输入符合该结构的响应。**通过条件**：`result.output` 是你的 Pydantic 模型的实例；如果多次验证失败，说明需要优化指令或增加重试机制，而不是立即添加新功能。
3. **依赖项检查** — 如果设置了 `deps_type`，则在调用 `run` 或 `run_sync` 时传入相应的依赖项。**通过条件**：调用能够通过类型检查，并且仅因模型或 API 的问题而失败，而不是因为缺少或错误的依赖项值。

## 决策框架

| 场景 | 配置方式 |
|----------|--------------|
| 简单文本响应 | `Agent(model)` |
| 结构化数据提取 | `Agent(model, output_type=MyModel)` |
| 需要外部服务 | 添加 `deps_type=MyDeps` |
| 需要多次验证 | 增加 `retries=3` |
| 调试/监控 | 设置 `instrument=True` |

---
> Source: [AgentWorkers/skills](https://github.com/AgentWorkers/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
