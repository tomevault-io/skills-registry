---
name: skill-distiller
description: Distill a set of sources (YouTube/video transcripts, web URLs, local documents, notes) into a new AgentSkill: ingest content, organize citations, and generate a skill folder with SKILL.md + references + optional scripts. Use when asked to 'make a skill from these videos/docs/links', 'turn this corpus into a skill', or 'create a domain-specific agent skill from source material'. Use when this capability is needed.
metadata:
  author: mcart13
---

# Skill Distiller

Turn a pile of source material into a reusable **AgentSkill**.

## Pre-flight: Check dependencies

Before ingesting YouTube sources, verify `yt-dlp` is installed:

```bash
which yt-dlp || echo "NOT INSTALLED"
```

**If not installed**, prompt the user to install it before proceeding with YouTube ingestion:

- macOS: `brew install yt-dlp`
- Linux: `pip install yt-dlp`
- Windows: `winget install yt-dlp`

If the user only has URLs and local files (no YouTube), skip this check.

---

## Workflow

### 1) Define the target skill

Collect:

- **Skill name** (lowercase + hyphens)
- **What it should help with** (2–5 concrete user requests it should handle)
- **What it should _not_ do** (boundaries)

**Scope check — should this be one skill or many?**

If the topic covers 3+ distinct categories or 5+ independent tactics, split it:

- **One skill** = the index (`SKILL.md` with a quick-reference table + priority tiers)
- **Individual rules** = one file per rule in a `rules/` directory (each with: why it matters, bad example, good example)

Example pattern (see `vercel-react-best-practices` for a live implementation):

```
my-skill/
├── SKILL.md          # Index: categories, priority table, rule list
└── rules/
    ├── rule-one.md   # Why + bad + good
    ├── rule-two.md
    └── ...
```

The agent loads SKILL.md first (lightweight), then pulls individual rule files only when relevant. This keeps context lean and lets each rule be self-contained.

### 2) Ingest sources into a corpus folder

Use the bundled ingestion scripts to create a local corpus you can cite and reuse.

**YouTube transcript → text**

```bash
python3 ~/.claude/skills/skill-distiller/scripts/get_youtube_transcript.py "https://www.youtube.com/watch?v=VIDEO_ID" > out.txt
```

**URL → rough readable text**

```bash
python3 ~/.claude/skills/skill-distiller/scripts/extract_url_text.py "https://example.com/article" > out.txt
```

**Local file → text**

```bash
python3 ~/.claude/skills/skill-distiller/scripts/read_text_file.py /path/to/file.md > out.txt
```

Save raw output directly into the new skill's folder (not /tmp — it needs to persist):

```
<new-skill>/
  references/
    sources.md
    knowledge.md
    raw/
      001-*.txt
      002-*.txt
```

**Coverage check before distilling:** After ingesting, ask yourself — do the sources actually cover the topic well enough? If there are obvious gaps (e.g. you have tactics but no data on results, or theory but no practical examples), fetch additional sources before moving on. Better to have complete coverage than to distill incomplete material.

### 3) Distill (synthesize) into references

Create `references/knowledge.md` with:

- Overview + glossary
- Processes/checklists
- Gotchas/anti-patterns
- Canonical templates/snippets (if applicable)

Create `references/sources.md` with:

- Bullet list of every source (URL, title, date captured)
- Notes about source reliability (official docs vs opinion)

Keep SKILL.md lean; put depth in `references/`.

### 4) Generate the new skill folder

**For detailed guidance on skill structure, writing style, and best practices, read [../skill-creator/SKILL.md](../skill-creator/SKILL.md).**

Create the new skill folder (name must match the skill):

- `<new-skill>/SKILL.md` (frontmatter: `name`, `description`)
- `references/` for the distilled knowledge
- `rules/` if the topic was split into individual rules (see scope check above)
- `scripts/` only when deterministic tooling is needed
- `assets/` for templates, images, or files used in output (not loaded into context)

**Description format:** Write the `description` in third-person with trigger keywords. The agent uses it to decide when to activate the skill — it should say _when_ to use it, not _what_ it does:

```yaml
# Good — tells the agent when to activate
description: "React performance optimization guidelines. This skill should be used when writing, reviewing, or refactoring React components."

# Bad — describes the skill instead of triggering it
description: "Contains best practices for React performance."
```

### 5) Test the skill

Dry-run with 2–3 realistic prompts and verify it:

- Uses the references appropriately
- Doesn’t hallucinate beyond the corpus
- Has clear refusal/boundary behavior when outside scope

## Requirements / Notes

- **YouTube transcript fetching requires `yt-dlp`** in PATH. Install with:
  - macOS: `brew install yt-dlp`
  - Linux: `pip install yt-dlp` or check [yt-dlp releases](https://github.com/yt-dlp/yt-dlp/releases)
  - Windows: `winget install yt-dlp` or download from releases
- `extract_url_text.py` is intentionally dependency-light (stdlib only); it's "good enough" for most articles but not perfect.
- All Python scripts require Python 3.8+.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcart13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
