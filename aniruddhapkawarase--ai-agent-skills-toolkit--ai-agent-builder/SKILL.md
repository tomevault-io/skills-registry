---
name: ai-agent-builder
description: Principal AI Architect. LangChain/LangGraph (chains, agents, tools, memory), CrewAI (orchestration, roles), Claude Agent SDK, RAG pipelines (chunking, embeddings, vector stores), context management, prompt engineering (CoT, few-shot), guardrails, cost optimization. Use when this capability is needed.
metadata:
  author: AniruddhaPKawarase
---

# Principal AI Agent Architecture & Development

## Overview

A 30-year architect treats AI agents as distributed systems: they have state, dependencies, failure modes, and scaling limits. Building production-grade agents requires understanding token economics, context windows, tool integration patterns, and graceful degradation.

## Agent Architecture Patterns

### Pattern 1: Linear Chain (Deterministic)
**Use case**: Form filling, sequential data extraction, predictable workflows.

```python
from langchain.llms import ChatOpenAI
from langchain.chains import SequentialChain

llm = ChatOpenAI(model='gpt-4', temperature=0)

# Step 1: Extract entities
entity_chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate(
        input_variables=['text'],
        template="Extract all people names from: {text}"
    ),
    output_key='entities'
)

# Step 2: Classify sentiment
sentiment_chain = LLMChain(
    llm=llm,
    prompt=PromptTemplate(
        input_variables=['text'],
        template="Rate sentiment of: {text} as positive/negative/neutral"
    ),
    output_key='sentiment'
)

# Sequential execution
chain = SequentialChain(
    chains=[entity_chain, sentiment_chain],
    input_variables=['text'],
    output_variables=['entities', 'sentiment']
)

result = chain({'text': "Alice and Bob are great friends!"})
# Output: {'entities': 'Alice, Bob', 'sentiment': 'positive'}
```

**Advantage**: Predictable, easy to debug, cheap token usage.
**Disadvantage**: No adaptive behavior, no tool use, rigid structure.

### Pattern 2: ReAct (Reasoning + Acting)
**Use case**: Research, API integration, multi-step problem solving.

```python
from langchain.agents import initialize_agent, Tool
from langchain.agents import AgentType

# Define tools
tools = [
    Tool(
        name='web_search',
        func=search_wikipedia,
        description='Search Wikipedia for information'
    ),
    Tool(
        name='calculator',
        func=eval_math,
        description='Evaluate math expressions'
    ),
    Tool(
        name='web_scraper',
        func=scrape_url,
        description='Fetch and parse URL content'
    )
]

# Create ReAct agent
agent = initialize_agent(
    tools,
    llm,
    agent=AgentType.REACT_DOCSTRING,  # Reasoning + Acting loop
    verbose=True,
    max_iterations=15,
    early_stopping_method='generate'
)

# Execution
result = agent.run("What is the population of France? Multiply by 2 and verify.")

# Behind the scenes:
# 1. LLM reasons: "I need to search for France's population"
# 2. LLM calls: web_search('France population')
# 3. Tool returns: "France population is 67 million"
# 4. LLM reasons: "Now multiply by 2"
# 5. LLM calls: calculator('67000000 * 2')
# 6. Tool returns: "134000000"
# 7. LLM outputs: "The answer is 134 million"
```

**Advantage**: Adaptive, uses tools dynamically, handles emergent problems.
**Disadvantage**: Unpredictable token usage, hallucination risks, latency.

### Pattern 3: Planning + Execution
**Use case**: Large projects, multi-stage orchestration, resource constraints.

```python
from langchain.agents import initialize_agent
from langchain.chains import LLMChain
from langchain.prompts import PromptTemplate

# Step 1: Planning (once, before execution)
planner = LLMChain(
    llm=llm,
    prompt=PromptTemplate(
        input_variables=['objective'],
        template="""Create a 5-step plan to achieve: {objective}

        Format:
        1. [Step 1]
        2. [Step 2]
        ... etc
        """
    )
)

# Step 2: Execution (follow the plan)
executor = initialize_agent(
    tools,
    llm,
    agent=AgentType.OPENAI_FUNCTIONS,
    verbose=True,
    max_iterations=15
)

objective = "Build a customer support chatbot for a SaaS product"
plan = planner.run(objective)
# Output:
# 1. Define chatbot scope and use cases
# 2. Gather customer support tickets
# 3. Extract Q&A pairs from tickets
# 4. Train embeddings on Q&A pairs
# 5. Deploy chatbot with RAG pipeline

# Execute each step
result = executor.run(f"Execute this plan: {plan}")
```

