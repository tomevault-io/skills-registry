---
name: custom-workers
description: Create and run custom background analysis workers with composable phases. Use when you need automated code analysis, security scanning, pattern learning, or API documentation generation. Use when this capability is needed.
metadata:
  author: ruvnet
---

# Custom Workers

Build composable background analysis workers with 24 phase executors and 6 presets.

## Quick Start

```bash
# List available presets
npx ruvector workers presets

# List available phase executors
npx ruvector workers phases

# Create a custom worker from preset
npx ruvector workers create my-scanner --preset security-scan

# Run the worker
npx ruvector workers run my-scanner --path ./src
```

## Available Presets

| Preset | Description | Phases |
|--------|-------------|--------|
| `quick-scan` | Fast file discovery and stats | file-discovery → summarization |
| `deep-analysis` | Comprehensive code analysis | file-discovery → static-analysis → complexity-analysis → import-analysis → pattern-extraction → graph-build → vectorization → summarization |
| `security-scan` | Security-focused analysis | file-discovery → security-analysis → secret-detection → dependency-discovery → report-generation |
| `learning` | Pattern learning and memory | file-discovery → pattern-extraction → embedding-generation → pattern-storage → sona-training |
| `api-docs` | API endpoint documentation | file-discovery → api-discovery → type-analysis → report-generation |
| `test-analysis` | Test coverage analysis | file-discovery → static-analysis → pattern-extraction → summarization |

## Phase Executors (24 total)

### Discovery Phases
- `file-discovery` - Find files matching patterns
- `pattern-discovery` - Discover code patterns
- `dependency-discovery` - Map dependencies
- `api-discovery` - Find API endpoints

### Analysis Phases
- `static-analysis` - AST-based code analysis
- `complexity-analysis` - Cyclomatic complexity
- `security-analysis` - Security vulnerability scan
- `performance-analysis` - Performance bottlenecks
- `import-analysis` - Import/export mapping
- `type-analysis` - TypeScript type extraction

### Pattern Phases
- `pattern-extraction` - Extract code patterns
- `todo-extraction` - Find TODOs/FIXMEs
- `secret-detection` - Detect hardcoded secrets
- `code-smell-detection` - Identify code smells

### Build Phases
- `graph-build` - Build code graph
- `call-graph` - Function call graph
- `dependency-graph` - Dependency graph

### Learning Phases
- `vectorization` - Convert to vectors
- `embedding-generation` - ONNX embeddings (384d)
- `pattern-storage` - Store in VectorDB
- `sona-training` - SONA reinforcement learning

### Output Phases
- `summarization` - Generate summary
- `report-generation` - Create report
- `indexing` - Index for search

## YAML Configuration

Create `workers.yaml` in your project:

```yaml
version: '1.0'
workers:
  - name: auth-scanner
    triggers: [auth-scan, scan-auth]
    phases:
      - type: file-discovery
        config:
          patterns: ["**/auth/**", "**/login/**"]
      - type: security-analysis
      - type: secret-detection
      - type: report-generation
    capabilities:
      onnxEmbeddings: true
      vectorDb: true

  - name: api-documenter
    triggers: [api-docs, document-api]
    phases:
      - type: file-discovery
        config:
          patterns: ["**/routes/**", "**/api/**"]
      - type: api-discovery
      - type: type-analysis
      - type: report-generation
```

Load configuration:
```bash
npx ruvector workers load-config --file workers.yaml
```

## CLI Commands

```bash
# Core commands
npx ruvector workers presets       # List presets
npx ruvector workers phases        # List phases
npx ruvector workers create <name> --preset <preset>
npx ruvector workers run <name> --path <path>

# Configuration
npx ruvector workers init-config   # Generate workers.yaml
npx ruvector workers load-config   # Load from workers.yaml
npx ruvector workers custom        # List registered workers

# Monitoring
npx ruvector workers status        # Worker status
npx ruvector workers results       # Analysis results
npx ruvector workers stats         # Statistics
```

## MCP Tools

Available via ruvector MCP server:

| Tool | Description |
|------|-------------|
| `workers_presets` | List available presets |
| `workers_phases` | List phase executors |
| `workers_create` | Create custom worker |
| `workers_run` | Run worker on path |
| `workers_custom` | List custom workers |
| `workers_init_config` | Generate config |
| `workers_load_config` | Load config |

## Capabilities

Workers can use these capabilities:

- **ONNX Embeddings**: Real transformer embeddings (all-MiniLM-L6-v2, 384d, SIMD)
- **VectorDB**: Store and search embeddings with HNSW indexing
- **SONA Learning**: Self-Organizing Neural Architecture for pattern learning
- **ReasoningBank**: Trajectory tracking and meta-learning

## Integration with Hooks

Workers auto-dispatch on UserPromptSubmit via trigger keywords:
- Type "audit this code" → triggers audit worker
- Type "security scan" → triggers security worker
- Type "learn patterns" → triggers learning worker

## Example: Security Scanner

```bash
# Create from security-scan preset
npx ruvector workers create security-checker --preset security-scan --triggers "security,vuln,cve"

# Run on source
npx ruvector workers run security-checker --path ./src

# View results
npx ruvector workers results
```

## Example: Custom Learning Worker

```yaml
# workers.yaml
workers:
  - name: code-learner
    triggers: [learn, pattern-learn]
    phases:
      - type: file-discovery
        config:
          patterns: ["**/*.ts", "**/*.js"]
          exclude: ["node_modules/**"]
      - type: static-analysis
      - type: pattern-extraction
      - type: embedding-generation
      - type: pattern-storage
      - type: sona-training
    capabilities:
      onnxEmbeddings: true
      vectorDb: true
      sonaLearning: true
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ruvnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
