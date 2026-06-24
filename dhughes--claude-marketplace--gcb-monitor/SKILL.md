---
name: gcb-monitor
description: This skill should be used when the user asks about build status in GCP, Google Cloud Build, or gcloud builds. Trigger phrases include "monitor the build", "watch the build", "check build status", "build in test environment", "build in staging", "build in production", "status of the build", "gcloud builds", "Cloud Build", "GCP build", "what happened with the build", "why did the build fail", "analyze the build failure", "did the build succeed", "check the deploy", "monitor the deploy", "anything weird with the build", or when user mentions checking CI/CD status in Google Cloud. Also use when user wants to use gcloud CLI to check build results or logs. Use proactively after merging a PR when build monitoring would be helpful. Use when this capability is needed.
metadata:
  author: dhughes
---

# Google Cloud Build Monitor

Monitor, analyze, and diagnose Google Cloud Build executions using the gcloud CLI. Provide continuous status updates until builds complete, with detailed failure analysis when builds fail.

## Prerequisites

Ensure the gcloud CLI is installed and authenticated:

```bash
gcloud auth list
gcloud config get-value project
```

If not authenticated, inform the user and provide authentication guidance.

## Core Workflow

### Step 1: Identify the GCP Project

Determine which GCP project contains the build:

1. Check current default project:
   ```bash
   gcloud config get-value project
   ```

2. If context suggests a specific environment (staging, production, dev), look for matching projects:
   ```bash
   gcloud projects list --format="table(projectId,name)" --filter="projectId~staging OR name~staging"
   ```

3. If multiple projects exist and the correct one is unclear, list available projects and ask the user:
   ```bash
   gcloud projects list --format="table(projectId,name)"
   ```

4. Context clues for project selection:
   - Branch name "staging" or "main" → look for staging/production projects
   - Recent merge to main → likely production build
   - PR context → likely staging or CI project

### Step 2: Find the Build

Locate the specific build to monitor:

**List recent builds:**
```bash
gcloud builds list --project=PROJECT_ID --limit=10 --format="table(id,status,createTime,source.repoSource.branchName,substitutions.TRIGGER_NAME)"
```

**Find in-progress builds:**
```bash
gcloud builds list --project=PROJECT_ID --filter="status=WORKING OR status=QUEUED" --format="table(id,status,createTime,source.repoSource.branchName)"
```

**Find builds by branch:**
```bash
gcloud builds list --project=PROJECT_ID --filter="source.repoSource.branchName=BRANCH_NAME" --limit=5
```

**Build identification strategy:**
1. If user specifies build ID → use that directly
2. If user mentions "latest" or "current" → find most recent or in-progress build
3. If user mentions environment (staging, production) → filter by relevant branch/trigger
4. If PR context available → check GitHub PR checks for Cloud Build link, or find builds matching the commit SHA
5. If only one build is running → use that one
6. If multiple builds and ambiguous → show list and ask user to choose

### Step 3: Get Build Details

Retrieve comprehensive build information:

```bash
gcloud builds describe BUILD_ID --project=PROJECT_ID --format=json
```

Key fields to extract:
- `status`: QUEUED, WORKING, SUCCESS, FAILURE, CANCELLED, TIMEOUT
- `steps`: Array of build steps with individual status
- `source`: Where the build came from (branch, commit, trigger)
- `createTime`, `startTime`, `finishTime`: Timing information
- `logUrl`: Link to Cloud Console logs
- `substitutions`: Build variables including trigger name

### Step 4: Monitor Until Complete

**Polling behavior:**
- Poll every 60 seconds
- Continue until status is SUCCESS, FAILURE, CANCELLED, or TIMEOUT
- Never give up—some builds take an hour or more

**Progress updates:**
- Show initial status with build details
- On each poll, report current status
- Every 5 minutes, print a reminder that monitoring continues
- On step completion, note which step finished

**Status interpretation:**
- `QUEUED`: Build waiting to start → continue polling
- `WORKING`: Build in progress → continue polling
- `SUCCESS`: Build completed successfully → announce completion
- `FAILURE`: Build failed → analyze failure
- `CANCELLED`: Build was cancelled → report cancellation
- `TIMEOUT`: Build exceeded time limit → report timeout

### Step 5: Analyze Failures

When a build fails, provide actionable diagnosis:

**Identify failed step:**
```bash
gcloud builds describe BUILD_ID --project=PROJECT_ID --format="json(steps)"
```

Parse the steps array to find which step has `status: FAILURE`.

**Get step logs:**
```bash
gcloud builds log BUILD_ID --project=PROJECT_ID --stream
```

For specific step logs, look at the log output around the failed step's execution time.

**Failure analysis checklist:**
1. Which step failed? (name and index)
2. What was the step trying to do?
3. What error message appeared in logs?
4. Common failure patterns:
   - Test failures → look for test output
   - Build errors → look for compiler/bundler errors
   - Deployment errors → look for permission or resource issues
   - Timeout → step exceeded time limit

**Report format for failures:**
```
❌ BUILD FAILED - PROJECT_ID

Build ID: BUILD_ID
Duration: X minutes
Failed Step: step-name (step N of M)

Error Summary:
[Extracted error message or description]

Log URL: [logUrl from build details]

Recommended Actions:
[Suggestions based on error type]
```

### Step 6: Completion Notification

**On success:**
```
✅ BUILD SUCCEEDED - PROJECT_ID

Build ID: BUILD_ID
Duration: X minutes
Branch: branch-name

All steps completed successfully.
```

**Audio announcement (macOS):**
```bash
command -v say >/dev/null 2>&1
```

If `say` is available, announce completion with a human-friendly message:
- Success: `say "Cloud Build for [branch or trigger name] completed successfully"`
- Failure: `say "Cloud Build for [branch or trigger name] failed"`

**Important:** Never include the build UUID in the spoken announcement—use the branch name, trigger name, or environment name instead for human-friendly audio.

## GitHub Integration

When working in a GitHub PR context:

**Find build from PR checks:**
If the PR has Cloud Build checks, the check details may include the build ID or a link to Cloud Console.

```bash
gh pr checks PR_NUMBER --json name,state,detailsUrl
```

Look for checks with "Cloud Build" in the name or detailsUrl pointing to console.cloud.google.com.

**Find build from commit SHA:**
```bash
gcloud builds list --project=PROJECT_ID --filter="substitutions.COMMIT_SHA=SHA" --limit=1
```

## Error Handling

**Authentication errors:**
- Check `gcloud auth list` for active account
- Suggest `gcloud auth login` if needed

**Permission errors:**
- Verify project access with `gcloud projects describe PROJECT_ID`
- Suggest checking IAM roles if access denied

**Network/API errors:**
- Wait 60 seconds and retry
- After 3 consecutive errors, inform user but continue trying
- Never stop monitoring due to transient errors

## Proactive Monitoring

Offer to monitor builds in these situations:
- After user merges a PR
- After user mentions deploying to staging/production
- After user pushes to a tracked branch
- When user asks about deployment status

Example: "I notice you just merged to main. Would you like me to monitor the production build?"

## Key Points

1. **Never give up**: Keep polling until definitive completion
2. **Human-friendly audio**: Use branch/trigger names, not UUIDs in say command
3. **Context awareness**: Infer project and build from conversation context
4. **Actionable failures**: Don't just report failure—diagnose and suggest fixes
5. **60-second interval**: Balance responsiveness with API considerations
6. **Graceful errors**: Transient failures don't stop monitoring

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dhughes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
