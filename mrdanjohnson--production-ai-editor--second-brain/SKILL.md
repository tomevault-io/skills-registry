---
name: second-brain
description: Interface with Dan's Second Brain - a personal knowledge management system with semantic search, memory storage, and AI-powered insights. Use when Dan wants to store information, retrieve past memories, search his knowledge base, analyze patterns in his data, or when working on tasks that could benefit from his accumulated knowledge. Also use proactively to capture insights, decisions, and important context from conversations. Use when this capability is needed.
metadata:
  author: mrdanjohnson
---

# Second Brain Skill

This skill provides seamless integration with Dan's personal Second Brain system - a semantic knowledge base that stores memories, notes, and insights with AI-powered search and retrieval.

## API Base URL
```
http://192.168.1.91:3100/api
```

## Core Capabilities

### 1. Memory Storage
Store any information Dan wants to remember:
- Work decisions and rationale
- Meeting notes and action items
- Learning insights and book summaries
- Project context and status
- Personal goals and progress
- Conversations and decisions

### 2. Memory Retrieval
Search Dan's knowledge base using:
- **Semantic search** - natural language queries ("what did I learn about X?")
- **Tag-based filtering** - by category (Work, Personal, Learning, etc.)
- **Date filtering** - memories from specific time periods
- **Exact matches** - specific terms or phrases

### 3. Analytics & Insights
Access aggregated data:
- Memory statistics (total, by category, by time period)
- Due date tracking for action items
- Category distribution
- Search history patterns

## Authentication

All API calls require a Bearer token. Use Dan's token:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VySWQiOiIyOWI3OGZiOC0yZDI3LTRjMmEtOTQwNS04YjE0ZDA5YzE0MDYiLCJpYXQiOjE3NzA3Mzk3NjUsImV4cCI6MTc3MTM0NDU2NX0.Hnf8ZcYRGB3OMsk_tb4BulGC1xo8SegfKPaGrwGN48k
```

## Key API Endpoints

### Memories
- `GET /memories` - List all memories (paginated)
- `POST /memories` - Create new memory
- `GET /memories/:id` - Get specific memory
- `PUT /memories/:id` - Update memory
- `DELETE /memories/:id` - Delete memory

### Search
- `POST /search/semantic` - AI-powered semantic search

### Analytics
- `GET /analytics/summary` - Dashboard stats
- `GET /analytics/duedates` - Due date breakdown
- `GET /analytics/timeline` - Memory timeline

## Memory Structure

When storing memories, include:
```json
{
  "content": "The actual memory text",
  "category": "Work|Personal|Learning|Health|Finance|Ideas|Notes",
  "tags": ["relevant", "tags", "for", "filtering"],
  "source": "slack|email|meeting|thought|reading|api",
  "sourceId": "optional-external-reference-id",
  "memoryDate": "2026-02-10T10:00:00Z",  // When the memory occurred
  "dueDate": "2026-02-15T17:00:00Z"      // If there's an action item
}
```

## When to Use This Skill

### Proactive Storage (High Value)
Capture information automatically during conversations:
- **Decisions made** - "Dan decided to use X approach"
- **Action items** - Tasks Dan commits to with deadlines
- **Insights discovered** - Aha moments or learnings
- **Context established** - Background info for future reference
- **Preferences expressed** - "Dan prefers Y over Z"
- **Goals mentioned** - Objectives Dan is working toward

### Reactive Retrieval
When Dan asks:
- "What did I say about...?"
- "Do I have any notes on...?"
- "What was my decision regarding...?"
- "What are my action items?"
- "What did I learn from...?"

### Analysis & Synthesis
- Summarize themes across memories
- Identify patterns in Dan's work/life
- Track progress toward goals
- Surface forgotten insights relevant to current context

## Best Practices

### Store Rich Context
Don't just store facts - store:
- **Why** - reasoning and rationale
- **When** - temporal context
- **Who** - people involved
- **Next steps** - what comes after

### Use Consistent Tags
Common tag patterns:
- Project names: `project-x`, `website-redesign`
- People: `john-smith`, `team-alpha`
- Topics: `ai`, `marketing`, `architecture`
- Status: `decided`, `pending`, `blocked`, `completed`
- Type: `decision`, `insight`, `question`, `task`

### Set Due Dates for Action Items
When storing tasks or commitments, always include `dueDate` so they appear in:
- Dashboard overdue counts
- Due date analytics
- Future "what's on my plate" queries

### Search Strategies
- **Broad**: "What do I know about AI?" → Semantic search
- **Specific**: "Show me my notes from last week's meeting" → Date + tag filter
- **Urgent**: "What do I need to do this week?" → Due date search
- **Related**: "What other decisions did I make about this?" → Category + semantic

## Helper Scripts

Use the scripts in `./scripts/` for common operations:
- `store_memory.py` - Store a memory with proper formatting
- `search_memories.py` - Execute semantic or filtered searches
- `get_summary.py` - Retrieve analytics summary
- `karakeep_import.py` - Import bookmarks from Karakeep with AI summarization

### Karakeep Integration

Import your Karakeep bookmarks with AI-powered summarization:

```bash
# Import new bookmarks only
python scripts/karakeep_import.py

# Import specific number
python scripts/karakeep_import.py --limit 20

# Re-import all (including previously processed)
python scripts/karakeep_import.py --all

# Preview without storing
python scripts/karakeep_import.py --dry-run
```

**What it does:**
1. Reads bookmarks from Karakeep's SQLite database
2. Extracts content (HTML, description, or summary)
3. Uses OpenAI to summarize with context about Dan's interests
4. Stores enriched memory in Second Brain with:
   - AI-generated summary
   - Relevance explanation
   - Suggested category and tags
   - Priority assessment
   - Original URL preserved

## Important Notes

- Memories are automatically embedded using OpenAI for semantic search
- The system categorizes and tags content automatically if not specified
- Due dates appear in the dashboard widget
- All memories include timestamps and are chronologically searchable
- The API returns structured content (AI-extracted tags, summary, entities)

## Example Workflows

### Capturing a Meeting
```python
# Store meeting notes with action items
POST /memories
{
  "content": "Meeting with Sarah about Q1 roadmap. Decided to prioritize mobile app over web redesign. Action: Sarah will send wireframes by Friday.",
  "category": "Work",
  "tags": ["meeting", "sarah", "roadmap", "mobile-app", "decision"],
  "source": "meeting",
  "dueDate": "2026-02-14T17:00:00Z"
}
```

### Finding Related Knowledge
```python
# Search for relevant past insights
POST /search/semantic
{
  "query": "decisions about mobile app prioritization",
  "limit": 10
}
```

### Checking Action Items
```python
# Get analytics to see overdue items
GET /analytics/duedates
```

---

## Reference Documentation

- **API Schema**: See [references/api-schema.md](references/api-schema.md)
- **Search Patterns**: See [references/search-patterns.md](references/search-patterns.md)
- **Storage Patterns**: See [references/storage-patterns.md](references/storage-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrdanjohnson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
