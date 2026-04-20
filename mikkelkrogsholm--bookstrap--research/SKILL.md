---
name: research
description: Source evaluation framework, web search strategies, and ingestion workflows for autonomous research. Includes reliability scoring, chunking strategies, entity extraction patterns, and quality thresholds. Use when this capability is needed.
metadata:
  author: mikkelkrogsholm
---

# Research and Source Ingestion

This skill provides comprehensive guidance for autonomous research execution, source evaluation, and corpus ingestion.

## Source Reliability Evaluation Framework

Evaluate every source using these criteria before ingestion. Reliability affects how confidently claims can be made.

### Reliability Tiers

| Tier | Description | Examples | Usage Guidelines |
|------|-------------|----------|------------------|
| **High** | Peer-reviewed, authoritative, primary sources | Academic journals, government archives, primary documents, expert monographs | Use for critical facts, can cite as sole source |
| **Medium** | Professional journalism, reputable secondary sources | Major newspapers, established magazines, well-sourced books, institutional reports | Use for context, prefer 2+ sources for key claims |
| **Low** | General reference, crowdsourced, unverified | Wikipedia, general encyclopedias, aggregator sites | Use for leads only, verify via higher-tier sources |
| **Very Low** | Personal blogs, opinion pieces, uncredited sources | Random blogs, forums, social media, promotional content | Avoid for factual claims, use only for cultural context if needed |

### Reliability Scoring Checklist

For each source, evaluate:

**Authority** (0-3 points):
- [ ] Author is subject matter expert (1 pt)
- [ ] Published by reputable institution (1 pt)
- [ ] Peer-reviewed or fact-checked (1 pt)

**Evidence** (0-3 points):
- [ ] Cites primary sources (1 pt)
- [ ] Includes references/bibliography (1 pt)
- [ ] Provides specific details, not generalizations (1 pt)

**Recency** (0-2 points):
- [ ] Published within relevant timeframe for topic (1 pt)
- [ ] Updated or confirmed still accurate (1 pt)

**Bias/Objectivity** (0-2 points):
- [ ] Acknowledges limitations or counterarguments (1 pt)
- [ ] Not promotional or heavily biased (1 pt)

**Total Score → Reliability Tier**:
- 8-10 points: **High**
- 5-7 points: **Medium**
- 2-4 points: **Low**
- 0-1 points: **Very Low**

### Source Type Definitions

**Primary Sources** (reliability: High):
- Original documents (letters, diaries, official records)
- Eyewitness accounts (interviews, memoirs from participants)
- Raw data (statistics, research datasets)
- Artifacts (photographs, objects, recordings)

**Secondary Sources** (reliability: Medium to High):
- Scholarly analysis of primary sources
- Historical or academic syntheses
- Biographies by credible historians
- Well-researched journalism

**Tertiary Sources** (reliability: Low):
- Encyclopedias (Wikipedia, Britannica)
- Textbooks (introductory overviews)
- Almanacs and fact books
- Bibliographies

**Web Sources** (reliability: Varies):
- Evaluate individually using checklist
- Domain matters: `.edu`, `.gov` often more reliable than `.com`
- Check "About" page for authorship and credentials
- Verify claims against higher-tier sources

## Web Search Strategies

### Strategy 1: Targeted Keyword Search

**When to use**: You know the specific fact, person, or event you need.

**Technique**:
```
"exact phrase" + broad context + qualifier

Examples:
"SOE Beaulieu training" + 1942 + protocol
"Lyon resistance network" + 1943 + structure
"wireless operator" + occupied France + procedure
```

**Query operators**:
- `"exact phrase"` — forces exact match
- `site:domain.com` — restrict to domain
- `filetype:pdf` — find PDFs (often academic papers)
- `-exclude` — remove unwanted terms
- `OR` — alternatives (capitalize for boolean)

### Strategy 2: Progressive Refinement

**When to use**: Broad topic, need to narrow down.

**Technique**:
1. Start broad: "SOE training"
2. Review top results for specific terms
3. Refine: "SOE training Beaulieu wireless"
4. Iterate until hitting primary sources or academic work

### Strategy 3: Reverse Citation Chase

**When to use**: Found one good source, need more.

**Technique**:
1. Find one high-quality source
2. Extract author names, key terms, referenced works
3. Search for those authors' other publications
4. Search for works that cite this source (Google Scholar: "Cited by")

### Strategy 4: Academic Database Search

**When to use**: Need scholarly rigor for nonfiction or historical fiction.

**Databases to use**:
- Google Scholar (free, broad coverage)
- JSTOR (subscription, humanities/social sciences)
- PubMed (free, medical/scientific)
- Archive.org (free, historical documents)

**Technique**:
- Use academic keywords (avoid colloquialisms)
- Filter by date range
- Sort by citation count for influential works

### Strategy 5: Primary Source Discovery

**When to use**: Fiction requiring historical accuracy, nonfiction requiring evidence.

**Resources**:
- National archives (e.g., UK National Archives, US National Archives)
- University special collections
- Digital humanities projects
- Museum databases

