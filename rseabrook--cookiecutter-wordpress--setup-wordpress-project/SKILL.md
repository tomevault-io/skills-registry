---
name: setup-wordpress-project
description: Initialize git, configure theme colors, fonts, and custom block styles for a WordPress project Use when this capability is needed.
metadata:
  author: rseabrook
---

# Setup WordPress Project

This skill performs initial setup for a WordPress theme project. It handles:
1. Git repository initialization
2. Theme colors configuration
3. Font setup
4. Custom block styles

## Prerequisites: Find the Theme Directory

Before proceeding, you must locate the custom theme directory. Look in `wp-content/themes/` for a directory that is NOT one of these default WordPress themes:
- `twentytwentyone`
- `twentytwentytwo`
- `twentytwentythree`
- `twentytwentyfour`
- `twentytwentyfive`

The remaining directory is the custom theme. Use this theme directory name for all paths below, referred to as `{theme-slug}`.

Also determine the text domain for the theme by converting the theme slug to use underscores instead of hyphens (e.g., `my-theme` becomes `my_theme`). This is used for translation functions in PHP.

## File Locations

- **style.css**: `wp-content/themes/{theme-slug}/style.css`
- **blocks.css**: `wp-content/themes/{theme-slug}/assets/css/blocks.css`
- **theme.json**: `wp-content/themes/{theme-slug}/theme.json`
- **functions.php**: `wp-content/themes/{theme-slug}/functions.php`
- **Fonts directory**: `wp-content/themes/{theme-slug}/assets/fonts/`

## Instructions

Execute each step in order.

---

### Step 1: Initialize Git Repository

Check if the current directory is a git repository by running `git status`.

- If it **is** a git repository: Skip to Step 2
- If it **is not** a git repository: Run `git init` to initialize one, then proceed to Step 2

---

### Step 2: Setup Theme Colors

Ask the user to provide all their theme colors in a single message using the AskUserQuestion tool.

**Format:** Colors can be provided as:
- One color per line: `Color Name: #hexcode`
- Comma-separated: `Color Name: #hexcode, Another Color: #hexcode`
- Or a mix of both

**Parsing rules:**
1. Split the input by commas and/or newlines to get individual color entries
2. For each entry, split by colon (`:`) to separate the color name from the hex code
3. Trim whitespace from both the name and hex code
4. The hex code starts with `#` followed by 3 or 6 hex characters

**Update the following files:**

#### style.css
Find the `:root` section within the "Variables" comment block. Replace the color variables (remove any example colors and TODO comments) with:
```css
--theme-color-{slug}: {hex};
```
Where `{slug}` is the color name in lowercase with spaces replaced by hyphens.

#### blocks.css
Find the `:root` section at the top. Replace the color variables (remove any example colors and TODO comments) with the same format as style.css.

#### theme.json
Find the `settings.color.palette` array. Replace the entire array contents with:
```json
{
    "slug": "{slug}",
    "color": "{hex}",
    "name": "{name}"
}
```

---

### Step 3: Setup Fonts

First, scan the `wp-content/themes/{theme-slug}/assets/fonts/` directory to find all `.woff2` files (excluding the icon font `gadabout-icons.woff2`).

For each font file found, determine:
- **Font family name**: Parse from the filename (e.g., `CormorantGaramond-Regular.woff2` -> `Cormorant Garamond`)
- **Font style**: `italic` if filename contains "Italic", otherwise `normal`
- **Slug**: Font family name in lowercase with spaces replaced by hyphens

Group font files by font family (regular and italic variants belong together).

**Update the following files:**

#### style.css
In the "Fonts" comment section, add `@font-face` declarations for each font file. Remove any example fonts and TODO comments, but keep the `gadabout-icons` font-face declaration.

Format:
```css
@font-face {
    font-family: '{Font Family Name}';
    src: url('assets/fonts/{filename}.woff2') format('woff2');
}
@font-face {
    font-family: '{Font Family Name}';
    font-style: italic;
    src: url('assets/fonts/{filename-italic}.woff2') format('woff2');
}
```

In the `:root` section, add font variables (remove example font variables and TODO comments, but keep `--font-icons`):
```css
--font-{slug}: '{Font Family Name}';
```

#### blocks.css
In the `:root` section, add the same font variables as style.css (remove examples and TODO comments, keep `--font-icons`).

