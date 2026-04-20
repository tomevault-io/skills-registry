---
name: rstudio-theming
description: Generates fully-covered RStudio dark themes from a single primary hex color across all 29 UI regions. Use when asked to "create an RStudio theme", "generate rstheme", "add missing RStudio selectors", "audit rstheme coverage", or "port VS Code theme to RStudio". Produces complete .rstheme CSS files with Ace editor, IDE chrome, terminal, dialog, and extras coverage.
metadata:
  author: awieczork
---

This skill generates comprehensive RStudio `.rstheme` dark themes from a single primary hex color using OKLCH palette derivation and a modular architecture where each UI region is an independent CSS section. The governing principle is complete coverage — every customizable UI region in RStudio must receive themed selectors organized into fine-grained modules so no default light surfaces leak through in dark mode. Begin with `<step_1_derive_palette>` to generate the foundational color scales.


<use_cases>

- Generate a new RStudio dark theme from a primary hex color with full coverage across all 29 UI regions
- Add coverage for missing UI regions (dialogs, terminal, menus) to an existing `.rstheme` that only covers the Ace editor
- Audit an existing `.rstheme` for selector coverage gaps and contrast failures using dual-check verification
- Adapt a VS Code Radiant Teal palette to RStudio format while maintaining cross-editor semantic consistency
- Regenerate a theme after upstream RStudio version changes break selectors or introduce new UI regions

</use_cases>


<module_architecture>

The theme is composed of 12 independent modules, each containing a subset of CSS selectors that change together. Modules are self-contained CSS sections that are independently editable — changing one module does not affect any other. The assembly step (`<step_10_assemble_rstheme>`) concatenates all module sections into the final `.rstheme` file. This mirrors the modular architecture used by the vscode-theming and dbeaver-theming skills.

Load [references/rstudio-selectors.md](references/rstudio-selectors.md) for: the complete selector catalog organized by 29 UI regions, with stability markers and `!important` annotation guidance per selector.

| Module | Purpose | Selector regions | Selectors |
|---|---|---|---|
| **ace-base** | Editor surface — background, foreground, gutter, cursor, selection, brackets, print margin | Region 01 | ~21 |
| **ace-syntax** | All Ace token selectors — keywords, strings, constants, comments, functions, types, operators | Region 02 | ~34 |
| **ace-extras** | Rainbow parentheses, fold widgets, autocomplete popup, markers, find highlights, indent guides | Regions 03, 23–26 | ~35 |
| **chrome-toolbar** | Primary and secondary toolbars, toolbar buttons, progress bars, dividers | Region 04 | ~12 |
| **chrome-menu** | Top menu, dropdown menus, popup panels, suggestion popups, menu item states | Region 05 | ~12 |
| **chrome-tabs** | Editor tabs, pane tabs, active/inactive/hover states, doc tabs, minimized windows | Region 06 | ~15 |
| **chrome-panels** | Status bar, splitters, navigation banner, command palette, base chrome, window frames | Regions 07, 22, 28–29 | ~23 |
| **chrome-panes** | Environment, file browser, packages, help, connections, plots, data viewer | Regions 08–13, 27 | ~26 |
| **chrome-scrollbar** | WebKit scrollbar tracks, thumbs, hover states, Firefox fallback | Region 18 | ~5 |
| **dialogs** | GWT and Bootstrap dialog boxes, buttons, input controls, tabbed dialogs, tree widgets | Regions 19–21 | ~67 |
| **terminal** | xterm.js surface, cursor, selection, 16 ANSI colors, xterm-256 extended palette | Regions 15–17 | ~518 |
| **console** | R console input selection, output tokens, error background, colorless mode | Region 14 | ~5 |

**Module CSS section structure:**

Each module produces a CSS section with a comment header identifying the module. Sections are concatenated during assembly.

```css
/* === Module: ace-base === */
.ace_editor {
  background-color: #1a1b2e;
  color: #e8e6e1;
}
.ace_gutter {
  background: #16171f;
  color: #6b6d7b;
}
```