**Advantage**: Cost-efficient (plan once, execute multiple times), transparent (explicit steps).
**Disadvantage**: Less adaptive, requires manual plan intervention.

## LangGraph (Agentic Control Flow)

LangGraph is a state machine framework for agents. Unlike LangChain chains, LangGraph handles conditional branching, loops, and persistence.

```python
from langgraph.graph import StateGraph, START, END
from langgraph.checkpoint.memory import MemorySaver
from typing import TypedDict, Annotated

# Define agent state
class AgentState(TypedDict):
    messages: Annotated[list, 'Append-only message history']
    user_id: str
    session_id: str
    context: dict

# Node functions (each can make LLM calls, tool calls, etc.)
def query_node(state: AgentState) -> AgentState:
    """Process user query."""
    response = llm.invoke(state['messages'])
    state['messages'].append({'role': 'assistant', 'content': response})
    return state

def tool_node(state: AgentState) -> AgentState:
    """Execute tool calls from LLM response."""
    last_message = state['messages'][-1]
    # Parse tool_calls from last_message
    for tool_call in last_message['tool_calls']:
        result = execute_tool(tool_call['name'], tool_call['args'])
        state['messages'].append({'role': 'tool', 'content': result})
    return state

def decision_node(state: AgentState) -> str:
    """Decide: continue or finish?"""
    last_message = state['messages'][-1]
    if 'tool_calls' in last_message:
        return 'tool'  # Go to tool_node
    else:
        return END  # Done

# Build graph
workflow = StateGraph(AgentState)
workflow.add_node('query', query_node)
workflow.add_node('tool', tool_node)

workflow.add_edge(START, 'query')
workflow.add_conditional_edges('query', decision_node)
workflow.add_edge('tool', 'query')  # Loop back to query after tool execution

# Compile with persistence
checkpointer = MemorySaver()
app = workflow.compile(checkpointer=checkpointer)

# Execute
state = {
    'messages': [{'role': 'user', 'content': 'What is the weather?'}],
    'user_id': 'user123',
    'session_id': 'sess123',
    'context': {}
}

result = app.invoke(state, config={'configurable': {'thread_id': 'sess123'}})
```

**Key Features**:
- **Checkpointing**: Resume interrupted conversations (human-in-the-loop).
- **Conditional routing**: Branches based on agent state.
- **Memory persistence**: Thread-based conversation history.

## RAG (Retrieval-Augmented Generation) Pipelines

RAG is the pattern for grounding LLM responses in external knowledge. Prevents hallucination by retrieving relevant documents before generation.

### RAG Architecture

```
User Query
    ↓
1. Embedding (Text → Vector)
    ↓
2. Retrieve (Vector Similarity Search)
    ↓
3. Rerank (Score Relevance)
    ↓
4. Augment (Inject into Prompt)
    ↓
5. Generate (LLM Produces Response)
```

### Step 1: Data Ingestion & Chunking

```python
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain.document_loaders import PyPDFLoader, DirectoryLoader

# Load documents
loader = DirectoryLoader('./docs', glob='*.pdf', loader_cls=PyPDFLoader)
documents = loader.load()

# Chunking strategy (critical for RAG quality)
splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,          # Characters per chunk
    chunk_overlap=200,        # Overlap for context continuity
    separators=['\n\n', '\n', '.', ' ']  # Split on paragraphs first, then sentences
)

chunks = splitter.split_documents(documents)

# Chunks: List[Document]
# Each chunk has:
# - page_content: "The quick brown fox..."
# - metadata: {'source': 'doc.pdf', 'page': 5}
```

**Chunking Strategy Considerations**:
- **Chunk Size**: 500-2000 chars typical. Larger = more context, fewer chunks. Smaller = more precise, more chunks.
- **Overlap**: 200-500 chars prevents information split across chunks.
- **Separators**: Hierarchical (paragraphs → sentences → words) preserves semantic boundaries.

### Step 2: Embedding & Vector Storage

