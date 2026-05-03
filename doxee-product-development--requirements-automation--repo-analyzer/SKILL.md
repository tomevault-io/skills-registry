---
name: repo-analyzer
description: Query-driven repository analyzer using progressive disclosure (tree-first, selective file reading). Supports (1) LOCAL mode for cloned repos, (2) API mode for remote Bitbucket repos without cloning. Uses multi-model cascade (haiku for scanning, sonnet for analysis, opus for synthesis). Answers specific questions ('What does this integrate with?') rather than exhaustive analysis. Includes tier-based depth control, monorepo detection, constitution output, data flow diagrams, and feature catalogs. Use when analyzing repos for codebase mapping or answering specific questions about repo architecture. Use when this capability is needed.
metadata:
  author: doxee-product-development
---

# Repo Analyzer

Analyze a repository and produce a structured profile for codebase mapping.

## Input

**Arguments**: `$ARGUMENTS`

Parse rules:
- `<path>` — LOCAL mode: path to cloned repo directory
- `<repo-slug>` — API mode: Bitbucket repo slug (detected when arg is not a filesystem path)
- `<repo-slug> --tier 1|2|3|4` — Depth control (default: tier 2)
- `<repo-slug> --model haiku|sonnet|opus` — Model selection (default: auto-select by tier)
- `<repo-slug> --query "question"` — Query-driven analysis (e.g., "What does this integrate with?")
- `<repo-slug> --workspace <slug>` — API mode workspace (default: `infinicarepositories`)
- `<repo-slug> --resume` — Resume from cached state (if exists)
- `<repo-slug> --clear-cache` — Clear cached state and start fresh

Mode detection:
- If arg starts with `/` or `./` or `~` → LOCAL mode
- Otherwise → API mode (treat as Bitbucket repo slug)

Cache location:
- `research/context-enrichment/.cache/{repo-slug}/state.json` — Analysis state
- `research/context-enrichment/.cache/{repo-slug}/files/` — Cached file reads
- Cache is automatically updated after each step for crash recovery

Model auto-selection:
- Tier 4 → haiku (fast catalog)
- Tier 3 → haiku (light scan)
- Tier 2 → sonnet (standard analysis)
- Tier 1 → sonnet (deep analysis, constitution/diagrams generated with sonnet)

## Workflow

### Step 0: Cache Management (Resumability)

**ALWAYS check cache first before starting analysis.**

Cache structure:
```
research/context-enrichment/.cache/{repo-slug}/
├── state.json              # Current analysis state
├── files/                  # Cached file reads
│   ├── README.md
│   ├── package.json
│   ├── application.yml
│   └── ...
└── profile-partial.md      # Incremental profile (written as analysis progresses)
```

**state.json format:**
```json
{
  "repo": "dp.service.preferences",
  "workspace": "infinicarepositories",
  "tier": 2,
  "model": "sonnet",
  "query": null,
  "started": "2026-02-09T10:30:00Z",
  "updated": "2026-02-09T10:35:00Z",
  "commit_hash": "abc123...",
  "commit_date": "2026-02-08",
  "progress": {
    "step": "phase2_config_analysis",
    "completed_steps": ["resolve_access", "phase1_tree", "phase1_high_signal"],
    "phase1_files_read": ["README.md", "package.json"],
    "phase2_files_read": ["application.yml"],
    "total_files_read": 3
  },
  "metadata": {
    "language": "java",
    "framework": "Spring Boot",
    "build_system": "maven",
    "is_monorepo": false
  },
  "findings": {
    "dependencies": ["spring-boot-starter-web", "kafka-clients", "..."],
    "integration_points": ["http://dp.service.settings", "kafka://preferences.events"],
    "api_endpoints_count": 12,
    "entities_count": 5
  }
}
```

**Cache operations:**

1. **Check for existing cache:**
   ```
   IF research/context-enrichment/.cache/{repo-slug}/state.json exists:
     - Read state.json
     - Check if analysis is complete (progress.step == "complete")
     - If complete: Ask user if they want to reuse or restart
     - If incomplete: Resume from progress.step
   ```

2. **Initialize cache (if new or --clear-cache):**
   ```
   - Create directory: research/context-enrichment/.cache/{repo-slug}/
   - Create state.json with initial state (step: "init")
   - Create files/ subdirectory for caching reads
   ```