**Isolation constraint:** Each CSS selector belongs to exactly one module. No selector appears in more than one module section. The `!important` annotation rule is module-level: the ace-syntax module NEVER uses `!important`; all chrome, dialog, terminal ANSI, and required ace-extras selectors use `!important` where indicated by the selector catalog.

</module_architecture>


<workflow>

Execute steps sequentially. Each step produces output that feeds the next — palette feeds semantic mapping, semantic mapping feeds module generation, modules concatenate into the final `.rstheme` file.


<step_1_derive_palette>

Generate six 12-step Radix scales from the primary hex using OKLCH color space via `coloraide`.

Load [color-derivation.instructions.md](../../instructions/color-derivation.instructions.md) for: OKLCH-first derivation rules, Radix 12-step architecture, coloraide usage patterns, and contrast enforcement requirements.

**Required scales:**

| Scale | Purpose | Hue derivation |
|---|---|---|
| **primary** | Accents, selection, cursor, links, active states | Primary hex hue (constant) |
| **neutral** | Backgrounds, borders, text, gutter, surfaces | Primary hue with chroma 0.005–0.02 |
| **red** | Errors, invalid syntax, deletions, git removed | Hue rotation to ~25° |
| **amber** | Warnings, deprecated syntax, modified indicators | Hue rotation to ~70° |
| **green** | Success states, git additions, string literals | Hue rotation to ~145° |
| **blue** | Info states, links, type annotations | Hue rotation to ~245° |

**Derivation rules:**

- Hold hue constant within each scale — vary only L and C across the 12 steps
- Dark mode lightness range: step 1 ~L 0.13–0.15, step 12 ~L 0.93–0.97
- Chroma curve: low (steps 1–2), rising (steps 3–8), peak (steps 9–10), moderate (steps 11–12)
- Apply `coloraide` gamut mapping (`fit('srgb')`) on every OKLCH→sRGB conversion
- Output all hex values in 6-digit lowercase format

</step_1_derive_palette>


<step_2_map_semantic_roles>

Assign palette scale steps to UI semantic roles following the cross-editor mapping.

Load [theme-conventions.instructions.md](../../instructions/theme-conventions.instructions.md) for: the `<semantic_mapping>` table and RStudio-specific format constraints.

**Core role assignments:**

| Semantic role | Scale/step | CSS target |
|---|---|---|
| App background (deepest) | neutral/1 | `.ace_gutter` background |
| Editor background | neutral/2 | `.ace_editor` background |
| Component background | neutral/3 | Toolbar, sidebar, panel backgrounds |
| Hover state | neutral/4 | `.ace_marker-layer .ace_active-line` |
| Selection | primary/4 | `.ace_marker-layer .ace_selection` |
| Subtle border | neutral/6 | Panel dividers, gutter border |
| Default border | neutral/7 | Input borders, dropdown borders |
| Strong border | neutral/8 | Focus rings |
| Accent solid | primary/9 | Active indicators, buttons |
| Accent hover | primary/10 | Cursor, links, hover accents |
| Secondary text | neutral/11 | Line numbers, placeholder text |
| Primary text | neutral/12 | Editor foreground, UI labels |

**Constraint:** Every foreground/background pair formed by these assignments must pass dual-check contrast before proceeding — verify in `<step_8_audit_contrast>`.

</step_2_map_semantic_roles>


<step_3_generate_ace_modules>

Generate the three Ace editor modules: ace-base, ace-syntax, and ace-extras.

Load [references/rstudio-selectors.md](references/rstudio-selectors.md) for: the complete selector listings in `<region_01_ace_editor_chrome>`, `<region_02_ace_syntax_tokens>`, `<region_03_rainbow_parentheses>`, `<region_23_find_replace_bar>`, `<region_24_marker_bar>`, `<region_25_code_folding>`, and `<region_26_autocomplete_popup>`.

**Module: ace-base** — Editor surface and structural selectors (Region 01, ~21 selectors):

