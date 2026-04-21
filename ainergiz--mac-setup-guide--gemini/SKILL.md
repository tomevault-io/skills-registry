---
name: gemini
description: Use Google Gemini for AI chat, research, image/video creation, and analysis. Use when user wants to use Gemini, ask complex questions, generate images or videos, do deep research, or use Gemini's thinking capabilities. Use when this capability is needed.
metadata:
  author: ainergiz
---

# Google Gemini

Browser automation for Google Gemini AI assistant at `gemini.google.com`.

## Models

Access via model dropdown (bottom-right of input area):

| Model | Description | Best For |
|-------|-------------|----------|
| **Fast** | Quick responses | Simple questions, fast interactions |
| **Thinking** | Extended reasoning | Complex problem solving |
| **Pro** | Advanced capabilities | Math, code, detailed analysis |

## Tools

Access via sliders icon (left of model selector):

| Tool | Description |
|------|-------------|
| **Deep Research** | In-depth research with sources |
| **Deep Think** | Extended reasoning (shows thinking process) |
| **Create videos** | Video generation with Veo 3.1 |
| **Create images** | AI image generation |
| **Canvas** | Collaborative document editing |
| **Guided Learning** | Educational/tutorial mode |

### Enabling a Tool

1. Click sliders icon next to input field
2. Click tool name to enable (checkmark appears)
3. Tool appears as pill in input area
4. Click X on pill to disable

### Deep Think

- Takes several minutes for complex queries
- Shows "Show thinking" toggle to reveal reasoning
- Best for thorough analysis and research
- Copy button at bottom of response

### Deep Research

- Generates comprehensive reports with citations
- Returns multiple sources
- Good for fact-checking and exploration

## Input Options

Access via "+" button (left of input area):

| Option | Description |
|--------|-------------|
| **Upload files** | Attach local files |
| **Add from Drive** | Import Google Drive files |
| **Photos** | Access photo library |
| **Import code** | Add code snippets |
| **NotebookLM** | Direct NotebookLM integration |

## Gems (Custom Assistants)

Access via "Gems" in sidebar:

- **Pre-made Gems**: Google-created specialized assistants
- **My Gems**: Create custom personas with specific instructions
- Click "+ New Gem" to create custom assistant

## Response Features

| Feature | Location | Description |
|---------|----------|-------------|
| **Copy** | Bottom of response | Copy full response text |
| **Export to Sheets** | In tables | Export data to Google Sheets |
| **Sources** | Below response | View cited sources |
| **Like/Dislike** | Bottom of response | Feedback buttons |
| **Share** | Response menu | Share conversation |

## Navigation

**Sidebar:**
- New chat
- My stuff (recent items)
- Gems
- Chat history
- Activity
- Settings and help

**Retrieving Previous Chats:**
1. Click chat in sidebar under "Chats"
2. Or use search icon (top-left)

## Copying Responses

**Best Practice**: Use native "Copy response" button (two overlapping rectangles icon at bottom of response).

Avoid JavaScript clipboard API - can be overwritten by other processes. Paste immediately after copying.

## Workflow Examples

### Deep Think Research
```
1. Navigate to gemini.google.com
2. Click sliders icon → enable "Deep Think"
3. Select "Pro" model for best results
4. Enter research query
5. Wait for completion (may take minutes)
6. Click "Copy response" button
```

### Image Generation
```
1. Navigate to gemini.google.com
2. Click sliders icon → enable "Create images"
3. Describe desired image in detail
4. Review generated options
5. Click to download or regenerate
```

### Export to NotebookLM
```
1. Complete research in Gemini
2. Copy response using native button
3. Open NotebookLM (see notebooklm skill)
4. Create notebook → Copied text → Paste
```

## Tips

- Deep Think + Pro model = most thorough analysis
- Use Deep Research for fact-based queries needing citations
- Gems are useful for repeated specialized tasks
- Chat history preserves all conversations
- Export tables to Sheets for further analysis

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ainergiz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