3. **Update cache after each step:**
   ```
   - After each completed step, update state.json:
     * Add step to completed_steps array
     * Update progress.step to next step
     * Update metadata and findings incrementally
     * Update updated timestamp
   - Write profile-partial.md with current profile content
   ```

4. **Cache file reads:**
   ```
   - Before reading a file via BB API or Read tool:
     * Check if research/context-enrichment/.cache/{repo-slug}/files/{filepath} exists
     * If exists: Use cached content (no API call)
     * If not: Read file, then write to cache
   ```

**Resume workflow:**
```
IF --resume flag OR cache exists with incomplete state:
  1. Load state.json
  2. Report: "Resuming {repo} analysis from step: {progress.step} ({X} files read)"
  3. Load all cached file reads from files/ directory
  4. Load profile-partial.md if exists
  5. Skip to progress.step in workflow
  6. Continue analysis from that step

ELSE:
  1. Initialize new cache
  2. Start from Step 1
```

**Clear cache workflow:**
```
IF --clear-cache flag:
  1. Remove research/context-enrichment/.cache/{repo-slug}/ directory
  2. Report: "Cache cleared for {repo}"
  3. Start fresh analysis
```

### Step 1: Load References

Read these files (relative to this skill directory):
- `references/analysis-pipeline.md` — Progressive disclosure pipeline, detection heuristics, output format
- `references/api-analysis-guide.md` — BB API patterns (API mode only)
- `references/cache-management.md` — Cache structure, operations, resumption scenarios

**Cache update:** Write to state.json: `progress.step = "references_loaded"`

### Step 2: Resolve Repository Access (Progressive Disclosure Start)

**Check cache first:** If cache exists and `progress.step` is past "resolve_access", skip this step and use cached metadata.

**LOCAL mode:**
1. Verify repo path exists and is readable
2. Detect metadata: remote URL, branch, last commit, file count, size
3. Get directory tree: Use `ls -R` or `find` for structure (not full Glob yet)
4. **Cache update:** Write metadata to state.json, set `progress.step = "tree_discovery"`
5. Proceed to Step 3

**API mode (PROGRESSIVE DISCLOSURE):**
1. **Phase 1: Commit Hash Resolution**
   ```
   bb_get /repositories/{workspace}/{repo}/commits?pagelen=1
   → Extract values[0].hash, date, author
   → Cache: Write to state.json (commit_hash, commit_date)
   ```

2. **Phase 2: Tree-First Discovery**
   ```
   bb_get /repositories/{workspace}/{repo}/src/{hash}/
   → Extract file/directory names (NO file reads yet)
   → Detect build system from file names only
   → Cache: Write tree listing to .cache/{repo}/files/.tree-root.json
   → Cache: Update state.json metadata (build_system, is_monorepo)
   ```

3. Report to user:
   ```
   Repository: {repo-slug} (API mode, tier {N}, model: {model})
   Workspace: {workspace}
   Commit: {hash} ({date})
   Query: {query if provided, else "standard analysis"}
   Cache: {X} files already cached
   Tree-first analysis starting...
   ```

4. **Cache update:** Set `progress.step = "phase1_high_signal"`

5. **Phase 3: Selective File Reading** (query-driven, not exhaustive)
   - If `--query` provided: Answer that specific question using targeted file reads
   - Otherwise: Run tier-appropriate analysis (see Step 4 for file read targets)

### Step 3: Monorepo Detection

**Check cache:** If `state.json.metadata.is_monorepo` is already set, skip detection and use cached value.

After detecting the build system in Stage 1, check for monorepo structure:

**Maven monorepo:** Read `pom.xml`, look for `<modules>` section → parse module list
**pnpm workspace:** Check for `pnpm-workspace.yaml` → parse `packages` list
**npm workspace:** Read `package.json`, check for `"workspaces"` field → parse workspace list

**Cache file reads:**
- Before reading pom.xml: Check `.cache/{repo}/files/pom.xml`
- After reading: Write to `.cache/{repo}/files/pom.xml`
- Update state.json: `metadata.is_monorepo = true/false`, `metadata.modules = [...]`

When monorepo detected:
- Generate parent profile with module overview
- Run pipeline independently per module (each module gets its own cache state)
- Each module gets its own section in the profile
- Generate inter-module dependency diagram

**Cache update:** Set `progress.step = "phase1_complete"`