- `.ace_editor` background/foreground, `.ace_gutter` background/color, `.ace_cursor` color
- `.ace_marker-layer .ace_selection`, `.ace_marker-layer .ace_active-line`
- `.ace_marker-layer .ace_bracket`, `.ace_selection.ace_start`, `.ace_print-margin`
- `.ace_indent-guide` (use `linear-gradient` background), `.ace_invisible`, `.ace_layer`
- `.ace_marker-layer .ace_foreign_line`, `.ace_marker-layer .ace_active_debug_line`

**Module: ace-syntax** — All token selectors (Region 02, ~34 selectors):

| Token group | Key selectors | Typical scale |
|---|---|---|
| Keywords | `.ace_keyword`, `.ace_keyword.ace_operator` | primary/9, neutral/12 |
| Strings | `.ace_string`, `.ace_string.ace_regex` | green/9 |
| Constants | `.ace_constant`, `.ace_constant.ace_language`, `.ace_constant.ace_numeric` | amber/9 |
| Comments | `.ace_comment` | neutral/11 (italic) |
| Functions | `.ace_support.ace_function`, `.ace_entity.ace_name.ace_function` | blue/9 |
| Types/classes | `.ace_support.ace_class`, `.ace_storage.ace_type` | primary/11 |
| Variables | `.ace_variable`, `.ace_identifier`, `.ace_variable.ace_parameter` | neutral/12 |
| Tags | `.ace_meta.ace_tag`, `.ace_entity.ace_name.ace_tag` | red/9 |
| Attributes | `.ace_entity.ace_other.ace_attribute-name` | amber/11 |
| Invalid | `.ace_invalid`, `.ace_invalid.ace_deprecated` | red/9, amber/9 strikethrough |
| Headings | `.ace_markup.ace_heading` | primary/10 bold |

**Module: ace-extras** — Feature selectors (Regions 03, 23–26, ~35 selectors):

- **Rainbow parentheses** — `.ace_paren_color_0` through `.ace_paren_color_6`, 7 hue-rotated colors (`!important` required)
- **Fold widgets** — `.ace_fold-widget`, `.ace_fold-widget:hover`, open/closed states (no `!important`)
- **Autocomplete** — `.ace_editor.ace_autocomplete` container, active line, hover (`!important` required)
- **Markers** — `.ace_marker-layer .ace_selected-word`, `.ace_step`, error/warning markers
- **Find/search** — `.ace_search`, `.ace_search_field`, `.ace_searchbtn`, IDE search bar (optional `!important`)
- **Chunk backgrounds** — `.ace_marker-layer .ace_foreign_line`, `.ace_marker-layer .ace_find_line`

**Critical constraint:** NEVER use `!important` on Ace syntax selectors (ace-syntax module) — Ace applies specificity correctly and `!important` causes cascading conflicts with RStudio's built-in style management.

</step_3_generate_ace_modules>


<step_4_generate_chrome_modules>

Generate the six IDE chrome modules as independent CSS sections. Each module owns its selectors exclusively — no selector appears in more than one module.

Load [references/rstudio-selectors.md](references/rstudio-selectors.md) for: the complete selector listings in `<region_04_ide_toolbar>` through `<region_13_plots_pane>`, `<region_18_scrollbars>`, `<region_22_splitter_divider>`, `<region_27_data_viewer>`, `<region_28_navigation_banner>`, and `<region_29_miscellaneous>`.

**Modules to generate:**

1. **chrome-toolbar** — Region 04: toolbar wrappers (`.rstheme_toolbarWrapper`, `.rstheme_secondaryToolbar`), toolbar buttons, progress bars, dividers (~12 selectors)
2. **chrome-menu** — Region 05: popup menus (`.popupMiddleCenter`, `.menuPopupMiddleCenter`), menu items, themed popup panels, suggestion popups (~12 selectors)
3. **chrome-tabs** — Region 06: tab layout panels, selected/hover/inactive states, doc tabs, multi-pod tabs, minimized windows (~15 selectors)
4. **chrome-panels** — Regions 07, 22, 28, 29: base chrome background (`.rstudio-themes-flat .rstudio-themes-dark-grey`), status bar, splitters, navigation banner, command palette, window frames (~23 selectors)
5. **chrome-panes** — Regions 08–13, 27: file listing headers, environment/data frame output, data grid headers, DataTables, help search input, data viewer (~26 selectors)
6. **chrome-scrollbar** — Region 18: WebKit scrollbar track, thumb, thumb hover, Firefox `scrollbar-color` and `scrollbar-width` (~5 selectors)

