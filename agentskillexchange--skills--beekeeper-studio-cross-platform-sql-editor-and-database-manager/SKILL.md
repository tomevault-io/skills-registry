---
name: beekeeper-studio-cross-platform-sql-editor-and-database-manager
description: Use when working with a source-backed ASE skill for Beekeeper Studio, the SQL editor and database manager for Linux, macOS, and Windows. It fits workflows that need a real client for querying, browsing tables, and working across PostgreSQL, MySQL, SQLite, SQL Server, and other supported databases.
metadata:
  author: agentskillexchange
---

# Beekeeper Studio Cross-Platform SQL Editor and Database Manager

A source-backed ASE skill for Beekeeper Studio, the SQL editor and database manager for Linux, macOS, and Windows. It fits workflows that need a real client for querying, browsing tables, and working across PostgreSQL, MySQL, SQLite, SQL Server, and other supported databases.

## Installation

Use the upstream install or setup path that matches your environment:
- git clone git@github.com:<your-username>/beekeeper-studio.git beekeeper-studio
- yarn install # installs dependencies
- yarn run electron:serve ## the app will now start
- brew update

Requirements and caveats from upstream:
- Does it use a different node version. Eg Electron-18 uses node 14, 22 uses node 16. So everyone needs to upgrade
- Does node-abi need to be upgraded to be able to understand the electron version? This is used in the build to fetch prebuilt packages. You need to upgrade this in root/package.json#resolutions

Basic usage or getting-started notes:
- Query run-history, so you can find that one query you got working 3 days ago
- run git log <last-tag>..HEAD --oneline | grep 'Merge pull' to find PRs merged

- Source: https://github.com/beekeeper-studio/beekeeper-studio
- Extracted from upstream docs: https://raw.githubusercontent.com/beekeeper-studio/beekeeper-studio/HEAD/README.md

## Documentation

- https://docs.beekeeperstudio.io/

## Source

- [Agent Skill Exchange](https://agentskillexchange.com/skills/beekeeper-studio-cross-platform-sql-editor-database-manager/)

---
> Source: [agentskillexchange/skills](https://github.com/agentskillexchange/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
