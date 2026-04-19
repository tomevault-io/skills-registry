---
name: sql-database-documentation
description: Use when working with the ability to take in a SQL-Dump and create a Markdown-Notebook for each found table
metadata:
  author: rocco-gossmann
---

I want you to:
- Extract all the table creation statements in the `*.sql` file you are given.
     - if you are not given an sql file, ask the user for one.
     - if you are given an sqlite database extract the .schema from it and use that instead of the `*.sql` file.

- all files you will create, need to be put into a specific folder, that you get by the user
  - if the user did not give you a folder, ask for one.
  - should the folder not exist. you are allowed to create it by using bash commands.

- create a `tablename.md` file, in that folder, foreach found "create table" of the `*.sql` .

- after you are done with any one table, remove all the HTML-Comments from the generated markdown file

The Markdown file must have the following structure.

```markdown

# [TableName]

<!--
  IF the table has a comment attached. then write that comment here.
-->

## Columns:
<!--
  List all the columns below
  if the column does not have a comment attached, then leave Column Description empty.
  else the comment is the columns description
  Do not create any description yourself. only the columns comment is allowed as description.
-->
| Column | ColumnType | Column Description |
| - | - | - |

## References

<!--
  List of foreign key columns
  I want you to link every TargetTable to it's respective `tablename.md` file
  If there is no foreign keys, remove this section
-->

| ForginKey | TargetTable | TargetColumn |
| - | - | - |

## Indexes
<!--
  List all Indexes
  if there is no indexes, remove this section
-->

- [index 1] (Primary, Unique, etc..)
- [index 2] (Primary, Unique, etc..)

```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rocco-gossmann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
