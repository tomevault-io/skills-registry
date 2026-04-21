---
name: notebooklm
description: Use Google NotebookLM to create AI notebooks, generate slides, audio overviews, quizzes, flashcards, and other outputs from sources. Use when user wants to create presentations, study materials, summaries, or analyze documents with NotebookLM. Use when this capability is needed.
metadata:
  author: ainergiz
---

# Google NotebookLM

Browser automation for Google NotebookLM at `notebooklm.google.com`.

## Overview

NotebookLM creates AI-powered notebooks from your sources, then generates various outputs (slides, audio, quizzes, etc.) using the Studio panel.

## Creating a Notebook

1. Navigate to `notebooklm.google.com`
2. Click **"+ Create notebook"** (top bar)
3. Add sources in the dialog

## Source Types

| Source | Description |
|--------|-------------|
| **Google Drive** | Docs, Slides, PDFs, Sheets |
| **Website** | Any public URL |
| **YouTube** | Videos with transcripts |
| **Copied text** | Paste content directly |
| **Web search** | Search and add from results |

### Adding Sources

**From "+ Add sources" button:**
- Select source type
- For copied text: paste content → click "Insert"
- For URLs: enter link → click "Insert"
- For Drive: browse and select files

**From Gemini (recommended for research):**
1. Use `gemini` skill with Deep Think for research
2. Copy response using native button
3. In NotebookLM: Add sources → Copied text → Paste → Insert

**Deep Research Integration:**
- Banner: "Try Deep Research for an in-depth report and new sources!"
- Search bar: "Search the web for new sources"
- Options: Web dropdown, Fast research toggle

## Studio Panel

Right panel with generation options. **Each has edit (pencil) icon for customization.**

| Feature | Output |
|---------|--------|
| **Audio Overview** | Podcast-style AI discussion |
| **Video Overview** | Video summary |
| **Mind Map** | Visual concept diagram |
| **Reports** | Structured documents |
| **Flashcards** | Study cards |
| **Quiz** | Test questions |
| **Infographic** | Visual representation |
| **Slide deck** | Presentation slides |
| **Data table** | Structured data extraction |

### Customizing Outputs

1. Click **pencil/edit icon** next to feature name
2. Adjust settings in dialog
3. Click **Generate**

**Slide Deck Options:**
| Setting | Options |
|---------|---------|
| Format | Detailed deck / Presenter slides |
| Language | Target language |
| Length | Short / Default / Long |
| Description | Custom instructions |

**Example customization:**
- Length: Short
- Description: "For lay audience, simple non-technical language"

## Chat Interface

**Middle panel features:**
- Auto-generated summary of sources
- Chat input for questions
- Source selector (bottom of input)
- "Save to note" button
- Copy, like, dislike buttons

**Asking questions:**
1. Type in "Start typing..." field
2. Select which sources to use (shows "X sources")
3. Press Enter or click send

## Notes

- **"+ Add note"** button (bottom-right of Studio)
- Save important responses as notes
- Notes appear in Studio panel history

## Top Bar Features

| Button | Function |
|--------|----------|
| **+ Create notebook** | New notebook |
| **Analytics** | Usage statistics |
| **Share** | Share notebook |
| **Settings** | Notebook settings |

## Workflow: Gemini to NotebookLM

For research-to-presentation workflows:

```
1. [Gemini] Use Deep Think + Pro for research (see gemini skill)
2. [Gemini] Copy response using native button
3. [NotebookLM] Navigate to notebooklm.google.com
4. [NotebookLM] + Create notebook
5. [NotebookLM] Add sources → Copied text → Paste → Insert
6. [NotebookLM] Wait for summary generation
7. [Studio] Click desired output (e.g., Slide deck)
8. [Studio] Click pencil icon → customize → Generate
```

## Tips

- Multiple sources can be combined in one notebook
- Select specific sources when chatting for focused answers
- Generated items appear in Studio panel history
- Audio Overview creates engaging podcast-style summaries
- Use Quiz + Flashcards together for study materials
- Mind Map helps visualize complex topic relationships

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Generation takes long | Normal - continue other work |
| Wrong content pasted | Clipboard overwritten; recopy from source |
| Studio panel missing | Click on notebook with sources; appears on right |
| Can't customize | Click pencil/edit icon next to feature name |
| Source not processing | Check file format is supported |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ainergiz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
