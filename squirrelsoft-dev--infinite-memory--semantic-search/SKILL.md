---
name: semantic-search
description: Automatically search indexed code when user asks questions about the codebase. Detects code-related queries and uses semantic memory to find relevant files. Activates for questions like "how does X work", "where is Y", "show me Z implementation". Use when this capability is needed.
metadata:
  author: squirrelsoft-dev
---

# Infinite Memory Semantic Search Skill

Auto-search code using semantic memory when user asks codebase questions.

## Activation Logic

This skill activates when **ALL** of these conditions are met:

1. **User question contains code-related keywords:**
   - Questions: "how does", "where is", "show me", "find", "what is", "explain"
   - Code concepts: "function", "class", "API", "route", "model", "database", "authentication", "configuration", etc.
   - Patterns: "decorator", "middleware", "handler", "service", "controller"

2. **Current project appears to be indexed:**
   - Check for Pixeltable database in ~/.pixeltable/
   - Or attempt a test `query_memory` call
   - If not indexed, **do not activate** (let setup-assistant skill handle it)

3. **Question is about THIS codebase:**
   - Not about external libraries ("how does React work?")
   - Not general programming questions ("how do I write a loop?")
   - About the current project's implementation

## Execution Steps

### 1. Detect Query Intent

Extract the search query from user's natural language question:

**Examples:**
- "How does authentication work?" → query: "authentication work"
- "Where is the User model defined?" → query: "User model definition"
- "Show me error handling" → query: "error handling"
- "Find all API endpoints" → query: "API endpoints"
- "How is the database connection configured?" → query: "database connection configuration"

### 2. Call query_memory MCP Tool

Adjust parameters based on query type:

**Specific queries** (class names, function names):
```
query_memory(
    query="UserModel class definition",
    max_results=5,
    similarity_threshold=0.8  # Higher threshold for precision
)
```

**Conceptual queries** (how does X work):
```
query_memory(
    query="authentication work flow",
    max_results=10,
    similarity_threshold=0.7  # Normal threshold
)
```

**Exploratory queries** (find all X, show me Y):
```
query_memory(
    query="API endpoints routes",
    max_results=15,
    similarity_threshold=0.6  # Lower threshold, more results
)
```

### 3. Format Results **Adaptively**

**Specific Queries** (user looking for exact thing):
- Show detailed code snippets
- Include function signatures
- Show context (surrounding code)
- Limit to top 3-5 most relevant

**Conceptual Queries** (user wants to understand):
- Show file paths with brief descriptions
- Explain relationships between files
- Group related results
- Can show more results (5-10)

**Broad Queries** (exploration/discovery):
- List file paths with one-line descriptions
- Categorize by type (models, routes, tests, etc.)
- Show more results (10-15)
- Offer to dive deeper into specific areas

### 4. Answer User's Question

**Don't just dump search results** - use them to answer the question:

**Good Answer Pattern:**
```
[Direct answer to user's question]

[Explanation based on retrieved code]

[Reference specific files with similarity scores as percentages]

[Offer to explain further or dive deeper]
```

**Example:**

User: "How does authentication work?"

Response:
```
Authentication in this project uses JWT tokens:

1. **Login Flow** (auth/login.py, 92% match):
   - User submits credentials to /api/login
   - Validates against database
   - Generates JWT token with user ID and expiration
   - Returns token in HTTP-only cookie

2. **Request Authentication** (middleware/auth.py, 89% match):
   - Middleware intercepts protected routes
   - Extracts JWT from cookie
   - Verifies signature and expiration
   - Adds user object to request context

3. **Session Management** (auth/session.py, 85% match):
   - Stores active sessions in Redis
   - Implements token refresh mechanism
   - 1-hour token expiry, 7-day refresh window

The main entry point is the authenticate_request() middleware function
that runs on every protected route.

Would you like me to explain any of these components in more detail?
```

## Anti-Patterns (When NOT to Activate)

**DO NOT activate for:**

❌ **General programming questions:**
- "How do I write a for loop in Python?"
- "What's the difference between async and sync?"
- "How does recursion work?"

❌ **External library questions:**
- "How does React's useState hook work?"
- "How do I use FastAPI's dependency injection?"
- "What are Django's ORM features?"

❌ **Non-code questions:**
- "What should we implement next?"
- "Is this architecture scalable?"
- "Should we use microservices?"

❌ **Project is not indexed:**
- Let the `setup-assistant` skill handle this case
- Don't activate and then fail - poor UX

## Fallback Behavior

### If No Results Found (total_results: 0)

Inform user and suggest alternatives:

```
I couldn't find exact matches for "[query]" in the indexed code.

This could mean:
- The feature uses different terminology
- The code hasn't been indexed yet (run /status to check)
- Try broader search terms

Would you like me to:
- Search for related concepts? (suggest alternative query)
- Check indexing status?
- Use traditional grep search instead?
```

### If Partial Results (1-2 results with low scores <0.75)

```
I found some potentially related code:

[Show results with caveat]

These matches have lower confidence scores. The feature might use
different terminology, or these could be tangentially related.

Would you like me to:
- Try a broader search?
- Search for alternative terms?
```

## Similarity Score Guidelines

When presenting scores to users, use percentages and context:

- **90-100%**: "exact match" or "highly relevant"
- **80-89%**: "strong match" or "very relevant"
- **70-79%**: "relevant match" or "related"
- **60-69%**: "possibly related" (use cautiously)
- **<60%**: Generally don't show (unless exploratory query with no better results)

## Integration with Other Features

**Work seamlessly with:**
- `/search` command: User's explicit search takes precedence
- `setup-assistant` skill: If project not indexed, defer to setup
- Manual grep/read: Combine semantic search with traditional tools when needed

## Performance Considerations

- Keep searches fast: <200ms for most queries
- Don't overwhelm user with too many results
- Cache recent queries if possible (future optimization)
- Bail out early if project clearly not indexed

## Example Activation Scenarios

### ✅ Should Activate

1. "How does authentication work in this app?"
2. "Where is the database connection configured?"
3. "Show me all API endpoints"
4. "Find the User model"
5. "What's the error handling pattern?"
6. "Where are tests for the login feature?"

### ❌ Should NOT Activate

1. "How do I write async code?" (general question)
2. "What does FastAPI's Depends do?" (external library)
3. "Should we refactor this?" (architectural question)
4. "What's our testing strategy?" (process question)
5. [Project not indexed] + any code question (defer to setup-assistant)

## Success Criteria

This skill is successful when:
- User's code questions get answered automatically
- Relevant code is surfaced without manual search
- User doesn't need to remember to use `/search` command
- Feels "invisible" - just works naturally
- Doesn't activate on non-code questions (low false positive rate)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/squirrelsoft-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