**Critical constraint:** ALWAYS use `!important` on IDE chrome selectors — GWT applies inline styles that override normal CSS specificity. Without `!important`, chrome styles are silently ignored.

**Selector pattern:** Use only stable, documented selector patterns — `.rstheme_` prefixed classes, `#rstudio_` ID selectors, and role-based attribute selectors. Never use obfuscated GWT class names.

</step_4_generate_chrome_modules>


<step_5_generate_dialog_module>

Generate the dialogs module covering all dialog boxes and modal overlays. Missing dialog styles are the most common cause of light surfaces in dark themes.

Load [references/rstudio-selectors.md](references/rstudio-selectors.md) for: the complete selector listings in `<region_19_dialog_boxes>`, `<region_20_buttons>`, and `<region_21_input_controls>`.

**Module scope (~67 selectors):**

- **GWT dialogs** — `.gwt-DialogBox` container, content, caption, buttons, inputs, selects, textareas, checkboxes, radio buttons, tab bars, tree widgets, suggestion popups
- **Bootstrap modals** — `.modal-dialog`, `.modal-content`, `.modal-header`, `.modal-body`, `.modal-footer`, form controls, buttons
- **Dark context variants** — `.rstudio-themes-dark .gwt-DialogBox`, `.rstudio-themes-dark .modal-*` scoped overrides
- **GWT buttons** — `.gwt-Button`, `.gwt-ToggleButton` with hover, active, disabled, and pressed states
- **GWT input controls** — `.gwt-TextBox`, `.gwt-ListBox`, `.gwt-CheckBox`, `.gwt-RadioButton`, focus states

**Critical constraint:** ALWAYS use `!important` on dialog selectors — dialogs render via GWT overlay panels with inline styles.

</step_5_generate_dialog_module>


<step_6_generate_terminal_module>

Generate the terminal module with xterm.js surfaces and ANSI color palette.

Load [references/rstudio-selectors.md](references/rstudio-selectors.md) for: the complete selector listings in `<region_15_terminal>`, `<region_16_ansi_colors>`, and `<region_17_xterm_256_extended>`.

**Terminal surface** — Region 15: `.terminal` background/foreground, `.xterm-cursor-block`, `.xterm-cursor-bar`, `.xterm-cursor-underline`, `.terminal .xterm-selection div`, `.xterm-viewport`

**ANSI 16 colors** — Region 16, map to palette scales:

| ANSI color | Normal scale/step | Bright scale/step |
|---|---|---|
| Black | neutral/1 | neutral/5 |
| Red | red/9 | red/10 |
| Green | green/9 | green/10 |
| Yellow | amber/9 | amber/10 |
| Blue | blue/9 | blue/10 |
| Magenta | primary/9 (hue ~320°) | primary/10 (hue ~320°) |
| Cyan | primary/9 | primary/10 |
| White | neutral/11 | neutral/12 |

**xterm-256 extended palette** (P3 — nice to have) — Region 17: 216-color cube (indices 16–231) and 24-step grayscale ramp (indices 232–255). Total: 480 selectors.

**Critical constraint:** ALWAYS use `!important` on ANSI color and xterm-256 selectors. Terminal surface selectors (`.terminal`, `.xterm-viewport`) do not use `!important`.

</step_6_generate_terminal_module>


<step_7_generate_console_module>

Generate the console module for R console-specific styling.

Load [references/rstudio-selectors.md](references/rstudio-selectors.md) for: the complete selector listing in `<region_14_console>`.

**Module scope (~5 selectors):**

