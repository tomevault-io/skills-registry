---
name: add-portfolio
description: Add a new project to the portfolio docs. Use when the user wants to add a new work/project to their portfolio. Use when this capability is needed.
metadata:
  author: farl
---

Add a new portfolio project page based on the user's input.

## Steps

1. **Parse the user's input** from `$ARGUMENTS` to extract:
   - Project name
   - GitHub repo URL
   - Demo URL (if provided)
   - YouTube video URL (if provided, extract the video ID for embedding)
   - Project description / summary
   - Feature list

2. **Determine `sidebar_position`**: Read all existing `docs/*.md` files, find the highest `sidebar_position` value, and use the next number.

3. **Generate the filename**: Convert the project name to kebab-case for the filename (e.g., "Multiplayer Piano" → `multiplayer-piano.md`).

4. **Create `docs/<filename>.md`** using this template:

```markdown
---
sidebar_position: <next_position>
---

# <Project Name>

<One-line summary>

- **Demo**: <demo_url>
- **Source Code**: <github_url>

## Demo

<iframe width="100%" style={{aspectRatio: '16/9'}} src="https://www.youtube.com/embed/<video_id>" title="<Project Name> Demo" frameBorder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowFullScreen></iframe>

## About

<Description paragraph>

## Features

- <feature 1>
- <feature 2>
- ...
```

### Template rules

- If no YouTube URL is provided, omit the entire `## Demo` section and the iframe.
- If no Demo URL is provided, omit the `- **Demo**: ...` line.
- For YouTube URLs, extract the video ID from formats like:
  - `https://youtu.be/<id>`
  - `https://www.youtube.com/watch?v=<id>`
- Write descriptions and features in the same language as the user's input.

5. **Update `docs/intro.md`**: Add the new project to the `## Projects` list, following the existing format:
   ```
   - [<Project Name>](/docs/<slug>) - <One-line summary>
   ```

6. **Show the user** a summary of what was created.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/farl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
