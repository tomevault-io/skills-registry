---
name: ghq
description: Local repository management tool for cloning and organizing GitHub repositories. Use when referencing existing codebases, inspecting source code, or managing local repository copies. Use when this capability is needed.
metadata:
  author: natsukium
---

# ghq - Local Repository Management

ghq is a tool for managing local clones of remote repositories in a consistent directory structure.

## Triggers

GitHub repository, clone repo, existing codebase, reference code, local repository

## When to Use

User mentions a GitHub repository, needs to reference existing code, wants to clone repositories for code inspection

## Examples

<examples>
  <example>
    <user>Check the nixpkgs source code for how to configure this</user>
    <agent>I'll check if nixpkgs exists locally and clone if needed: `ghq get NixOS/nixpkgs`</agent>
  </example>
  <example>
    <user>Look at the React source code</user>
    <agent>I'll fetch React locally for reference: `ghq get facebook/react`</agent>
  </example>
</examples>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/natsukium) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
