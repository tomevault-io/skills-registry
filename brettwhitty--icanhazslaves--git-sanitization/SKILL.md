---
name: git-sanitization
description: Protocol for identifying and scrubbing prohibited language from Git history. Use when this capability is needed.
metadata:
  author: brettwhitty
---

# 🧹 Git Sanitization Skill

## 🔬 Purpose
To standardize the identification and removal of "spicy" or prohibited language from the repository's working tree and commit history.

## 🛠️ Workflow

### 1. Audit & Discovery
- [ ] **Working Tree Search**: Grep the codebase for a list of prohibited terms.
- [ ] **History Search**: Audit commit logs (`git log --all --grep="..."`) for the same terms.
- [ ] **Identify Targets**: Reference [prohibited-terms.md](file:///home/brett/repos/icanhazslaves/private/docs/prohibited-terms.md) and document specific files and commit hashes requiring sanitization.

### 2. Drafting the Plan
- [ ] **Implementation Plan**: Present a formal Implementation Plan to the User detailing the terms to be replaced and the emojis/substitutes to be used.
- [ ] **Authorization**: Wait for explicit [GATE] approval.

### 3. Execution (SOP Adherence)
- [ ] **File Sanitization**: Perform `sed` replacements in the working tree and commit changes.
- [ ] **History Rewrite**: Execute `git filter-branch -f --msg-filter ...` to scrub logs.
- [ ] **Cleanup**: Purge `.git/refs/original/`, expire reflogs, and run `git gc`.

### 4. Verification
- [ ] **Sanity Check**: Run `git log` to confirm replacements.
- [ ] **Forensic Verification**: Ensure old hashes are unreachable via `git show <old-hash>`.

### 5. Finalization
- [ ] **Walkthrough**: Document the successful sanitization and provide evidence.
- [ ] **Push Authorization**: Request final approval for `git push --force`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brettwhitty) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