```python
from langchain.embeddings import OpenAIEmbeddings
from langchain.vectorstores import Pinecone
import pinecone

# Initialize embedding model
embeddings = OpenAIEmbeddings(model='text-embedding-3-large')

# Vector DB: Pinecone
pinecone.init(api_key=os.environ['PINECONE_API_KEY'])
index = pinecone.Index('documents')

# Store embeddings
vector_store = Pinecone.from_documents(
    chunks,
    embeddings,
    index_name='documents'
)

# OR PostgreSQL with pgvector
from langchain.vectorstores.pgvector import PGVector
connection_string = 'postgresql://user:pass@localhost/vectordb'
vector_store = PGVector.from_documents(
    chunks,
    embeddings,
    connection_string=connection_string,
    table_name='documents'
)
```

### Step 3: Retrieval & Reranking

```python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import CohereReranker
from langchain.retrievers.multi_query import MultiQueryRetriever

# Simple retrieval (top-k by cosine similarity)
retriever = vector_store.as_retriever(
    search_type='similarity',
    search_kwargs={'k': 5}  # Return top 5 chunks
)

# Reranking (re-score retrieved docs for relevance)
compressor = CohereReranker(model='rerank-english-v2.0', top_n=3)
compressed_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=retriever
)

# Multi-query retrieval (query diversification)
multi_query = MultiQueryRetriever.from_llm(
    retriever=retriever,
    llm=llm,
    prompt=MULTI_QUERY_PROMPT
)

# Execution
query = "How do I handle authentication in microservices?"
docs = compressed_retriever.get_relevant_documents(query)
# Returns: [Document(page_content='...'), Document(page_content='...'), ...]
```

### Step 4: Augmented Generation

```python
from langchain.chains import RetrievalQA
from langchain.prompts import PromptTemplate

# RAG prompt template
template = """Use the following context to answer the question.

Context:
{context}

Question: {question}

Answer:"""

prompt = PromptTemplate(
    input_variables=['context', 'question'],
    template=template
)

# RAG chain
rag_chain = RetrievalQA.from_chain_type(
    llm=llm,
    chain_type='stuff',  # Other: 'map_reduce', 'refine'
    retriever=compressed_retriever,
    chain_type_kwargs={'prompt': prompt},
    return_source_documents=True
)

# Execute
result = rag_chain({'query': 'How do I handle authentication in microservices?'})
# Output:
# {
#   'result': 'Use JWT tokens with refresh rotation...',
#   'source_documents': [Document(...), Document(...)]
# }
```

## Context Window Management

LLM context windows are finite. Every token costs money and latency.

### Token Counting & Optimization

```python
from tiktoken import encoding_for_model

def estimate_tokens(text, model='gpt-4'):
    """Count tokens in text."""
    encoding = encoding_for_model(model)
    return len(encoding.encode(text))

# Optimization strategies
class ContextManager:
    def __init__(self, max_tokens=8000):
        self.max_tokens = max_tokens
        self.token_count = 0

    def add_message(self, role: str, content: str) -> bool:
        """Add message if it fits in context window."""
        tokens = estimate_tokens(content)

        if self.token_count + tokens > self.max_tokens:
            # Evict oldest message
            self.evict_oldest()

        self.token_count += tokens
        return True

    def evict_oldest(self):
        """Remove oldest message to make room."""
        oldest = self.messages.pop(0)
        self.token_count -= estimate_tokens(oldest['content'])

# Usage
manager = ContextManager(max_tokens=8000)
manager.add_message('user', 'long query here')
manager.add_message('assistant', 'long response here')
```

## Prompt Engineering (Advanced Patterns)

### Chain-of-Thought (CoT)
**Principle**: Ask LLM to explain reasoning before answering.

```
Prompt:
"Let's think step by step. If Sally has 5 apples and gives 2 to Bob, how many does she have left?"

vs.

Standard prompt:
"Sally has 5 apples, gives 2 to Bob. How many left?"

Result: CoT improves accuracy on complex reasoning by 15-30%.
```

### Few-Shot Learning
**Principle**: Provide examples before the actual query.

```python
from langchain.prompts import FewShotPromptTemplate, PromptTemplate

examples = [
    {'input': 'The food was delicious and the staff was friendly', 'output': 'Positive'},
    {'input': 'The service was slow and the food was cold', 'output': 'Negative'},
    {'input': 'The ambiance was nice, but pricey', 'output': 'Mixed'}
]

example_template = PromptTemplate(
    input_variables=['input', 'output'],
    template="Input: {input}\nOutput: {output}"
)

few_shot_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_template,
    suffix="Input: {input}\nOutput:",
    input_variables=['input']
)

# Output prompt includes all examples + new input
```

### Structured Output (JSON Schema)
**Principle**: Force LLM to output structured JSON.

