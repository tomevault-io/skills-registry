---
name: hackmd-api
description: Interact with HackMD API to create, update, list, and manage notes programmatically. Use when you need to create documentation in HackMD, sync content, or automate note management workflows. Use when this capability is needed.
metadata:
  author: eastsun5566
---

# HackMD API Integration

This skill enables AI agents to interact with the HackMD API to programmatically manage notes, create documentation, and automate collaborative workflows.

## When to Use This Skill

Use this skill when you need to:

- **Create documentation automatically** after completing a task or project
- **Sync content** from code repositories to HackMD
- **Manage notes programmatically** (create, update, list, delete)
- **Automate collaborative workflows** like creating meeting notes or project documentation
- **Backup or migrate** HackMD content

## Prerequisites

Before using this skill, ensure:

1. **HackMD API Token**: You must have a valid HackMD API token
   - Get one from: https://hackmd.io/settings#api
   - Set the environment variable: `export HACKMD_API_TOKEN=your_token_here`

2. **Node.js**: The skill requires Node.js (v18 or later)

3. **Dependencies**: Install the required packages:
   ```bash
   cd skills/hackmd-api/scripts
   npm install
   ```

## Available Commands

The skill provides a CLI tool located at `scripts/hackmd-cli.mjs` with the following commands:

### List Notes

List all your HackMD notes:

```bash
node scripts/hackmd-cli.mjs list-notes
```

Options:

- `--limit <number>` - Limit the number of notes returned (default: 10)

### Create Note

Create a new HackMD note:

```bash
node scripts/hackmd-cli.mjs create-note --title "My Note" --content "# Hello\nThis is my note"
```

Options:

- `--title <string>` - Note title (required)
- `--content <string>` - Note content in Markdown (required)
- `--read-permission <string>` - Read permission: owner, signed_in, guest (default: owner)
- `--write-permission <string>` - Write permission: owner, signed_in, guest (default: owner)

### Get Note

Retrieve a note's content and metadata:

```bash
node scripts/hackmd-cli.mjs get-note <note-id>
```

### Update Note

Update an existing note:

```bash
node scripts/hackmd-cli.mjs update-note <note-id> --content "# Updated\nNew content"
```

Options:

- `--content <string>` - New note content (required)
- `--read-permission <string>` - Update read permission
- `--write-permission <string>` - Update write permission

### Delete Note

Delete a note:

```bash
node scripts/hackmd-cli.mjs delete-note <note-id>
```

## Usage Examples

### Example 1: Create Project Documentation

When completing a project, automatically create documentation in HackMD:

````markdown
1. Gather project information (overview, features, setup instructions)
2. Format content using HackMD Flavored Markdown (use the `hackmd` skill for syntax)
3. Create the note:
   ```bash
   node scripts/hackmd-cli.mjs create-note \
     --title "Project X Documentation" \
     --content "$(cat project-docs.md)" \
     --read-permission signed_in
   ```
````

4. The command will return the note URL for sharing

````

### Example 2: Sync Code Documentation

Keep HackMD documentation in sync with code changes:

```markdown
1. Generate documentation from code comments
2. Check if the note exists using list-notes
3. If exists, update it:
   ```bash
   node scripts/hackmd-cli.mjs update-note <note-id> \
     --content "$(cat updated-docs.md)"
````

4. If not, create a new note

````

### Example 3: Create Meeting Notes

Automatically create meeting notes:

```markdown
1. Prepare meeting agenda in Markdown format
2. Create the note with appropriate permissions:
   ```bash
   node scripts/hackmd-cli.mjs create-note \
     --title "Team Meeting - 2024-01-15" \
     --content "# Team Meeting\n\n## Agenda\n- Topic 1\n- Topic 2" \
     --read-permission signed_in \
     --write-permission signed_in
````

3. Share the URL with team members

````

## Error Handling

The CLI tool provides clear error messages:

- **Missing API token**: Set `HACKMD_API_TOKEN` environment variable
- **Invalid note ID**: Verify the note exists and you have access
- **Permission denied**: Check your API token has the necessary permissions
- **Network errors**: Verify internet connection and HackMD API status

## Integration with hackmd Skill

This skill works best in combination with the `hackmd` skill:

1. Use `hackmd` skill to **format content** with correct HackMD syntax (alerts, diagrams, embeds, etc.)
2. Use `hackmd-api` skill to **publish and manage** the formatted content

## API Reference

For detailed API documentation, see:
- [API_REFERENCE.md](references/API_REFERENCE.md)
- [HackMD API Documentation](https://hackmd.io/@hackmd-api/developer-portal)

## Security Notes

- **Never commit** your API token to version control
- Store tokens in environment variables or secure credential managers
- Use appropriate **read/write permissions** for sensitive content
- **Audit access** regularly through HackMD settings

## Troubleshooting

### Command not found

Ensure you're in the correct directory and Node.js is installed:

```bash
cd /path/to/hackmd-skills
node --version  # Should be v18 or later
````

### API Token Issues

Verify your token is set correctly:

```bash
echo $HACKMD_API_TOKEN  # Should display your token
```

### Dependencies Missing

Install dependencies if you see module errors:

```bash
cd skills/hackmd-api/scripts
npm install
```

## Contributing

Found an issue or have a suggestion? Please open an issue on the GitHub repository.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eastsun5566) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
