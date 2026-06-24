---
name: ontology-engine
description: Convert markdown vault to RDF/TTL knowledge graph. Extract persons, projects, meetings, and relationships into semantic triples. Use when this capability is needed.
metadata:
  author: kubony
---

# Ontology Engine

Convert markdown vault to RDF/TTL ontology for semantic querying.

## Usage

### Build Knowledge Graph

```bash
source .venv/bin/activate && \
python .claude/skills/ontology-engine/scripts/vault_to_ttl.py ./vault --output ./knowledge.ttl
```

### With Custom Folders

```bash
source .venv/bin/activate && \
python .claude/skills/ontology-engine/scripts/vault_to_ttl.py ./vault \
  --persons-dir contacts \
  --projects-dir work \
  --output ./knowledge.ttl
```

## Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `vault_path` | Required | Root vault directory |
| `--output` | `knowledge.ttl` | Output TTL file |
| `--persons-dir` | `persons` | Person files folder |
| `--projects-dir` | `projects` | Project files folder |
| `--archives-dir` | `archives` | Archived projects folder |

## Extraction Rules

### Person Files

**Location**: `{vault}/{persons-dir}/*.md`

**Filename Pattern**: `Name_Organization.md`

**Extracted Fields**:
- Name (from YAML `title` or filename)
- Organization (from filename suffix)
- Tags (from YAML `tags`)
- Summary (from YAML `summary`)
- Contact info (from YAML `contact`)
- Last contact date (from YAML `last_contact`)

### Project Files

**Location**: `{vault}/{projects-dir}/*/`, `{vault}/{archives-dir}/*/`

**Folder Pattern**: `YYMM ProjectName` or `YYMMDD ProjectName`

**Extracted Fields**:
- Project name
- Start date (from folder name)
- Tags
- Summary
- Status (active/archived)

### Meetings

**Pattern**: `## YYYY.MM.DD` or `## YYYY-MM-DD` headings

**Extracted Fields**:
- Date
- Summary (content under heading)
- Topics (hashtags in content)
- Participants (from [[wikilinks]])

### Relationships

**From [[Wikilinks]]**:
- Person → Person: `:knows`
- Person → Project: `:involvedIn`
- Meeting → Person: `:participant`
- Meeting → Project: `:relatedTo`

## Output Schema

### Classes

| Class | Description |
|-------|-------------|
| `:Person` | Individual person |
| `:Project` | Project or initiative |
| `:Meeting` | Meeting, call, or interaction |
| `:Organization` | Company or group |
| `:Topic` | Discussion topic |

### Properties

| Property | Domain | Range |
|----------|--------|-------|
| `:name` | Any | string |
| `:date` | Meeting/Project | date |
| `:summary` | Any | string |
| `:filePath` | Person/Project | string |
| `:tag` | Any | string |
| `:participant` | Meeting | Person |
| `:affiliatedWith` | Person | Organization |
| `:involvedIn` | Person | Project |
| `:relatedTo` | Meeting | Project |
| `:hasTopic` | Meeting | Topic |
| `:knows` | Person | Person |

## Example Output

```turtle
@prefix : <http://obsidian.local/ontology#> .
@prefix data: <http://obsidian.local/data#> .

data:John_Doe a :Person ;
    :name "John Doe"@en ;
    :affiliatedWith data:Acme_Corp ;
    :summary "Senior developer specializing in AI" ;
    :filePath "persons/John_Doe_Acme.md" .

data:Acme_Corp a :Organization ;
    :name "Acme Corp"@en .

data:John_Doe_meeting_0 a :Meeting ;
    :participant data:John_Doe ;
    :date "2024-01-15"^^xsd:date ;
    :summary "Project kickoff discussion" .
```

## Dependencies

```bash
pip install rdflib pyyaml
```

---
> Source: [kubony/claude-knowledge-graph](https://github.com/kubony/claude-knowledge-graph) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