### Step 4: Execute Progressive Analysis (Query-Driven)

**Check cache:** Resume from `progress.step`. If already completed phases, skip to next incomplete phase.

**DO NOT run full 6-stage pipeline.** Use progressive disclosure instead.

**PHASE 1: Tree + High-Signal Files** (always run, minimal reads)
**Cache checkpoint:** `progress.step = "phase1_high_signal"`
Query: "What is this repo and what are its dependencies?"
- Tree: Root directory listing (already done in Step 2, cached)
- **Cached read:** README.md (check `.cache/{repo}/files/README.md` first)
- **Cached read:** CHANGELOG.md (if present)
- **Cached read:** Build file (package.json / pom.xml)
- Extract: Language, framework, build system, top dependencies
- **Cache updates:**
  - Write each file to `.cache/{repo}/files/{filename}`
  - Update `state.json.progress.phase1_files_read = [...]`
  - Update `state.json.metadata` with extracted info
  - Update `state.json.findings.dependencies = [...]`
  - Set `progress.step = "phase1_complete"`
  - Write `profile-partial.md` with meta header + summary + tech stack sections

**PHASE 2: Tier-Specific Targeted Reads** (query-driven, selective only)
**Cache checkpoint:** `progress.step = "phase2_targeted"`

| Tier | File Read Target | Max Files | Queries Answered |
|------|-----------------|-----------|------------------|
| **Tier 4** | Tree only | **0** | "What is this?" (from tree + repo name) |
| **Tier 3** | + README + build file | **1-3** | "What is the purpose and tech stack?" |
| **Tier 2** | + configs (application.yml, docker-compose.yml, openapi.yaml) | **3-7** | + "What does this integrate with?" |
| **Tier 1** | + helm/values.yaml + sample entities/controllers | **5-10** | + "What APIs/data does this expose?" + constitution/diagrams |

**Query-Driven File Selection (with caching):**
- Query "What does this integrate with?"
  → **Cached read:** application.yml, docker-compose.yml
  → Parse for URLs/topics
  → **Cache update:** Write files, update `findings.integration_points`, update profile-partial.md
  → Set `progress.step = "phase2_integrations_complete"`

- Query "What APIs does this expose?"
  → Check tree (cached) for openapi.yaml or src/schemas/
  → **Cached read:** 1-2 sample OpenAPI specs (not all)
  → **Cache update:** Write files, update `findings.api_endpoints_count`, update profile-partial.md
  → Set `progress.step = "phase2_apis_complete"`

- Query "What data does this own?"
  → Check tree (cached) for entity/ or migrations/
  → **Cached read:** 1-2 sample entity/migration files
  → **Cache update:** Write files, update `findings.entities_count`, update profile-partial.md
  → Set `progress.step = "phase2_data_complete"`

- Query "What UI routes?"
  → Check tree (cached) for src/router/ or src/routes/
  → **Cached read:** Router index file only
  → **Cache update:** Write file, update `findings.ui_routes_count`, update profile-partial.md
  → Set `progress.step = "phase2_routes_complete"`

**API mode selective reading:**
- Directory listing: `bb_get /repositories/{ws}/{repo}/src/{hash}/{dir}/` (for tree queries)
- File content: `bb_get /repositories/{ws}/{repo}/src/{hash}/{filepath}` (only for targeted files)
- **NEVER** scan entire controller/ or entity/ directories — tree list only, read samples if Tier 1

**Output depth control:**

| Component | Tier 1 | Tier 2 | Tier 3 | Tier 4 |
|-----------|--------|--------|--------|--------|
| Meta header | Yes | Yes | Yes | Yes |
| Summary | Full | Full | Full | 1 line |
| Tech Stack | Full | Full | Basic | Language only |
| Key Dependencies | Top 15 | Top 10 | Top 5 | — |
| API Surface | Count + samples | Count only | — | — |
| Data Models | Count + key entities | Count only | — | — |
| Integration Points | Full (from configs) | Full (from configs) | — | — |
| Configuration | Key configs | Key configs | Basic | — |
| Module Mapping | Full | Full | Best guess | — |
| **Constitution** | **Full** | — | — | — |
| **Data Flow Diagram** | **Full** | — | — | — |
| **Feature Catalog** | **Full** | — | — | — |

See `references/analysis-pipeline.md` for progressive disclosure patterns per stage.

