---
name: kb-search
description: > Use when this capability is needed.
metadata:
  author: GoogleCloudPlatform
---

The `fileskb` mcp server provides the following tools to extract relevant
information from a directory hierarchy of markdown files:

* **list_contents** - browse and navigate the directory tree to list the contents
  of the specified path. The items may be files or sub-directories.

* **read_file** - read the contents of a file in the knowledge base. The entire
  contents are provided. Extract and summarise the relevant information based on
  the documentation being generated..

* **search_content** - searches the knowledge base and returns the matching files,
  along with matching line numbers and line snippets. This can be used to quickly
  find matches without having to list and read all files.

---
> Source: [GoogleCloudPlatform/dataplex-labs](https://github.com/GoogleCloudPlatform/dataplex-labs) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
