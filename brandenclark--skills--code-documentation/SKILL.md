---
name: code-documentation
description: Use when user asks for documentation, javadocs, docstrings, or comments on source files - adds idiomatic docs per language, removes anti-patterns like @author/@since, preserves existing license headers, skips trivial getters/setters/constructores, and provides readability suggestions.
metadata:
  author: brandenclark
---

<skill_overview>
Add idiomatic documentation to source files; remove anti-patterns; never change code behavior.
</skill_overview>

<rigidity_level>
LOW FREEDOM - Follow the process steps and anti-pattern rules exactly. Documentation content adapts to codebase context.
</rigidity_level>

<quick_reference>
| Step | Action | Verify |
|------|--------|--------|
| 1 | Read file + related types | Understand context before writing |
| 2 | Identify language | Select correct doc format |
| 3 | Remove anti-patterns | No @author, @since; preserve existing license headers; remove trivial getter/setter/constructor docs |
| 4 | Document: class > constructors > public > protected > pkg-private | Skip private unless complex |
| 5 | Edit tool per doc block | Never Write whole file |
| 6 | Final pass | Re-read file, confirm no anti-patterns remain |
| 7 | Readability suggestions | List improvements that reduce need for comments |

**NEVER:** Change code behavior, add `@author`/`@since`, document trivial getters/setters/constructors, add new license headers, remove existing license/copyright headers.
</quick_reference>

<when_to_use>

- User asks for documentation, javadocs, docstrings, comments on files
- Existing docs are incomplete, wrong format, or contain anti-patterns
- User says "document this file/class/module"
  </when_to_use>

<the_process>

## Documentation Formats by Language

- **Java:** Javadoc (`/** */` with `@param`, `@return`, `@throws`, `@see`, `{@link}`, `{@code}`)
- **Python:** Google-style docstrings (`Args:`, `Returns:`, `Raises:`)
- **TypeScript/JavaScript:** JSDoc (`/** */` with `@param`, `@returns`, `@throws`)
- **Go:** Godoc (`//` blocks starting with declaration name)
- **Rust:** Rustdoc (`///` with markdown, `# Examples`/`Errors`/`Panics`)
- **C/C++:** Doxygen (`/** */` with `@param`, `@return`, `@brief`)
- **C#:** XML doc comments (`/// <summary>`, `<param>`, `<returns>`)
- **Ruby:** YARD | **Kotlin:** KDoc | **Swift:** DocC | **PHP:** PHPDoc
- **Other:** Research community-standard format before writing

## Anti-Patterns to REMOVE (Always)

These MUST be removed if present. Never add them.

- `@author` tags (git tracks authorship)
- `@since` tags (git tracks history)
- `@version` tags (git tracks versions)
- Documentation on trivial getters/setters/constructors (see below)

**Trivial getter/setter rule:** If the body is just `return this.x` or `this.x = x` with no other logic, DELETE any docs on it. The method name IS the documentation. Only document getters/setters that have: validation, lazy initialization, side effects, computed values, or non-obvious behavior.

**Trivial constructor rule:** If the constructor only assigns parameters to fields with no additional logic, DELETE any docs on it. The constructor signature IS the documentation. Only document constructors that have: validation, complex initialization, side effects, or non-obvious behavior.

**"But the team lead wrote those tags"** - Remove them. Git is the source of truth for authorship and history.

## Copyright/License Headers

**Preserve existing copyright/license headers.** Some files contain headers required by legal obligations (third-party code, specific licenses). Never remove them. Never add new ones either — if a file has no copyright header, leave it that way.

## What to Document

**Class/file level:** Purpose, responsibilities, behaviors, configuration, lifecycle, relationships. Use structured sections (`<p>`, `<ul>`) for complex classes.

**Constructors:** What the object is configured for, required vs optional params, defaults, throws.

**Methods:** WHAT and WHY, not HOW. Document params (types, constraints, nullability, defaults), return values (including edge cases like null), exceptions, and add cross-references with `{@link}` or `@see`.

**Overrides:** Document THIS override's specific behavior, not just `{@inheritDoc}`. What does THIS implementation do differently?

**Skip:** Private methods (unless complex logic warrants it), trivial getters/setters/constructors.

## Inline Comments (Only When Necessary)

**DO add for:** Non-obvious business logic, workarounds with ticket references, magic numbers, counter-intuitive control flow, intentional performance tradeoffs.

**DO NOT add for:** Self-explanatory code, simple assignments, standard patterns, anything already covered by method docs.

## Process Steps

**Step 1: Read and understand.** Read the entire file. Read related files (parent classes, interfaces, key types) to understand context. NEVER document without reading first.

**Step 2: Identify language.** Select the correct documentation format from the list above.

**Step 3: Remove anti-patterns FIRST.** Delete `@author`, `@since`, `@version` tags, and docs on trivial getters/setters/constructors. Preserve existing copyright/license headers. Do this BEFORE adding new docs.

**Step 4: Document in order.** Class-level, then constructors, then public methods, then protected, then package-private. Skip private unless complex. Skip trivial getters/setters/constructors.