#### theme.json
In the `settings.typography.fontFamilies` array, replace any example font families with entries for each font. Format:
```json
{
    "name": "{Font Family Name}",
    "slug": "{slug}",
    "fontFamily": "'{Font Family Name}', serif",
    "fontFace": [
        {
            "fontFamily": "{Font Family Name}",
            "fontWeight": "300 800",
            "fontStyle": "normal",
            "fontStretch": "normal",
            "src": [ "file:./assets/fonts/{filename}.woff2" ]
        },
        {
            "fontFamily": "{Font Family Name}",
            "fontWeight": "300 800",
            "fontStyle": "italic",
            "fontStretch": "normal",
            "src": [ "file:./assets/fonts/{filename-italic}.woff2" ]
        }
    ]
}
```

---

### Step 4: Setup Custom Block Styles

Ask the user to provide all their custom block styles in a single message using the AskUserQuestion tool.

**Input format:** The user will describe styles casually, as they might read them from an Illustrator file. Expect informal descriptions like:

```
Cormorant Garamond 14px, letter spacing 1px
Montserrat Bold 18px on desktop, 14px on mobile
Playfair Display 24px, line height 1.2, uppercase
```

**Parsing rules:**

1. **Block type**: Defaults to `paragraph` unless explicitly specified (e.g., "heading", "h2", "image")

2. **Label**: Use what the user provides as-is (e.g., "Cormorant Garamond 14px")

3. **Slug/name**: Derive from the label by converting to lowercase and replacing spaces with hyphens (e.g., "cormorant-garamond-14px")

4. **Mobile vs Desktop terminology**:
   - "desktop" or "on desktop" = 768px breakpoint (`@media screen and (min-width: 768px)`)
   - "mobile" or "on mobile" = base/global CSS (no media query)
   - If user says "14px on desktop, 12px on mobile" -> mobile font-size is 12px, 768px breakpoint font-size is 14px

5. **Always create boilerplate for 768px breakpoint**: Even if the user doesn't explicitly specify desktop/mobile differences, create empty or duplicated CSS for both the base and the 768px media query so the structure is ready for future tweaks.

6. **CSS properties**: Interpret casual descriptions:
   - "letter spacing 1px" -> `letter-spacing: 1px;`
   - "line height 1.2" -> `line-height: 1.2;`
   - "uppercase" -> `text-transform: uppercase;`
   - "bold" -> `font-weight: bold;`
   - Font names should use the CSS variable if available (e.g., "Cormorant Garamond" -> `var(--font-cormorant-garamond)`)

7. **Sorting**: Sort all block styles in this order:
   - First, alphabetically by font family name (e.g., Copperplate before Dantiane before Stylebender before Sweet Sans)
   - Within each font family, sort by font size from smallest to largest
   - This sorting applies to both functions.php registrations and blocks.css declarations
   - Add a comment with the font family name before each group in functions.php (e.g., `// Copperplate`)

**Update the following files:**

#### functions.php
Find the function that registers block styles (look for a function containing `register_block_style` calls). Replace any example `register_block_style()` calls with new ones for each style (sorted as described above):
```php
// {Font Family}
register_block_style( 'core/{block}', array(
    'name' => '{slug}',
    'label' => __( '{Label}', '{text_domain}' ),
) );
```

Where `{text_domain}` is the theme's text domain (the theme slug with hyphens replaced by underscores).

#### blocks.css
After the `:root` section, add CSS for each block style (sorted to match functions.php). Remove any example block style CSS.

Format (mobile-first, always include 768px breakpoint):
```css
/* {Label} */
.entry-content {block-selector}.is-style-{slug},
.editor-styles-wrapper {block-selector}.is-style-{slug} {
    font-family: var(--font-{font-slug});
    font-size: {size}px;
    letter-spacing: {spacing};
    /* line-height only if specified */
}

@media screen and (min-width: 768px) {
    .entry-content {block-selector}.is-style-{slug},
    .editor-styles-wrapper {block-selector}.is-style-{slug} {
        font-size: {size}px;
        /* line-height only if specified */
    }
}
```

**IMPORTANT - Media query CSS rules:**
- Only include `font-size` in media query blocks (and `line-height` if specified)
- Do NOT repeat `font-family` or `letter-spacing` in media queries - these properties don't change between breakpoints

Where `{block-selector}` is:
- `p` for paragraph (default)
- `h1`, `h2`, etc. for headings
- `.wp-block-image` for image

If the user specifies additional breakpoints beyond 768px, include those as well:
- 576px: `@media screen and (min-width: 576px)`
- 992px: `@media screen and (min-width: 992px)`
- 1200px: `@media screen and (min-width: 1200px)`
- 1400px: `@media screen and (min-width: 1400px)`

---

### Step 5: Confirm Completion

After completing all steps, show the user a summary:
- Whether git was initialized or already existed
- List of colors added
- List of fonts configured
- List of custom block styles created
- Confirm which files were updated

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rseabrook) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
