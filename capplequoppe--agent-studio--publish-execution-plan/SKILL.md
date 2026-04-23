---
name: publish-execution-plan
description: Publish a local execution plan to GitLab as epics and issues Use when this capability is needed.
metadata:
  author: capplequoppe
---

Useful Environment Variables:
- GITLAB_HOST: "gabronickwontdiefromcovid.com"
- GITLAB_TOKEN: can be found in @.npmrc

## Overview

This skill publishes a local execution plan (structured according to the `execution-plans` skill)
to GitLab as a set of independent phase epics and task issues. Since nested epics require the
Ultimate tier, this skill uses one epic per phase, with a
shared label tying them together.

To preserve global context across epics, the root `README.md` content is prepended to each epic's
description.

## Prerequisites

- The execution plan directory must follow the structure defined by the `execution-plans` skill:
  - A root `README.md` describing the plan as a whole
  - Phase subdirectories (named `phase-*`) each containing a `README.md` and task markdown files
  - Task markdown files structured as issues with Title, Description, Acceptance Criteria, etc.
- The repository can be identified by the git remote origin URL
- The GitLab group is derived from the project's namespace in the remote URL

## Steps

1. **Validate the execution plan directory** at the path given in `$ARGUMENTS[0]`:
   - Confirm the root `README.md` exists
   - Identify all phase subdirectories (sorted alphanumerically)
   - For each phase, identify all task markdown files (sorted alphanumerically, excluding `README.md`)

2. **Determine GitLab project and group**:
   - Extract the project path from `git remote get-url origin`
   - URL-encode the project path for API calls (e.g., `moralis%2Fcore-blockchain-data-platform%2Fuse-cases-mono`)
   - Determine the **closest parent group** to the project (i.e., the project's direct namespace), NOT the top-level group. For example, if the project path is `moralis/core-blockchain-data-platform/use-cases-mono`, the group is `moralis/core-blockchain-data-platform`, not `moralis`.
   - Query the group by its full path: `GET /api/v4/groups/:url_encoded_full_path` (e.g., `GET /api/v4/groups/moralis%2Fcore-blockchain-data-platform`)
   - Confirm the group ID with the user before proceeding

3. **Create a scoped label** for the execution plan:
   - Derive the plan name from the execution plan directory name (e.g., `reorg-validation`)
   - Create a scoped label: `execution-plan::{plan-name}` at the group level if it doesn't already exist
   - Also create phase labels: `phase::{phase-directory-name}` for each phase
   - Use `POST /api/v4/groups/:id/labels` with a distinct color for the execution plan label

4. **Read the root README.md** content — this will be prepended to every epic description for global context.

5. **For each phase subdirectory** (in order):
   a. Read the phase `README.md`
   b. Create a GitLab epic via `POST /api/v4/groups/:id/epics` with:
      - **Title**: `[{plan-name}] {phase-directory-name}: {title from phase README H1}`
      - **Description**: Composed as follows:
        ```
        > This epic is part of the **{plan-name}** execution plan.
        > See related epics with label `execution-plan::{plan-name}`.

        ---

        <details>
        <summary>Execution Plan Context (click to expand)</summary>

        {root README.md content}

        </details>

        ---

        {phase README.md content}
        ```
      - **Labels**: `execution-plan::{plan-name}`, `phase::{phase-directory-name}`
   c. Record the epic IID and internal ID for later issue association
   d. For each task markdown file in the phase (sorted, excluding README.md):
      - Read the task file content
      - Extract the title from the first H1 heading (line starting with `# `)
      - Create a GitLab issue via `POST /api/v4/projects/:id/issues` with:
        - **Title**: The extracted H1 title
        - **Description**: The full markdown content of the task file (everything after the H1 line)
        - **Labels**: `execution-plan::{plan-name}`, `phase::{phase-directory-name}`
      - Associate the issue with the phase epic via `POST /api/v4/groups/:id/epics/:epic_iid/issues/:issue_id`
      - Record the issue IID and title for dependency linking

6. **Rewrite local file links in epic descriptions** (second pass):
   - After all epics and issues are created, build a mapping of local markdown paths to GitLab URLs:
     - For each phase directory name → the corresponding epic URL (e.g., `./phase-1-domain-foundation/README.md` → epic URL)
     - For each task file name → the corresponding issue URL (e.g., `./01-value-object-tests.md` → issue URL)
   - For each epic, update its description via `PUT /api/v4/groups/:id/epics/:epic_iid` with all local markdown links replaced:
     - In the **root README section** (inside `<details>`): replace phase README links like `./phase-X-name/README.md` with the corresponding epic URL
     - In the **phase README section**: replace task file links like `./NN-task-name.md` with the corresponding issue URL
   - Use string replacement on the description text, matching markdown link patterns `[text](./path.md)` and replacing the URL portion only
   - This ensures all links in GitLab epic descriptions are navigable and point to the correct GitLab entities

7. **Link dependencies between issues** (third pass):
   - Parse the root `README.md` for phase-level dependencies (look for dependency descriptions, diagrams, or "Dependencies" sections in phase READMEs)
   - For each phase README, check the "Dependencies" section to identify which phases it depends on
   - For task-level dependencies, check each task's "How It Contributes" or "Additional Notes" sections for references to other tasks/phases
   - Link dependent issues using `POST /api/v4/projects/:id/issues/:issue_iid/links` with:
     - `target_project_id`: the same project ID
     - `target_issue_iid`: the IID of the dependency
     - `link_type`: `is_blocked_by` (for the dependent issue pointing to the blocking issue)
   - At minimum, link the **first issue of each phase** to the **last issue of its predecessor phase** to establish the phase ordering
   - Interview the user if dependency relationships are ambiguous

8. **Print a summary** of everything created:
   - List of epics with their URLs
   - List of issues per epic with their URLs
   - List of dependency links created
   - The shared label for filtering all items

## GitLab API Reference

### Epics (Group-level)
- Create: `POST /api/v4/groups/:id/epics` — body: `{ "title": "...", "description": "...", "labels": "..." }`
- Update: `PUT /api/v4/groups/:id/epics/:epic_iid` — body: `{ "description": "..." }`
- Add issue: `POST /api/v4/groups/:id/epics/:epic_iid/issues/:issue_id`

### Issues (Project-level)
- Create: `POST /api/v4/projects/:id/issues` — body: `{ "title": "...", "description": "...", "labels": "..." }`
- Link: `POST /api/v4/projects/:id/issues/:issue_iid/links` — body: `{ "target_project_id": ..., "target_issue_iid": ..., "link_type": "..." }`

### Labels (Group-level)
- Create: `POST /api/v4/groups/:id/labels` — body: `{ "name": "...", "color": "#..." }`

### Common Headers
```
--header "PRIVATE-TOKEN: $GITLAB_TOKEN"
--header "Content-Type: application/json"
```

## Error Handling

- If an epic or issue creation fails, log the error and continue with the remaining items
- After all items are created, report any failures for manual remediation
- If the group ID cannot be determined, ask the user to provide it
- Rate limit: add a small delay between API calls if the GitLab instance has rate limiting

## Notes

- This skill assumes the GitLab tier is **Premium** (no nested epics). If the tier is upgraded to Ultimate in the future, this skill could be extended to use a root epic with child epics.
- The execution plan label acts as the "virtual root epic" — filtering by `execution-plan::{plan-name}` shows all related epics and issues.
- Epic ordering is conveyed through the title prefix (phase directory names sort naturally).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/capplequoppe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
