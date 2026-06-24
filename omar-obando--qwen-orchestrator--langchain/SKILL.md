---
name: langchain
description: Use when building LLM applications with LangChain, implementing chains, agents, tools, memory, prompts, and retrieval systems. Includes best practices for prompt engineering, tool integration, and agent development. Based on LangChain/LangGraph official documentation and agent development best practices.
metadata:
  author: Omar-Obando
---

# LangChain Skill — LLM Applications & Agent Engineering

## Overview

This skill provides comprehensive guidance for building LLM applications with LangChain, implementing chains, agents, tools, memory, prompts, and retrieval systems. It includes best practices for prompt engineering, tool integration, and agent development. Based on LangChain/LangGraph official documentation and agent development best practices.

## When to Use

**Use this skill when:**

- Building LLM applications with LangChain
- Implementing chains (LLMChain, SequentialChain, RouterChain)
- Creating agents with tools and capabilities
- Integrating tools and external APIs (search, calculators, databases)
- Managing memory (ConversationBufferMemory, VectorStoreRetrieverMemory)
- Creating prompts and templates (PromptTemplate, FewShotPromptTemplate)
- Building retrieval-augmented generation (RAG) systems
- Implementing document loaders and parsers
- Using embeddings for semantic search
- Building chat applications with conversation history
- Implementing output parsers (StructuredOutputParser, JsonOutputParser)
- Creating prompt engineering patterns (few-shot, chain-of-thought)
- Building agents with tool calling capabilities
- Implementing agent memory with vector stores
- Creating agents with external knowledge sources
- Building agents with multi-step reasoning
- Using LangSmith for tracing and monitoring
- Implementing LangChain expression language (LCEL)
- Building agents with streaming output
- Creating agents with context window management

**Do NOT use this skill when:**

- Building stateful workflows with complex state (use **langgraph** skill)
- Designing database schema (use **database-design** skill)
- Creating UI components (use **frontend-design** skill)
- Implementing simple LLM calls without chains (use **llm-integrations** skill)
- Managing agent teams and coordination (use **agent-task-coordinator** skill)
- Building Qwen-specific agents (use **qwen-agent** skill)
- Implementing complex graph-based agent architectures (use **langgraph** skill)

**Why avoid:** LangChain is powerful but adds overhead. For simple LLM calls or stateful workflows, use more targeted skills.

## Core Components

### LLM Integration

```python
from langchain_openai import ChatOpenAI
from langchain_google_genai import ChatGoogleGenerativeAI

# OpenAI
llm = ChatOpenAI(
    model="gpt-4o",
    temperature=0.7,
    max_tokens=1000
)

# Google Gemini
llm = ChatGoogleGenerativeAI(
    model="gemini-3.1-pro",
    temperature=0.7
)

# Custom provider
llm = ChatOpenAI(
    model="openrouter/meta-llama/llama-3-70b-instruct:free",
    base_url="https://openrouter.ai/api/v1",
    api_key="your-key"
)
```

### Chains

```python
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

# Simple chain
prompt = PromptTemplate.from_template("What is the capital of {country}?")
chain = LLMChain(llm=llm, prompt=prompt)
result = chain.run("France")

# Sequential chain
from langchain.chains import SequentialChain

chain1 = LLMChain(llm=llm, prompt=prompt1, output_key="capital")
chain2 = LLMChain(llm=llm, prompt=prompt2, output_key="description")

sequential_chain = SequentialChain(
    chains=[chain1, chain2],
    input_variables=["country"],
    output_variables=["capital", "description"]
)

result = sequential_chain.run("France")
```

### Agents

```python
from langchain.agents import create_agent
from langchain.memory import InMemorySaver

# Simple agent
agent = create_agent(
    model="openai:gpt-4o",
    tools=[search_tool, calculator_tool],
    system_prompt="You are a helpful assistant.",
    checkpointer=InMemorySaver()
)

result = agent.invoke({"messages": [{"role": "user", "content": "What's the weather in Paris?"}]})
```

### Tools

```python
from langchain.tools import Tool
from langchain_community.tools import DuckDuckGoSearchRun

# Built-in tools
search_tool = DuckDuckGoSearchRun()

# Custom tool
def search_database(query: str) -> str:
    """Search the database for relevant information."""
    # Your database search logic
    return "Results"

search_db_tool = Tool(
    name="DatabaseSearch",
    func=search_database,
    description="Search the internal database for information"
)

tools = [search_tool, search_db_tool]
```

### Memory

```python
from langchain.memory import (
    ConversationBufferMemory,
    ConversationSummaryMemory,
    VectorStoreRetrieverMemory
)

# Buffer memory
memory = ConversationBufferMemory(memory_key="chat_history", return_messages=True)

# Summary memory
memory = ConversationSummaryMemory(llm=llm, memory_key="chat_history")

# Vector store memory
retriever = vector_store.as_retriever()
memory = VectorStoreRetrieverMemory(retriever=retriever)
```

## Prompts & Templates

### Basic Prompt Template

```python
from langchain.prompts import PromptTemplate

prompt = PromptTemplate.from_template(
    "Tell me a joke about {topic}. Make it funny."
)

result = llm.invoke(prompt.format(topic="dogs"))
```

### Few-Shot Prompting

