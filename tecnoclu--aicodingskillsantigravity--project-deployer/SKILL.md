---
name: project-deployer
description: Analyzes the project and generates a tailored 'deploy.bat' script for standardized deployment with interactive commit messages.
metadata:
  author: tecnoclu
---

# Project Deployer Instructions

Use this skill when the user wants to set up deployment for a project.

## Workflow

1.  **Analyze Project**:
    *   Check for existing deployment scripts (`deploy.sh`, `deploy.bat`).
    *   Identify the project type (Laravel, Node, etc.) to determine if build steps are needed (e.g., `npm run build`, `composer install`).
    *   *Default Strategy*: For most "Relay-like" projects, a Git-based deployment is preferred.

2.  **Generate `deploy.bat`**:
    *   Create a file named `deploy.bat` in the project root.
    *   **Standard Content Template**:
        ```batch
        @echo off
        echo === Deployment ===
        set /p commit_msg="Enter commit message: "
        if "%commit_msg%"=="" set commit_msg="Update"

        echo Committing: %commit_msg%
        git add .
        git commit -m "%commit_msg%"
        git push

        echo.
        echo Deployed to GitHub!
        pause
        ```
    *   **Customization**: If the project requires specific pre-deploy steps (like building assets), insert those commands *before* `git add .` if those built assets are tracked, or ensure the remote handles them.

3.  **Verification**:
    *   Notify the user that `deploy.bat` has been created.
    *   Instruct them to run it via `.\deploy.bat`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tecnoclu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