- `#rstudio_console_input .ace_marker-layer .ace_selection` — Console input selection (match editor selection color)
- `#rstudio_console_output .ace_keyword` — Keyword tokens in console output
- `#rstudio_console_output .ace_constant.ace_language` — Language constants in console output
- `.ace_console_error` — Console error message background tint (red/2 background)
- `.nocolor.ace_editor .ace_line span` — Colorless editor mode text (`!important` required)

**Console output differentiation:** Use distinct background tints for standard output (transparent), error output (red/2 background), and message output (subtle neutral/3 background) to help users distinguish R console feedback at a glance.

</step_7_generate_console_module>


<step_8_audit_contrast>

Run WCAG 2.1 AA + APCA dual-check on all foreground/background pairs generated in steps 3–7.

Load [contrast-audit/SKILL.md](../contrast-audit/SKILL.md) for: the complete pair collection, classification, calculation, and reporting workflow.

**Threshold quick reference:**

| Context | WCAG minimum | APCA minimum | APCA preferred |
|---|---|---|---|
| Body text (code, comments) | 4.5:1 | Lc 75 | Lc 90 |
| Large text (headings, titles) | 3:1 | Lc 60 | Lc 75 |
| UI components (buttons, tabs) | 3:1 | Lc 60 | Lc 75 |
| Sub-text (line numbers, hints) | 3:1 | Lc 45 | Lc 60 |
| Non-text (borders, indicators) | 3:1 | Lc 30 | Lc 45 |

**Remediation:** When a pair fails contrast, adjust lightness in OKLCH — NEVER adjust hue or chroma to fix contrast. Increase foreground L or decrease background L until both thresholds pass.

**Radix scale validation:** Verify step 11 vs step 2 achieves Lc >= 60 and step 12 vs step 2 achieves Lc >= 90 for every scale.

</step_8_audit_contrast>


<step_9_validate_css>

Validate the generated CSS for correctness and convention compliance before assembly.

**Syntax validation:**

- Parse all CSS rules — no missing semicolons, no unclosed braces, no invalid property values
- Verify all hex colors match `#[0-9a-f]{6}` (6-digit lowercase, no shorthand)
- Confirm no duplicate selectors with conflicting declarations across modules

**Convention compliance:**

- `!important` present on ALL chrome, dialog, terminal ANSI, and required ace-extras selectors
- `!important` absent from ALL Ace syntax selectors (ace-syntax module)
- No obfuscated GWT class names (patterns like `.gwt-ABC123`, `.GNKJF3BCHB`) — only stable selectors
- No `font-family` or `font-size` declarations — RStudio controls these via user preferences (exception: `@font-face` for Server mode)

**Module completeness:**

- Cross-reference generated selectors against the selector catalog in [references/rstudio-selectors.md](references/rstudio-selectors.md)
- Flag any region from the catalog that has zero selectors in the output
- Verify all 12 modules have been generated — each module's comment header must be present
- Minimum coverage: all 12 modules with at least one selector per mapped region

</step_9_validate_css>


<step_10_assemble_rstheme>

Combine all module CSS sections into a single `.rstheme` file by concatenating module outputs in a fixed order. This mirrors the modular assembly pattern used by vscode-theming and dbeaver-theming — each module is independently generated, then merged into the final output.

**File structure (module assembly order):**

1. Metadata comment header — `rs-theme-name`, `rs-theme-is-dark`, primary hex, version, date
2. **ace-base** module — `.ace_editor` background/foreground, `.ace_gutter`, `.ace_cursor`, selection, active line
3. **ace-syntax** module — all token selectors (keywords, strings, constants, comments, functions, types)
4. **ace-extras** module — rainbow parentheses, fold widgets, autocomplete, markers, find highlights
5. **chrome-toolbar** module — toolbar wrappers and buttons
6. **chrome-menu** module — menus and popup panels
7. **chrome-tabs** module — tab strips and states
8. **chrome-panels** module — status bar, splitters, navigation, base chrome
9. **chrome-panes** module — environment, files, help, plots, packages, data viewer
10. **chrome-scrollbar** module — scrollbar styling
11. **dialogs** module — GWT dialogs, Bootstrap modals, buttons, input controls
12. **terminal** module — xterm.js surface and ANSI colors
13. **console** module — R console I/O styling

