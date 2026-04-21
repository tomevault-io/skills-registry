---
name: research-synthesizer
description: > Use when this capability is needed.
metadata:
  author: fangdav
---

# Research Synthesizer

Pull together findings from multiple .md research files into a single, unified .docx deliverable.

## When to Use

- User wants a consolidated view of research scattered across folders
- A topic has been researched in multiple documents and needs a single source of truth
- Preparing a deliverable or briefing from raw research files

## Execution Flow

### Step 1: Identify Scope

Ask the user:
- What topic or question are you synthesizing around?
- Which folders should I pull from? (or "all research folders")

If not specified, search these locations for relevant .md files:
- `_Active/Product/General_Research/`
- `_Active/Finance/Assumption_Research/`
- `_Active/Marketing/General_Research/`
- `_Active/Legal/General_Research/`
- `_Active/Product/Brainstorming/`

### Step 2: Scan and Collect

1. Read all .md files in the target folders
2. Identify files relevant to the user's topic
3. Present a list of source files found and ask for confirmation before proceeding
4. Extract key findings, data points, conclusions, and open questions from each

### Step 3: Synthesize

Organize findings into a coherent narrative:

1. **Remove redundancy** — Merge overlapping findings across docs
2. **Resolve conflicts** — Flag where sources disagree; note both positions
3. **Identify gaps** — Call out what the research does NOT cover
4. **Preserve attribution** — Note which source file each finding came from

### Step 4: Output

Produce a .docx file with this structure:

```
Title: [Topic] — Research Synthesis
Date: [Date]
Sources: [List of source .md files]

1. Executive Summary
   - 3-5 bullet points capturing the headline findings

2. Key Findings
   - Organized by theme, not by source document
   - Each finding attributed to its source file(s)

3. Conflicting Information
   - Where sources disagree, present both sides

4. Gaps & Open Questions
   - What the research hasn't addressed yet

5. Source Index
   - Table listing each source file, its folder, and what it covers
```

### Step 5: Create the Topic Folder and Research Subfolder

Every .docx deliverable lives inside a dedicated topic folder with a `Research/` subfolder containing all source .md files.

**Create this structure:**
```
[Department_Folder]/
└── [Topic_of_Research]/
    ├── [Topic_of_Research].docx
    └── Research/
        ├── README.md
        ├── [source_file_1].md
        ├── [source_file_2].md
        ├── [Segment_A]/
        │   ├── [deep_dive].md
        │   └── [sub_topic]/
        │       └── [sub_research].md
        └── ...
```

**Steps:**
1. Create `[Topic_of_Research]/` in the appropriate department folder
2. Create `Research/` inside it
3. Copy or move all source .md files into `Research/`, preserving any logical groupings as subfolders (by segment, sub-topic, or phase)
4. If deep-dive files exist for specific segments, nest them in segment subfolders within `Research/`
5. Add a `README.md` inside `Research/` describing its contents and folder structure
6. Place the .docx at the top level of the topic folder, alongside `Research/`

The `Research/` folder can be deeply nested — organize by whatever structure makes the sources navigable.

### Step 6: Hyperlink Source Files

The finished .docx must include **clickable hyperlinks** to files inside its `Research/` subfolder:
- Every inline citation that references an internal .md file should hyperlink to that file within `Research/`
- The Source Index table should have each .md filename as a hyperlink
- This allows readers to click through from any finding directly to the original research document
- All links should use relative paths within the topic folder

## Style

- Parsimonious. State findings directly, no filler.
- Attribute everything. Never present a finding without noting where it came from.
- Flag uncertainty. If a finding is speculative or based on limited data, say so.

## Sources & Citations (Vancouver Style)

Every claim, data point, and finding must be traceable. Use **Vancouver citation style** — numbered references in order of first appearance.

**Inline:** Use bracketed numbers where a finding is stated.
- Example: `Users prefer token-gated access over free entry [1], though retention varies by club size [2,3].`

**The output must include:**
1. **Inline citations** — Each finding gets a numbered reference pointing to the source
2. **References section** — Numbered list at the end combining both internal files and external sources
   - Internal: `[1] Pocket Space. SocialFi_Market_Analysis.md. _Active/Product/General_Research/. 2025.`
   - External: `[2] de la Rouviere S. Tokens 2.0: Curved token bonding. Medium. 2017. https://example.com`
3. **Source Index** — A table listing every internal .md file used, its folder path, and what it covers
4. **Carry-through** — If a source .md file cites external URLs, papers, or reports, carry those into the synthesis references

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fangdav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
