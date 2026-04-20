---
name: gitea-issue-check
description: Review and process Gitea issues for the active Role. Use when this capability is needed.
metadata:
  author: brettwhitty
---

# Gitea Issue Check Skill

## Prerequisites
*   **Active Role**: Must be in a Role Context (e.g., `//office @rd`).
*   **Configuration**: `GEMINI.md` must define `Gitea.Auth` path.

## Workflow

### 1. Verify Identity & Configuration
*   **Goal**: Confirm operational context.
*   **Action**: Read the active `GEMINI.md`.
*   **Check**:
    *   `Gitea.User`: The Gitea Username (e.g., `rd`, `rory-agent`).
    *   `Gitea.Auth`: Path to credential file (e.g., `private/auth/.rd-env`).

### 2. Authenticate
*   **Goal**: Ensure `tea` CLI has valid credentials.
*   **Action**:
    ```bash
    # Activate Mise Environment (Profile)
    eval "$(MISE_ENV=<Gitea.User> mise activate)"

    # auto-register if missing (uses env vars from mise profile)
    if ! tea login list --output simple | grep -q "^<Gitea.User>"; then
         tea login add --name "<Gitea.User>" --url "https://gitea.gnomatix.com" --token "${GITEA_ISSUES_USER_AUTH_TOKEN}" --insecure
    fi
    ```

### 3. Fetch Assigned Issues
*   **Goal**: Retrieve active backlog.
*   **Action**:
    ```bash
    tea issues ls --assignee <Gitea.User> --state open --fields index,title,state,author,labels,updated
    ```

### 4. Triage Loop (Per Issue)
For each relevant issue:
1.  **Read Context**: `tea issue <id>` and `tea issue comments <id>`.
2.  **Analyze**:
    *   **New Request**: Acknowledge and add to `task.md`.
    *   **Blocked**: Comment requesting info.
    *   **Done**: Close issue (`tea issue close <id>`).
3.  **Execute**: Perform necessary work or updates.

### 5. Synchronize State
*   **Goal**: Align Local Brain with Remote Truth.
*   **Action**: Update `task.md` to reflect new priorities or closed items.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brettwhitty) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