**File naming:** `{Theme-Name}.rstheme` — title case with hyphens, `.rstheme` extension.

**Dark mode detection:** RStudio auto-detects dark mode when `.ace_editor` background luminance < 0.2. Ensure the neutral/2 step used for editor background has OKLCH L < 0.20. Include `rs-theme-name` and `rs-theme-is-dark` comments as explicit overrides at the top of the file.

**Installation verification:** After generation, verify the file loads without error via `rstudioapi::addTheme("path/to/Theme.rstheme")`.

</step_10_assemble_rstheme>

</workflow>


<important_rules>

Critical rules that govern all RStudio theme generation. Violations cause silent failures or broken themes.

- **`!important` split rule** — ALWAYS use `!important` on IDE chrome, dialog, and terminal selectors (GWT inline styles override normal specificity). NEVER use `!important` on Ace editor syntax selectors (Ace handles specificity correctly, and `!important` causes cascading conflicts)
- **No obfuscated GWT classes** — NEVER target minified/obfuscated GWT class names like `.gwt-ABC123` or `.GNKJF3BCHB`. These change between RStudio versions and break themes on update. Use only `.rstheme_` prefixed classes, `.ace_` classes, `.xterm` classes, `.rstudio-themes-dark-menus`, and officially documented selectors
- **Dark mode detection** — RStudio auto-detects dark mode when `.ace_editor` background has luminance < 0.2. Ensure neutral/2 has OKLCH L < 0.20. Include `rs-theme-name` and `rs-theme-is-dark` comments as explicit overrides at the top of the file, even though RStudio auto-detects dark mode from luminance
- **No font styling** — `font-family` and `font-size` are controlled by RStudio user preferences, not CSS. Do not set them in `.rstheme` (exception: `@font-face` declarations work in RStudio Server mode only)
- **OKLCH-only derivation** — All colors derive from the primary hex via OKLCH. Cross-reference [color-derivation.instructions.md](../../instructions/color-derivation.instructions.md) for pipeline rules. No hardcoded hex values anywhere
- **Selector catalog** — The complete selector reference is in [references/rstudio-selectors.md](references/rstudio-selectors.md). JIT-load this file when mapping selectors per UI region — do not memorize or inline the full catalog
- **Hex format** — All color values in 6-digit lowercase hex with `#` prefix. No shorthand, no uppercase, no named colors, no alpha channels (RStudio CSS does not support `#rrggbbaa`)

</important_rules>


<pitfalls>

Common mistakes that cause broken or incomplete RStudio themes.

- **Missing `!important` on GWT chrome** — Styles are silently ignored because GWT applies inline styles with higher specificity. The theme appears to partially work but chrome surfaces remain default. Fix: add `!important` to every non-Ace selector
- **Using obfuscated GWT classes** — Theme works on one RStudio version but breaks on the next update when GWT recompiles and reassigns class names. Fix: use only stable `.rstheme_`, `.ace_`, and documented selectors
- **Hardcoding colors instead of deriving from palette** — Produces an inconsistent theme where some surfaces feel disconnected. Fix: trace every hex value back through the OKLCH pipeline from the primary hex
- **Missing dialog box selectors** — The most visible dark theme failure: opening Preferences or Install Packages reveals a white modal over a dark editor. Fix: always include the ~67 dialog selectors from `<step_5_generate_dialog_module>`
- **Untested terminal ANSI colors** — Certain ANSI color combinations produce invisible text (e.g., dark blue on dark background). Fix: verify all 16 ANSI colors against the terminal background with dual-check contrast
- **Setting `font-family` in CSS** — Conflicts with the user's RStudio font preferences, causing unexpected font changes or fallback to system fonts. Fix: let RStudio manage fonts via its Appearance preferences
- **Using HSL for palette math** — HSL has non-uniform lightness perception, producing visible hue shifts at low lightness values typical of dark themes. Fix: use OKLCH for all derivation, convert to sRGB only for final hex output

