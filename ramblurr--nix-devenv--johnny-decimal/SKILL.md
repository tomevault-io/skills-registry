---
name: johnny-decimal
description: Apply Johnny.Decimal organization system when sorting, filing, organizing, or categorizing information. Use when creating or maintaining a structured numbering scheme for files, projects, or any hierarchical data. Enforces the strict containment rules and format patterns required by the Johnny.Decimal specification. Use when this capability is needed.
metadata:
  author: ramblurr
---

# Johnny.Decimal Organization System

Johnny.Decimal is a structured numbering scheme for organizing information with the hierarchy:
System → Area → Category → ID

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

---

## Types

Each record in a Johnny.Decimal system has a type. Type values MUST be lowercase:

| Type     | Value      |
|----------|------------|
| System   | `system`   |
| Area     | `area`     |
| Category | `category` |
| ID       | `id`       |

---

## Titles

Systems, areas, categories, and IDs MUST each have a title.

- MUST contain at least 1 character
- MUST NOT exceed 255 characters
- MAY contain any printable Unicode characters

---

## Systems

A system is a contained collection of areas, categories, and IDs.

### System Identifier

A system identifier is OPTIONAL.

If present, the system identifier:
- MUST match the pattern `[A-Z][0-9][0-9]`
- Valid range: `A00` through `Z99`
- MUST be unique across all systems in scope

### Constraints

- A system without an identifier is valid
- A system MAY contain zero or more areas

---

## Areas

An area is a high-level grouping of categories. Areas represent broad domains within a system.

### Format

An area identifier:
- MUST match the pattern `[0-9]0-[0-9]9` where both digits are identical
- Valid values: `00-09`, `10-19`, `20-29`, `30-39`, `40-49`, `50-59`, `60-69`, `70-79`, `80-89`, `90-99`

### Constraints

- An area MUST be unique within its system
- An area MAY contain zero or more categories
- An area MUST only contain categories whose first digit matches the area's first digit
  - Area `10-19` MAY contain categories `10` through `19`
  - Area `10-19` MUST NOT contain category `20`

---

## Categories

A category is a grouping of IDs. Categories represent a specific domain of work or collection of related items.

### Format

A category identifier:
- MUST match the pattern `[0-9][0-9]`
- Valid range: `00` through `99`

### Constraints

- A category MUST be unique within its system
- A category MUST belong to exactly one area
- A category MUST be contained within the area whose range includes the category number
  - Category `11` MUST belong to area `10-19`
  - Category `11` MUST NOT belong to area `20-29`
- A category MAY contain zero or more IDs
- A category MUST NOT exist without a parent area

---

## IDs

An ID is the fundamental unit of organisation in a Johnny.Decimal system. An ID represents a single project, topic, or collection of related items.

### Format

An ID:
- MUST match the pattern `[0-9][0-9].[0-9][0-9]`
- Valid range: `00.00` through `99.99`

The portion before the decimal is the "category component". The portion after the decimal is the "ID component".

### Constraints

- An ID MUST be unique within its system
- An ID MUST belong to exactly one category
- An ID MUST be contained within the category matching its category component
  - ID `15.52` MUST belong to category `15`
  - ID `15.52` MUST NOT belong to category `16`
- An ID MUST NOT exist without a parent category

---

## Metadata

Metadata is a collection of key/value pairs attached to an ID.

### Applicability

- Metadata MAY be attached to IDs
- Metadata MUST NOT be attached to systems, areas, or categories

### Standard Zeros

IDs are the leaf nodes of a Johnny.Decimal system and the only place where data exists. Metadata about higher-level structures is stored using standard zeros:

| Structure     | Standard Zero |
|---------------|---------------|
| System        | `00.00`       |
| Area `20-29`  | `20.00`       |
| Category `21` | `21.00`       |

To store metadata about category `21`, attach it to ID `21.00`. To store metadata about area `20-29`, attach it to ID `20.00`. To store metadata about the system itself, attach it to ID `00.00`.

### Keys

A metadata key:
- MUST contain at least 1 character
- MUST match the pattern `[a-zA-Z][a-zA-Z0-9_]*`
- MUST be unique within the metadata of a single ID

### Reserved Keys