**Technique**:
- Search "[topic] primary sources"
- Search "[topic] archive collection"
- Look for digitized documents, oral histories

## Ingestion Workflow

Follow this workflow for each source:

### Step 1: Chunk

Break source into semantically coherent pieces using LLM-based chunking.

**Chunking strategy**: `semantic` (not fixed token windows)

**Process**:
1. LLM identifies natural breakpoints (topic shifts, scene changes, paragraph boundaries)
2. Max tokens per chunk: `1024` (configurable via `bookstrap.config.json`)
3. Overlap between chunks: `128` tokens (preserves context)

**Why semantic chunking?**
- Preserves meaning (doesn't split mid-thought)
- Better for retrieval (chunks are topically coherent)
- LLM can identify section headers, topic transitions

**Implementation**:
- Use host framework's LLM (Claude, Gemini, etc.)
- Prompt: "Identify natural breakpoints for chunking this document. Return byte offsets."

### Step 2: Embed

Generate vector embeddings for each chunk.

**Process**:
1. Send chunk text to embedding provider (configured in `bookstrap.config.json`)
2. Store embedding in `source.embedding` or `section.embedding` field
3. SurrealDB native vector type: `array<float>`

**Embedding providers** (via config):
- **Gemini**: `text-embedding-004` (768 dims)
- **OpenAI**: `text-embedding-3-small` (1536 dims)
- **Ollama**: `nomic-embed-text` (768 dims, local)
- **LM Studio**: Local embeddings (768 dims)

**Dimensions must match** config setting for vector similarity queries to work.

### Step 3: Extract

Use LLM to extract entities and relationships from each chunk.

**Entities to extract**:
- **Characters** (fiction): name, description, status (alive/dead)
- **Locations**: name, description, introduced (bool)
- **Events**: name, description, sequence, date (if available)
- **Concepts** (nonfiction): name, description

**Extraction prompt pattern**:
```
Analyze this text and extract:
1. People mentioned (name, role, description)
2. Locations (name, description)
3. Events (name, description, date if mentioned)
4. Key concepts or themes

For each entity, also identify:
- Relationships (who knows whom, what relates to what)
- Timeline information (when did this happen?)

Text:
[chunk content]
```

**Store entities**:
```surql
-- Example: Create character
CREATE character SET
  name = "Anna",
  description = "SOE wireless operator, recruited 1942",
  status = "alive",
  embedding = $embedding_vector;
```

### Step 4: Relate

Create graph relationships between entities.

**Relationship types**:
- `appears_in`: character → section
- `located_in`: section → location
- `cites`: section → source
- `supports`: source → concept
- `precedes`: event → event (chronological)
- `follows`: event → event (inverse of precedes)
- `knows`: character → character
- `related_to`: concept → concept

**Storage pattern**:
```surql
-- Link character to section
RELATE character:anna->appears_in->section:ch3_sec2;

-- Link section to cited source
RELATE section:ch3_sec2->cites->source:soe_manual_1942;

-- Link source to supported concept
RELATE source:soe_manual_1942->supports->concept:wireless_protocols;

-- Timeline ordering
RELATE event:training_begins->precedes->event:deployment;
```

**Why relationships matter**:
- Enable graph queries ("What do we know about Anna?")
- Enforce consistency (can't mention dead character as alive)
- Support citation tracking (every claim → source)

### Step 5: Quality Check

Verify ingestion quality before marking task complete.

**Thresholds** (reject source if fails):
- [ ] At least 1 entity extracted
- [ ] Embedding successfully generated (vector not null)
- [ ] Source reliability scored (not "unknown")
- [ ] Source metadata complete (title, URL, source_type, ingested_at)

**Validation queries**:
```surql
-- Check if entities were created
SELECT count() FROM character WHERE id IN (SELECT <-appears_in<-section<-cites<-source WHERE id = $source_id);

-- Check embedding exists
SELECT embedding FROM source WHERE id = $source_id AND embedding IS NOT NONE;
```

**If quality check fails**:
- Log warning with source ID
- Flag for manual review
- Do not mark knowledge_gap as resolved

## Ingestion Storage Pattern

Full ingestion creates these database records:

```surql
-- 1. Create source record
CREATE source SET
  title = "SOE Training Manual 1942",
  content = $full_text,
  embedding = $doc_embedding,
  url = "https://example.com/soe-manual",
  source_type = "primary",
  reliability = "high",
  ingested_at = time::now(),
  ingested_during = "research";  -- or "bootstrap", "writing"

-- 2. Create entities found in source
CREATE character SET
  name = "Anna",
  description = "SOE wireless operator",
  embedding = $entity_embedding,
  introduced_in = section:none;  -- will link when writing

-- 3. Create relationships
RELATE source:soe_manual->supports->concept:wireless_training;

-- 4. Update timeline if dates found
CREATE event SET
  name = "Beaulieu training begins",
  description = "Anna starts SOE wireless operator training",
  sequence = 5,
  date = "1942-08-15T00:00:00Z";

-- 5. Mark knowledge gap resolved
UPDATE knowledge_gap:gap_12 SET
  resolved = true,
  resolved_by = source:soe_manual;
```

## Provider Configuration

Research providers are configured in `bookstrap.config.json`:

### Web Search Providers

```json
{
  "research": {
    "provider": "tavily",  // or "brave", "serper", "google"
    "api_key_env": "TAVILY_API_KEY",
    "rate_limit": {
      "requests_per_minute": 10
    },
    "blocked_domains": ["example-spam.com"],
    "allowed_domains": [],  // if set, only fetch from these
    "max_sources_per_task": 5
  }
}
```

**Provider selection**:
- **Tavily**: Best for research-focused queries, returns high-quality sources
- **Brave**: Privacy-focused, good general search
- **Serper**: Google results via API
- **Google**: Direct Google API (requires custom search setup)

### Embedding Providers

```json
{
  "embeddings": {
    "provider": "gemini",  // or "openai", "ollama", "lmstudio"
    "model": "text-embedding-004",
    "dimensions": 768,
    "api_key_env": "GEMINI_API_KEY"
  }
}
```

**Dimensions must match** across all embeddings in a database. Cannot mix 768-dim and 1536-dim vectors.

## Research Task Execution Pattern

When executing a research task:

1. **Load task**: `SELECT * FROM knowledge_gap WHERE id = $task_id`
2. **Web search**: Use configured provider with task.question
3. **Evaluate sources**: Apply reliability framework to each result
4. **Select sources**: Pick top `max_sources_per_task` by reliability score
5. **Ingest each source**:
   - Fetch content
   - Chunk semantically
   - Generate embeddings
   - Extract entities
   - Create relationships
6. **Quality check**: Verify thresholds met
7. **Mark resolved**: `UPDATE knowledge_gap SET resolved=true, resolved_by=$source_id`
8. **Commit**: `git commit -m "[bookstrap] research: [task description]"`

## Example Research Workflow

```bash
# Task: Research "SOE wireless operator training protocols"

1. Web search query: "SOE wireless operator training" + Beaulieu + 1942 + protocol
2. Results:
   - nationalarchives.gov.uk/soe/training → High reliability (primary source)
   - wikipedia.org/SOE → Low reliability (tertiary, use for leads only)
   - soe-history-blog.com → Very Low (personal blog, skip)

3. Select: nationalarchives.gov.uk document
4. Fetch content (text extraction from HTML/PDF)
5. Chunk: LLM finds 12 semantic chunks
6. Embed: Generate 768-dim vectors via Gemini
7. Extract:
   - Event: "Wireless training begins at Beaulieu, August 1942"
   - Concept: "Morse code proficiency required 20 WPM"
   - Concept: "Encryption protocols for field operations"
8. Relate:
   - RELATE source->supports->concept:morse_proficiency
   - CREATE event SET name="Training begins", date="1942-08-01"
9. Quality check: ✓ 3 entities, ✓ embedding, ✓ reliability=high
10. Mark resolved: UPDATE knowledge_gap:gap_12 SET resolved=true
11. Commit: git commit -m "[bookstrap] research: SOE wireless training protocols"
```

## Handling Research Failures

**If no sources found**:
- Log failure reason
- Leave knowledge_gap unresolved
- Report to user: "Could not find reliable sources for [topic]"
- Suggest: broaden search, try alternative keywords, or mark as low-priority

**If only low-reliability sources found**:
- Ingest with `reliability = "low"`
- Flag for user review
- Note in commit message: "Low-reliability sources only"
- Consider marking gap as "partially resolved"

**If ingestion fails**:
- Check URL accessibility
- Verify API keys configured
- Check rate limits
- Retry with backoff
- If persistent failure: skip source, try next result

## Output and Reporting

After completing a research task:

```markdown
## Research Task Complete: [topic]

**Sources ingested**: [count]
- High reliability: [count]
- Medium reliability: [count]

**Entities extracted**:
- Characters: [count]
- Locations: [count]
- Events: [count]
- Concepts: [count]

**Knowledge gap resolved**: [gap ID]

**Next steps**:
- Continue with next research task
- OR return to writing (if all gaps resolved)
```

## Quality Thresholds

Reject sources that fail these criteria:

| Criterion | Threshold | Action if Failed |
|-----------|-----------|------------------|
| Reliability score | ≥ 2/10 | Skip source, try next |
| Entity extraction | ≥ 1 entity | Retry extraction, else skip |
| Embedding generation | Non-null vector | Retry, check API, else skip |
| Content length | ≥ 100 chars | Skip (too short to be useful) |
| Duplicate check | Not already ingested | Skip (avoid duplicate sources) |

## Supporting Files

This skill references additional resources:

- `web-search.md` — Detailed search strategies by genre/topic
- `extraction.md` — Entity extraction patterns and prompts
- `scripts/chunk.py` — Semantic chunking implementation
- `scripts/embed.py` — Embedding generation utilities

Load these files when needed for specific sub-tasks.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikkelkrogsholm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
