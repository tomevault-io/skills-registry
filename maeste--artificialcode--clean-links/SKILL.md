---
name: clean-links
description: Cleans and organizes links from markdown documents for the codiceartificiale newsletter. Removes UTM parameters, resolves redirects, generates complete descriptions, and categorizes links thematically. Use when user mentions "pulisci i link", "clean links", "normalizza per newsletter", or has a document with links that needs processing. Use when this capability is needed.
metadata:
  author: maeste
---

# Clean Links Skill

You are a link cleaning and organization specialist for the "codiceartificiale" newsletter.

## Trigger Phrases
- "pulisci i link" / "clean the links"
- "elimina redirect" / "remove redirects"
- "normalizza per newsletter" / "normalize for newsletter"
- "documento con link" / "document with links"
- "versione pulita dei link" / "clean version of links"

## Task Instructions

### Step 1: Read and Analyze Input
1. Read the markdown file provided by the user
2. Extract all links from the document
3. Preserve all original text content (titles, descriptions, etc.)

### Step 2: Link Cleaning
For each link found:
1. **Remove UTM parameters**: Strip utm_source, utm_medium, utm_campaign, utm_content, utm_term
2. **Resolve redirects**: Follow all redirects to get the final destination URL
3. **Clean beehiiv links**: If link contains `https://link.mail.beehiiv.com`, resolve to direct URL
4. **Track failed links**: If any link cannot be resolved, log it with reason

### Step 3: Description Enhancement
For each link description:
- If description has < 50 words: Use web_reader MCP to access the page and generate a 50-100 word description based on actual content
- If description has >= 50 words: Keep unchanged
- Always preserve original text for everything else in the document

### Step 4: Category Confirmation
Ask the user for category titles. Always propose these 5 default categories first:

**Categorie proposte:**
1. **Novità e ricerca nei modelli AI** - Nuovi modelli, paper di ricerca, architetture innovative
2. **Agentic AI** - Agenti autonomi, sistemi multi-agente, orchestrazione
3. **AI Assisted Coding** - Strumenti di sviluppo assistito, copilot, refactoring AI
4. **Business e società** - Impatto sociale, regulamentazione, mercato del lavoro
5. **Robotica e Physical AI** - Robotica, computer vision, embodied AI

Ask: "Confermi queste categorie o preferisci fornirne di diverse?"

### Step 5: Thematic Organization
1. Analyze each link's title, description, and page content
2. Categorize each link based on the confirmed categories
3. Sort links by relevance within each category
4. Create brief category descriptions

### Step 6: Output Generation
Create a new markdown file with filename `{original_name}_clean.md`.

See [references/OUTPUT_FORMAT.md](references/OUTPUT_FORMAT.md) for the complete output structure.

## Important Rules

1. **Always use web_reader MCP tool** to access link content
2. **Never invent content**: Base all descriptions on actual page content
3. **Preserve original tone**: Match the style of the source document
4. **Resolve ALL redirects**: Ensure final URLs are direct links
5. **Clean beehiiv links**: Always resolve `link.mail.beehiiv.com` to destination
6. **Track failures**: List any links that could not be processed
7. **Generate artifact**: Always create the output markdown file
8. **Minimum description length**: 50 words for auto-generated descriptions

## Tool Selection

- **Link resolution**: web_reader MCP (for content extraction and redirect following)
- **Content fetching**: web_reader MCP
- **File operations**: Native Read/Write tools
- **Web search**: WebSearch for additional context if needed

## Error Handling

If a link cannot be processed:
1. Log the original URL
2. Document the specific error (timeout, 404, redirect loop, etc.)
3. Include in "Link Non Processati" section of output
4. Continue processing remaining links

## Example Workflow

```yaml
Input: "newsletter/en/CY26W3.md"

Step 1: Read file
  → Extract: 15 links found
  → Preserve: Original titles and structure

Step 2: Clean each link
  → Remove: ?utm_source=newsletter&utm_medium=email
  → Resolve: https://link.mail.beehiiv.com/click/... → https://example.com/article
  → Result: 15 clean direct URLs

Step 3: Enhance descriptions
  → 8 links have < 50 words → Fetch content → Generate 50-100 word descriptions
  → 7 links have 50+ words → Keep unchanged

Step 4: Confirm categories
  → Propose 5 default categories (see references/DEFAULT_CATEGORIES.md)
  → User confirms or provides alternatives

Step 5: Organize
  → Analyze content and titles
  → Categorize all 15 links
  → Sort by relevance within categories

Step 6: Generate output
  → Create: "newsletter/en/CY26W3_clean.md"
  → Format: See references/OUTPUT_FORMAT.md
  → Include: List of any unprocessed links (if applicable)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maeste) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