The following keys are reserved and have defined semantics:

| Key           | Type                   | Description                                |
|---------------|------------------------|--------------------------------------------|
| `description` | string                 | A human-readable description of the ID     |
| `relatesTo`   | array of ID references | References to other IDs in the same system |
| `url`         | array of URIs          | External resources associated with this ID |

Implementations MUST validate reserved keys according to their type definitions.

### description

The `description` value:
- MUST be a string
- MAY be of arbitrary length
- Is interpreted as GitHub-Flavored Markdown (GFM). Plain text without formatting is valid.

### relatesTo

The `relatesTo` value:
- MUST be an array
- Each element MUST be a string matching the ID format (`[0-9][0-9].[0-9][0-9]`)
- Each referenced ID SHOULD exist in the same system
- Relationships are one-way; implementations MAY derive backlinks

### url

The `url` value:
- MUST be an array
- Each element MUST be a valid URI (per RFC 3986)

### User-defined Keys

Keys not listed as reserved MAY be used freely by users.

User-defined keys SHOULD use camelCase for consistency with reserved keys.

### Values

A metadata value:
- MUST be a valid JSON value (string, number, boolean, array, or object)
- MUST NOT be null

---

## Plain-Text Index File Format

The recommended index file format is plain text, saved as `00.00 Index.txt` in your system.

### Example

```text
10-19 Your first area's title
   11 Your first category's title
      11.01 Your first ID's title
      11.02 The second ID in category 11
   12 Category twelve
20-29 Your second area
   21 Category twenty-one
      21.01 ...and so on
```

### Ordering

The index file MUST appear in order. This is incorrect:

```text
20-29 Second area
10-19 First area
```

### Parents May Be Childless; Orphans Are Disallowed

This is fine:

```text
10-19 An area with no children
20-29 Another area
   21 A category with no children
```

This is not:

```text
11 A category without a parent area
21.01 An ID without a parent category
```

### White Space

While indentation is encouraged for legibility, white space should be ignored by a parser. This is legal, if ugly:

```text
 10-19 Your first area's title

11 Your first category's title
               11.01 The title of your first ID
```

### Comments

JavaScript comments are allowed. Multi-line comments may be used if they are the only text on the line.

```text
10-19 My area     // which I can comment like this
   11 My category /* or like this */
   /* multiline comments
      are allowed on their own lines
    */
   11.01 Whereas this /* is not, as it breaks
                         the ID
                       */
```

### Metadata in Plain-Text

Arbitrary metadata may be stored in key/value pairs directly below any item by entering:

- A dash
- A space
- The key name
- A colon
- A space
- The value

The value may not span a newline. All values are strings.

```text
10-19 Area
   11 Category
      11.01 ID
      - Location: work email.
```

---

## Quick Validation Checklist

When creating or validating a Johnny.Decimal structure:

- [ ] Does each category's first digit match its area?
- [ ] Does each ID's category component match its category?
- [ ] Does every category have a parent area defined?
- [ ] Does every ID have a parent category defined?
- [ ] Is each identifier unique within its scope?
- [ ] Does every item have a title (1-255 chars)?
- [ ] Is the index file in proper order?
- [ ] Are all metadata keys valid (`[a-zA-Z][a-zA-Z0-9_]*`)?
- [ ] Are reserved metadata keys used correctly (description, relatesTo, url)?

---

## Summary Table

| Type     | Pattern                     | Example   | Valid Range              | Parent Required |
|----------|-----------------------------|-----------|--------------------------|-----------------|
| System   | `[A-Z][0-9][0-9]`           | `A01`     | A00–Z99 (optional)       | No              |
| Area     | `[0-9]0-[0-9]9`             | `10-19`   | 00-09 through 90-99      | System          |
| Category | `[0-9][0-9]`                | `11`      | 00–99                    | Area            |
| ID       | `[0-9][0-9].[0-9][0-9]`     | `11.01`   | 00.00–99.99              | Category        |

---

## References

- [Johnny.Decimal Website](https://johnnydecimal.com/) - Official documentation and guides
- [Index Specification](https://github.com/johnnydecimal/index-spec) - Formal specification for the Johnny.Decimal index

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ramblurr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
