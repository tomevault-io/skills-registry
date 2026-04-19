---
name: export-conversation
description: Export the current Claude Code conversation to an Excel (.xlsx) file with columns for Prompt, Summary, and Output. Use when user asks to export, save, or document the conversation. Requires Python with openpyxl. Use when this capability is needed.
metadata:
  author: ormizj
---

# Conversation to Excel Exporter

This skill exports the current Claude Code conversation to a structured Excel file for documentation, sharing, or archival purposes.

## Quick start

When invoked, this skill will:
1. Analyze the conversation history
2. Extract user prompts and assistant responses
3. Generate concise summaries for each exchange
4. Create an Excel file with three columns: Prompt, Summary, Output
5. Save with a timestamped filename (e.g., `conversation-export-2026-01-12-143052.xlsx`)

**Example usage:**
```
User: /export-conversation
User: Export this conversation to Excel
User: Save our conversation as an Excel file
```

## Excel Structure

The generated file contains three columns:

| Column | Description | Content |
|--------|-------------|---------|
| **Prompt** | User's input | The complete user question/request |
| **Summary** | Brief outcome | AI-generated summary of what was accomplished |
| **Output** | Full response | The complete assistant response (may be truncated for very long responses) |

## Workflow

### 1. Prepare the Conversation Data

First, create a structured representation of the conversation history. This involves:

- Extracting each conversation turn (user prompt + assistant response)
- Identifying key actions taken (file reads, code changes, command executions)
- Noting any errors or issues encountered

### 2. Generate Summaries

For each conversation turn, generate a concise summary (1-2 sentences) that captures:
- **What was requested**: The user's goal or question
- **What was done**: Key actions taken or information provided
- **Outcome**: Success, partial completion, or next steps needed

**Summary examples:**
- "Created new Vue component with accordion functionality"
- "Debugged authentication error in API endpoint, fixed token validation"
- "Explained TypeScript generics using restaurant menu analogy and code examples"
- "Set up WebSocket connection for real-time chat feature"

### 3. Create the Excel File

Use the Python utility script to generate the Excel file:

```bash
python scripts/export_conversation.py
```

The script will:
- Create a new Excel workbook
- Format headers with bold text and background color
- Set appropriate column widths for readability
- Apply text wrapping for better presentation
- Save the file in the current working directory

### 4. Format and Finalize

The script automatically applies formatting:
- **Header row**: Bold, blue background, white text
- **Column widths**: Prompt (40), Summary (50), Output (80)
- **Text wrapping**: Enabled for all cells
- **Font**: Arial 10pt for content, 11pt bold for headers

### 5. Confirm Export

Report to the user:
- Full path to the exported file
- Number of conversation turns exported
- File size (approximate)
- Quick preview of the first few entries

## Implementation Guide

### Step 1: Prepare Conversation Data

Create a structured data format for the conversation. You'll need to manually construct this based on the conversation context:

```python
conversation_data = [
    {
        "prompt": "User's first question or request",
        "summary": "Brief description of what was accomplished",
        "output": "Full assistant response text"
    },
    {
        "prompt": "User's second question or request",
        "summary": "Brief description of what was accomplished",
        "output": "Full assistant response text"
    },
    # ... more turns
]
```

### Step 2: Write the Data File

Create a JSON file with the conversation data:

```python
import json
from datetime import datetime

data = {
    "export_date": datetime.now().isoformat(),
    "conversation": conversation_data
}

with open("conversation_data.json", "w", encoding="utf-8") as f:
    json.dump(data, f, indent=2, ensure_ascii=False)
```

### Step 3: Run the Export Script

Execute the Python script that reads the JSON and creates the Excel file:

```bash
python scripts/export_conversation.py conversation_data.json
```

### Step 4: Verify and Report

Check that the file was created successfully and provide the user with the file location.

## Handling Edge Cases

### Very Long Responses

If an assistant response is extremely long (>32,767 characters - Excel cell limit):
- Truncate with "...[truncated]" marker
- Note in the summary that output was truncated
- Suggest splitting into multiple rows if necessary

### Empty or Single-Turn Conversations