```python
from langchain.output_parsers import PydanticOutputParser
from pydantic import BaseModel

class Person(BaseModel):
    name: str
    age: int
    email: str

parser = PydanticOutputParser(pydantic_object=Person)

prompt_template = PromptTemplate(
    template="Extract person info from: {text}\n{format_instructions}",
    input_variables=['text'],
    partial_variables={'format_instructions': parser.get_format_instructions()}
)

# LLM response is automatically parsed into Person object
```

## Guardrails & Safety

### Input Validation

```python
from langchain.chains import GuardrailsChain
from langchain.prompts import PromptTemplate

class InputGuardrail:
    def __init__(self):
        self.blocked_keywords = ['execute', 'rm -rf', 'drop database']

    def check(self, user_input: str) -> bool:
        """Return False if input contains dangerous keywords."""
        for keyword in self.blocked_keywords:
            if keyword.lower() in user_input.lower():
                return False
        return True

# Usage
guardrail = InputGuardrail()
if not guardrail.check(user_input):
    raise ValueError("Input contains blocked content")
```

### Output Validation

```python
def validate_output(llm_output: str, constraints: dict) -> bool:
    """
    Validate LLM output against constraints.
    constraints = {
        'max_length': 200,
        'contains_citation': True,
        'no_profanity': True
    }
    """
    if len(llm_output) > constraints.get('max_length', float('inf')):
        return False

    if constraints.get('contains_citation') and '[' not in llm_output:
        return False

    return True
```

## Cost Optimization

```python
class CostOptimizer:
    # Token costs (2025 pricing)
    MODELS = {
        'gpt-4-turbo': {'input': 0.01, 'output': 0.03},
        'gpt-4': {'input': 0.03, 'output': 0.06},
        'gpt-3.5-turbo': {'input': 0.0005, 'output': 0.0015},
        'claude-3-opus': {'input': 0.015, 'output': 0.075},
        'claude-3-sonnet': {'input': 0.003, 'output': 0.015}
    }

    def estimate_cost(self, model: str, input_tokens: int, output_tokens: int) -> float:
        rates = self.MODELS[model]
        return (input_tokens * rates['input'] + output_tokens * rates['output']) / 1000

    def select_model(self, task: str, budget: float) -> str:
        """Select cheapest model that meets requirements."""
        if task == 'simple_classification':
            return 'gpt-3.5-turbo'  # Cheap, sufficient
        elif task == 'complex_reasoning':
            return 'gpt-4'  # Expensive but necessary
        elif task == 'long_context_rag':
            return 'claude-3-opus'  # Longest context window
        return 'gpt-4-turbo'  # Default balanced choice
```

## Output Format

```markdown
# 🤖 AI Agent Architecture Review

## I. Agent Pattern Assessment
- **Current Pattern**: [Linear / ReAct / Planning+Execution]
- **Recommendation**: [Pattern recommendation with rationale]

## II. RAG Pipeline Evaluation
- Chunking: Size [X] chars, Overlap [Y] chars
- Embedding Model: [OpenAI / Cohere / Local]
- Vector DB: [Pinecone / Weaviate / pgvector]
- Retrieval: [Cosine similarity / Hybrid search]
- Reranking: [Enabled / Disabled] - Recommend: [Enabled for production]

### RAG Quality Metrics
- Retrieval Precision@5: [X%]
- Answer Relevance: [Y%]
- Token Efficiency: [Z tokens/query]

## III. Context Management
- Max Context Window: [tokens]
- Average Query Token Count: [tokens]
- Headroom: [% of context window available]

## IV. Prompt Engineering Assessment
- [ ] Chain-of-Thought enabled for complex reasoning
- [ ] Few-shot examples provided (if needed)
- [ ] Structured output validation enabled
- [ ] Temperature tuned ([X] - 0=deterministic, 1=creative)

## V. Safety & Guardrails
- Input validation: [Implemented / Missing]
- Output validation: [Implemented / Missing]
- Rate limiting: [Implemented / Missing]
- Token limit enforcement: [Implemented / Missing]

## VI. Cost Analysis
- Est. Monthly Cost: $[X] at current usage
- Recommended Model: [Model] (balance quality/cost)
- Optimization Opportunities: [List any]

## VII. Skill Chaining
- Chain to `saas-architecture` for deployment patterns.
- Chain to `ml-pipeline` for training data pipelines.
```

---
> Source: [AniruddhaPKawarase/ai-agent-skills-toolkit](https://github.com/AniruddhaPKawarase/ai-agent-skills-toolkit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