```python
from langchain.prompts import FewShotPromptTemplate, PromptTemplate

examples = [
    {"input": "Hello", "output": "Hi there!"},
    {"input": "How are you?", "output": "I'm doing great, thanks!"},
]

example_prompt = PromptTemplate(
    input_variables=["input", "output"],
    template="Input: {input}\nOutput: {output}"
)

few_shot_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    suffix="Input: {input}\nOutput:",
    input_variables=["input"]
)

result = llm.invoke(few_shot_prompt.format(input="Good morning"))
```

### Chat Prompt Template

```python
from langchain.prompts import ChatPromptTemplate

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    ("human", "What is the capital of {country}?"),
])

chain = prompt | llm
result = chain.invoke({"country": "France"})
```

### Prompt Engineering Best Practices

```python
# ✅ GOOD: Clear instructions
prompt = PromptTemplate.from_template(
    """
    You are a data analyst. Analyze the following data and provide insights.

    Data: {data}

    Provide:
    1. Summary of key trends
    2. Anomalies detected
    3. Recommendations

    Keep your response concise and structured.
    """
)

# ❌ BAD: Vague instructions
prompt = PromptTemplate.from_template(
    "Analyze this data: {data}"
)
```

## Retrieval-Augmented Generation (RAG)

### Basic RAG

```python
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings
from langchain_core.output_parsers import StrOutputParser
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables import RunnablePassthrough

# Create vector store
embeddings = OpenAIEmbeddings()
vectorstore = Chroma.from_documents(
    documents=documents,
    embedding=embeddings
)

# RAG chain
template = """Answer the question based on the context:
Context: {context}
Question: {question}
Answer:"""

prompt = ChatPromptTemplate.from_template(template)

retriever = vectorstore.as_retriever()

def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

rag_chain = (
    {"context": retriever | format_docs, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)

result = rag_chain.invoke("What is the main topic?")
```

### Advanced RAG

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

# Compression retriever
compressor = LLMChainExtractor.from_llm(llm)
compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=vectorstore.as_retriever()
)

# RAG with compression
rag_chain = (
    {"context": compression_retriever | format_docs, "question": RunnablePassthrough()}
    | prompt
    | llm
    | StrOutputParser()
)
```

## Best Practices

### 1. Use Specific Prompts

```python
# ✅ GOOD: Specific instructions
prompt = PromptTemplate.from_template(
    """
    You are a Python expert. Explain the following concept clearly and concisely.
    Provide code examples when relevant.

    Concept: {concept}
    """
)

# ❌ BAD: Vague instructions
prompt = PromptTemplate.from_template("Explain {concept}")
```

### 2. Chain Composition

```python
# ✅ GOOD: Composable chains
chain1 = LLMChain(llm=llm, prompt=prompt1, output_key="analysis")
chain2 = LLMChain(llm=llm, prompt=prompt2, output_key="summary")

sequential_chain = SequentialChain(
    chains=[chain1, chain2],
    input_variables=["text"],
    output_variables=["analysis", "summary"]
)

# ❌ BAD: Monolithic chain
# All logic in one chain - hard to test and maintain
```

### 3. Error Handling

```python
# ✅ GOOD: Error handling
try:
    result = chain.run(input_text)
except Exception as e:
    logger.error(f"Error in chain: {e}")
    result = "An error occurred. Please try again."
```

### 4. Memory Management

```python
# ✅ GOOD: Appropriate memory
# For short conversations
memory = ConversationBufferMemory(memory_key="chat_history", return_messages=True)

# For long conversations
memory = ConversationSummaryMemory(llm=llm, memory_key="chat_history")

# For RAG-based memory
memory = VectorStoreRetrieverMemory(retriever=retriever)
```

## Common Anti-Patterns

### ❌ Bad: No Prompt Engineering

```python
# ❌ BAD: No prompt engineering
prompt = PromptTemplate.from_template("Generate text: {input}")
```

**Problems:**

- Unpredictable outputs
- No control over tone
- Poor quality results

### ✅ Good: With Prompt Engineering

```python
# ✅ GOOD: With prompt engineering
prompt = PromptTemplate.from_template(
    """
    You are a professional writer. Generate a blog post about {topic}.

    Requirements:
    - 500-700 words
    - Professional tone
    - Include 3 key points
    - Use bullet points for key points

    Topic: {topic}
    """
)
```

### ❌ Bad: No Error Handling

```python
# ❌ BAD: No error handling
result = chain.run(input_text)  # Can crash
```

### ✅ Good: With Error Handling

```python
# ✅ GOOD: With error handling
try:
    result = chain.run(input_text)
except Exception as e:
    logger.error(f"Error in chain: {e}")
    result = "An error occurred. Please try again."
```

## Real-World Impact

**Before this skill:**

- Unpredictable LLM outputs
- No prompt engineering
- Difficult to maintain chains
- No memory management

**After this skill:**

- Consistent LLM outputs
- Effective prompt engineering
- Maintainable chains
- Proper memory management

## Cross-References

- **`langgraph`** - For stateful workflows
- **`qwen-agent`** - For Qwen-specific agents
- **`llm-integrations`** - For LLM provider configuration

## References

- [LangChain Documentation](https://docs.langchain.com/)
- [LangChain OSS](https://docs.langchain.com/oss/)
- [LangChain Python](https://reference.langchain.com/python/langchain/)
- [LangChain JavaScript](https://reference.langchain.com/javascript/langchain/)

---
> Source: [Omar-Obando/qwen-orchestrator](https://github.com/Omar-Obando/qwen-orchestrator) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