### Step 5: Generate Profile

**Check cache:** If `profile-partial.md` exists and analysis is complete, finalize it.

**Incremental profile writing:**
- Throughout Steps 2-4, continuously update `profile-partial.md` with completed sections
- Each cache update appends/updates relevant profile sections
- If interrupted, profile-partial.md contains all completed sections

**Finalization:**
1. Read `profile-partial.md` (contains all incremental progress)
2. Generate any remaining sections based on tier:
   - **Tier 1:** Add constitution, data flow diagram, feature catalog (if not yet added)
   - **Tier 2+:** Finalize integration points, configuration sections
3. Write final profile to: `research/context-enrichment/repo-profiles/{repo-name}.md`
4. **Cache update:**
   - Copy `profile-partial.md` → `{repo-name}.md`
   - Set `progress.step = "complete"`
   - Set `state.json.completed = true`
   - Set `state.json.completed_at = {timestamp}`
5. Keep cache for future resumption/reuse (do NOT delete unless --clear-cache)

Profile format follows templates in `references/analysis-pipeline.md`:
- Section 10: Standard profile (all tiers)
- Section 11: Constitution template (tier 1 only)
- Section 12: Data flow diagram template (tier 1 only)
- Section 13: Feature catalog template (tier 1 only)
- Section 14: Monorepo handling

### Step 6: Report Summary

```
Repo: {name} (tier {N}, {mode} mode)
Stack: {language} / {framework} / {build-tool}
APIs: {N} endpoints discovered
Models: {N} entities found
Integrations: {N} points mapped
Files read: {total_files_read} (cached: {cached_count})
Analysis time: {duration} (resumed: {yes/no})
Module: {platform-module-alignment}
Profile: research/context-enrichment/repo-profiles/{repo-name}.md
Cache: research/context-enrichment/.cache/{repo-slug}/ (preserved for future runs)
```

**Cache preservation:**
- Cache is NOT deleted after successful completion
- Allows instant resumption if user wants to regenerate with different tier/query
- Use `--clear-cache` to force fresh analysis

## Error Handling

**All errors preserve cache state for resumption.**

- **Path not found (local mode)**: Report and stop — ask user to verify path
  - Cache: Preserve state, mark as `error: "path_not_found"`

- **Repo not found (API mode)**: Report 404 — check slug spelling and workspace
  - Cache: Preserve state, mark as `error: "repo_not_found"`

- **Empty repo / no commits**: Create minimal profile noting empty state
  - Cache: Complete normally, mark `metadata.empty = true`

- **Branch name with slashes**: Use commit hash instead (see api-analysis-guide.md)
  - Cache: Store resolved commit hash

- **No recognizable build system**: Attempt language detection from file extensions
  - Cache: Store `metadata.build_system = "unknown"`

- **No API specs found**: Scan code for route annotations; note if no endpoints found
  - Cache: Store `findings.api_endpoints_count = 0`

- **Large repo (>10K files)**: Use sampling — top-level structure and key directories only
  - Cache: Mark `metadata.sampling = true`

- **API rate limits**: Reduce parallel calls, retry with backoff
  - Cache: Preserve all reads completed so far, mark `error: "rate_limited"`
  - User can resume with `--resume` after waiting

- **Interrupted execution (timeout/credits exhausted)**:
  - Cache automatically preserves progress
  - Report: "Analysis interrupted at step: {progress.step}. Resume with --resume flag."
  - Cache contains: All file reads, partial profile, metadata extracted so far
  - Next run with `--resume` picks up exactly where it left off

## References

- **Cache management, resumption, file deduplication**: See [references/cache-management.md](references/cache-management.md) ⭐ CRITICAL
- **Progressive disclosure pipeline, heuristics, output format**: See [references/analysis-pipeline.md](references/analysis-pipeline.md)
- **BB API patterns for remote analysis**: See [references/api-analysis-guide.md](references/api-analysis-guide.md)
- **Platform module mapping**: Cross-reference with `context/01-platform-architecture.md`

## Cache Location

Cache is stored at: `research/context-enrichment/.cache/{repo-slug}/`

**Recommended .gitignore entry:**
```
# Repo analyzer cache (can be regenerated)
research/context-enrichment/.cache/
```

Cache can be safely deleted — all analysis can be regenerated from Bitbucket API.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doxee-product-development) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