</pitfalls>


<error_handling>

- If a contrast check fails on a foreground/background pair, then adjust the foreground lightness (L) in OKLCH — increase L for light-on-dark, decrease L for dark-on-light. Never adjust hue or chroma to fix contrast. Re-run dual-check after adjustment
- If a selector from the catalog is not found in the target RStudio version, then flag it as version-dependent, wrap it in a comment noting the minimum version, and exclude it from P1 coverage requirements
- If CSS validation reports a syntax error, then check for missing semicolons, unclosed braces, or invalid property values. Common in generated CSS: missing semicolon on last property before closing brace
- If the `.rstheme` file fails to load via `rstudioapi::addTheme()`, then verify: file has `.rstheme` extension, CSS parses without syntax errors, no BOM characters at file start, file uses UTF-8 encoding
- If the editor background is not detected as dark mode, then check that neutral/2 has OKLCH L < 0.20 — if it is marginally above 0.20, reduce L by 0.01 increments until dark mode triggers
- If a UI region has no stable selectors available, then return BLOCKED with the region name and RStudio version — do not fabricate selectors

</error_handling>


<validation>

**P1 — Blocking** (must pass before delivery):

- All 12 modules generated with selectors matching their mapped regions from `<module_architecture>`
- All Ace syntax token selectors present (keyword, string, constant, comment, function, type, operator, variable, tag, attribute, support, invalid, deprecated)
- `!important` convention correct: present on all chrome/dialog/terminal ANSI selectors, absent from all Ace syntax selectors
- No obfuscated GWT class names in any selector
- All foreground/background pairs pass WCAG 2.1 AA + APCA dual-check
- `.ace_editor` background has OKLCH L < 0.20 (dark mode detection)
- All hex values are 6-digit lowercase format
- No hardcoded colors — every value traces to the OKLCH pipeline

**P2 — Quality** (should pass):

- All dialog box categories covered (preferences, new file, git, install packages, search/replace)
- Terminal ANSI 16 colors complete with all normal/bright pairs
- Rainbow parentheses included with 7 hue-rotated colors
- Metadata comment header present with theme name, version, primary hex, and date
- Radix scale step 11 vs step 2 achieves Lc >= 60 for all scales
- Radix scale step 12 vs step 2 achieves Lc >= 90 for all scales

**P3 — Polish** (nice to have):

- xterm-256 extended palette (216-color cube + 24-step grayscale)
- Navigation banner and breadcrumb styling
- Indent guides with subtle visual treatment
- Fold widgets with themed expand/collapse indicators
- Body text pairs reach preferred APCA Lc 90 (not just minimum Lc 75)
- Console output differentiation (standard, error, message styling)

</validation>


<resources>

- [color-derivation.instructions.md](../../instructions/color-derivation.instructions.md) — OKLCH pipeline rules, Radix scale architecture, coloraide usage, contrast enforcement
- [theme-conventions.instructions.md](../../instructions/theme-conventions.instructions.md) — `<semantic_mapping>` table, RStudio format constraints, file naming conventions
- [contrast-audit/SKILL.md](../contrast-audit/SKILL.md) — Contrast verification workflow: pair collection, classification, WCAG + APCA calculation, gap detection, reporting
- [references/rstudio-selectors.md](references/rstudio-selectors.md) — Complete CSS selector catalog organized by 29 UI regions with stability markers and `!important` guidance (JIT-load when generating module content)
- [tools/generate_rstheme.py](../../tools/generate_rstheme.py) — Reference implementation: OKLCH palette derivation → module-based CSS generation (~2100 lines)
- [extensions/rstudio/Dark-Radiant-Teal.rstheme](../../extensions/rstudio/Dark-Radiant-Teal.rstheme) — Reference output: complete themed `.rstheme` with 753 selectors across all UI regions

</resources>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/awieczork) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