**Step 5: Use Edit tool per doc block.** Never use Write to replace the entire file. Each documentation addition is a separate Edit.

**Step 6: Final anti-pattern pass.** Re-read the file after all edits. Confirm: no `@author`, no `@since`, no `@version`, no docs on trivial getters/setters/constructors, existing license headers preserved. Fix any that remain.

**Step 7: Readability suggestions.** After completing docs, suggest (DO NOT implement) improvements that would reduce the need for comments: variable/param renames, method extractions, constant extractions, type improvements. Present as a summary list.

## Scope Boundaries

**ONLY documentation changes.** Never modify code behavior, rename variables, extract methods, add/remove imports, or fix compilation errors. If you find code issues, mention them in readability suggestions.
</the_process>

<examples>
<example>
<scenario>Agent documents trivial getters/setters</scenario>

<code>
/**
 * Gets the name.
 * @return the name
 */
public String getName() {
    return this.name;
}
</code>

<why_it_fails>

- `getName()` returning `this.name` is self-documenting
- The javadoc adds zero information
- Clutters the file with noise
- Makes meaningful docs harder to find
  </why_it_fails>

<correction>
Delete the javadoc entirely. The method signature IS the documentation.

Only document if the getter has real logic:

```java
/**
 * Returns the display name, falling back to the username if no
 * display name has been set.
 */
public String getDisplayName() {
    return displayName != null ? displayName : username;
}
```

</correction>
</example>

<example>
<scenario>Agent preserves @author tags or removes license headers</scenario>

<code>
/**
 * Copyright (c) 2024 Acme Corp. MIT License.
 */

/\*\*

- Service for processing orders.
- @author John Smith
- @since 1.0
  \*/
  public class OrderService { ... }
  </code>

<why_it_fails>
Two opposite mistakes agents make:

1. Preserving @author/@since (git tracks these, remove them)
2. Removing copyright/license headers (may be legally required, preserve them)
   </why_it_fails>

<correction>
Remove @author and @since. KEEP the license header:
```java
/**
 * Copyright (c) 2024 Acme Corp. MIT License.
 */

/\*\*

- Service for processing orders.
- ...
  \*/
  public class OrderService { ... }

```
</correction>
</example>

<example>
<scenario>Agent uses Write tool for entire file instead of Edit per block</scenario>

<code>
Write tool → entire file with all docs added at once
</code>

<why_it_fails>
- Risk of accidentally changing code
- Harder for user to review what changed
- Can introduce whitespace/formatting drift
- Loses the ability to approve doc-by-doc
</why_it_fails>

<correction>
Use Edit tool for each doc block addition:
```

Edit: add class-level javadoc
Edit: add constructor javadoc
Edit: add createOrder javadoc
Edit: remove @author tag
...

```
Each edit is reviewable independently.
</correction>
</example>
</examples>

<critical_rules>
## Rules With No Exceptions

1. **Read before documenting** - Never write docs without reading the file AND related types
2. **Remove anti-patterns FIRST** - @author, @since, @version, trivial getter/setter/constructor docs go away before adding anything
2b. **Preserve existing license/copyright headers** - May be legally required; never remove or add them
3. **Never document trivial getters/setters/constructors** - `return this.x` and `this.x = x` need no docs
4. **Edit tool per doc block** - Never Write the entire file
5. **Final anti-pattern pass** - Re-read after all edits, confirm none remain
6. **Never change code** - Only documentation changes, never behavior
7. **Readability suggestions at the end** - Suggest but don't implement

## Common Rationalizations

All of these mean: **STOP. Follow the rules.**

- "The team lead wrote those @author tags" (Remove them. Git is the source of truth.)
- "I should document every public method" (Not trivial getters/setters/constructors.)
- "I'll use Write to be thorough" (Use Edit per block. Always.)
- "The docs are done, no need for a final pass" (Re-read. Check for anti-patterns.)
- "Readability suggestions aren't necessary" (Always provide them.)
- "This copyright header is unnecessary" (Keep it. Legal may require it. Never remove.)
- "I should add a license header" (Never add new ones. Only preserve existing.)
</critical_rules>

<verification_checklist>
Before claiming documentation is complete:

- [ ] Read file and related types before writing any docs
- [ ] Correct documentation format for the language
- [ ] No `@author` tags anywhere in file
- [ ] No `@since` tags anywhere in file
- [ ] No `@version` tags anywhere in file
- [ ] Existing copyright/license headers preserved (not removed or added)
- [ ] No docs on trivial getters/setters/constructors (body is just `return this.x` or `this.x = x`)
- [ ] Class-level doc present with purpose and key behaviors
- [ ] All public/protected methods documented (except trivial getters/setters/constructors)
- [ ] Used Edit tool per doc block (not Write for whole file)
- [ ] Final pass completed - re-read file confirming no anti-patterns
- [ ] Readability suggestions provided
- [ ] No code changes made (only documentation)
</verification_checklist>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brandenclark) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