If conversation has minimal content:
- Still create the file but include a note in the summary
- Example: "Conversation too short - minimal context to export"

### Special Characters and Formatting

The script handles:
- Unicode characters (emojis, special symbols)
- Code blocks and formatting
- Line breaks (preserved in Excel cells)
- Long URLs

### Missing Dependencies

If openpyxl is not installed:
```bash
pip install openpyxl
```

If Python is not available:
- Inform user that Python 3.6+ is required
- Provide installation instructions
- Alternative: Offer CSV export instead (simpler, no dependencies)

## Summary Generation Guidelines

**Good summaries:**
- ✅ "Created authentication middleware with JWT validation and error handling"
- ✅ "Debugged CORS issue in API, updated Nuxt config to allow localhost:3000"
- ✅ "Refactored database queries to use prepared statements, improved performance"
- ✅ "Explained Vue Composition API lifecycle with diagram and examples"

**Poor summaries:**
- ❌ "Did some work" (too vague)
- ❌ "Fixed it" (what was fixed?)
- ❌ "Made changes to files" (which files? what changes?)
- ❌ "Responded to user" (not descriptive)

**Key principles:**
- Be specific about what was done
- Include the main technology/file/component involved
- Mention the outcome or result
- Keep it to 1-2 sentences maximum

## Requirements

### Software Requirements
- **Python**: Version 3.6 or higher
- **openpyxl**: Python library for Excel file creation
  ```bash
  pip install openpyxl
  ```

### Environment
- Works on Windows, macOS, and Linux
- Requires write permissions in the working directory
- No special environment variables needed

### Installation Check

To verify Python and openpyxl are available:
```bash
python --version
python -c "import openpyxl; print(f'openpyxl version: {openpyxl.__version__}')"
```

## File Reference

- **[scripts/export_conversation.py](scripts/export_conversation.py)**: Python utility for Excel generation
- **[EXAMPLES.md](EXAMPLES.md)**: Sample conversations and their Excel outputs

## Example Interaction

```
User: Can you export this conversation to Excel?
Assistant: I'll export our conversation to a structured Excel file.

[Analyzes conversation history and creates conversation_data.json]

I've extracted our conversation with the following exchanges:
1. Initial request about export functionality
2. [... other exchanges ...]

Now creating the Excel file...

[Runs: python scripts/export_conversation.py conversation_data.json]

Done! Your conversation has been exported to:
C:\Users\orm\Desktop\Files\My Projects\claude-sandbox\conversation-export-2026-01-12-143052.xlsx

Summary:
- 5 conversation turns exported
- File size: 12.3 KB
- Columns: Prompt, Summary, Output

You can open this file in Excel, Google Sheets, or any spreadsheet application.
```

## Tips for Best Results

1. **Review before export**: Check that all important exchanges are included
2. **Customize summaries**: Edit summaries to be more specific if needed
3. **Split long conversations**: For very long conversations (50+ turns), consider splitting into multiple exports by topic
4. **Preserve context**: Include relevant background in prompts if exchanges reference earlier work
5. **Test the script first**: Run the export script on sample data to ensure dependencies are installed

## Troubleshooting

### Python not found
```
Error: python: command not found
```
**Solution**: Install Python 3.6+ from python.org or use your system package manager

### openpyxl not installed
```
ModuleNotFoundError: No module named 'openpyxl'
```
**Solution**: Run `pip install openpyxl`

### Permission denied
```
PermissionError: [Errno 13] Permission denied
```
**Solution**: Check write permissions in the directory or specify a different output location

### File already open
```
PermissionError: ... process cannot access the file ...
```
**Solution**: Close the Excel file if it's already open, or export to a different filename

## Alternative: CSV Export

If Python/Excel dependencies are not available, you can export to CSV instead:

```python
import csv

with open('conversation-export.csv', 'w', newline='', encoding='utf-8') as f:
    writer = csv.writer(f)
    writer.writerow(['Prompt', 'Summary', 'Output'])

    for turn in conversation_data:
        writer.writerow([turn['prompt'], turn['summary'], turn['output']])
```

CSV files can be opened in Excel, but won't have the formatting (colors, column widths, etc.)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ormizj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
