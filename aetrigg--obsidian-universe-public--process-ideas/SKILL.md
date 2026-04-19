---
name: process-ideas
description: Scans recent daily stars for idea signals [i] and "What's On My Mind?" journal entries, then helps convert them into missions in the appropriate sub-INBOXes. Use when this capability is needed.
metadata:
  author: aetrigg
---

# Process Ideas Skill

## Prerequisites
- Read SYSTEM/_ai/Agents/README.md for universe context
- Read SYSTEM/_ai/Agents/MISSION_PROTOCOLS.md for detailed workflow
- Read SYSTEM/_ai/Agents/UNIVERSE_STRUCTURE.md for INBOX routing rules

## Workflow

1. **Scan Daily Stars**
   - Look in daily notes (YYYY-MM-DD format) from the last 7 days
   - Search for lines starting with `- [i]` (idea signals)
   - Search "What's On My Mind?" sections for task/idea mentions

2. **Present Ideas**
   - Show each idea with context (date, time, surrounding text)
   - Format: "📅 YYYY-MM-DD @ HH:MM [pm/am] (idea signal/captured idea)"

3. **Process Each Idea**
   For each idea found, ask:
   - Convert to mission? (y/n/later)
   - Which sub-INBOX? (auto-suggest based on context and tag routing rules from MISSION_PROTOCOLS.md)
   - Tag (infer from context when possible)
   - Due date (optional, YYYY-MM-DD format)
   - Priority (optional: ⏫ critical / 🔼 high / 🔽 low)

4. **Format Mission**
   Use Tasks plugin format:
   ```
   - [ ] mission description #tag 📅 YYYY-MM-DD ⏫
   ```

5. **Add to Sub-INBOX**
   - Route to appropriate INBOX/XX_NAME sub-INBOX file
   - Add to relevant section within that file
   - Confirm addition to user

## Tag Routing Rules
Reference MISSION_PROTOCOLS.md for complete routing map:
- #obsidian → 08_UNIVERSE
- #chores → 02_CHORES
- #personal, #reading, #scripting, #gaming, #gospel, #personal/* → 01_PERSONAL
- #finances, #finances/* → 03_FINANCES
- #writing, #writing/*, #5th-of-june, #5oJ, #5thofjune → 04_WRITING
- #coloringthecosmos, #coloring-the-cosmos, #coloring, #coloring/*, #coloringbooks, #coloringbooks/* → 05_COLORING
- #family, #family/*, #friends, #friends/* → 06_FAMILY-FRIENDS
- #work → ask user
- unknown → prompt user, offer to remember routing

## Example Output

```
Found 2 ideas in recent daily stars:

📅 2025-01-22 @ 05:55 pm (idea signal)
"update NORTH STAR glossary to include missions terminology"

Convert to mission? (y/n/later): y
Mission: "update NORTH STAR glossary to include missions terminology"
Which INBOX? (UNIVERSE/PERSONAL/etc): UNIVERSE
Tag: #obsidian (inferred, correct?): y
Due date? (optional): none

✓ Added to 08_UNIVERSE:
- [ ] update NORTH STAR glossary to include missions terminology #obsidian

---

📅 2025-01-20 (captured idea from "What's On My Mind?")
"Maybe I should organize my 5th of June research using NotebookLM"

Convert to mission? (y/n/later): y
Mission: "organize 5th of June research using NotebookLM"
Which INBOX? (UNIVERSE/PERSONAL/etc): WRITING
Tag: #5th-of-june (inferred, correct?): y
Due date? (optional): 2025-01-30

✓ Added to 04_WRITING:
- [ ] organize 5th of June research using NotebookLM #5th-of-june 📅 2025-01-30
```

## Notes
- Always check both [i] glyphs AND "What's On My Mind?" sections
- Remove "every Monday" type frequency text when converting recurring task templates
- Be respectful of Andrea's reactive workflow - suggest, don't dictate
- If unsure about routing, ask rather than guess
- Keep mission descriptions concise and actionable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aetrigg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
