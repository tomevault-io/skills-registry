---
name: jira-code-review
description: Instructions and workflow steps to get a Jira ticket and branch ready for code review Use when this capability is needed.
metadata:
  author: dextermb
---

Before following the instructions below make sure to execute the following skills:
- git-commit
- gitlab-pull-request

IMPORTANT: Once both of the previous skills have been approved and actioned then you can continue on with the workflow below.

Now that the previous steps have finished we need to transition the work from "In Progress" (also known as "In Development", "In Dev") to "In Review" (also known as "Code Review") so that our peers know they can take a look at the pull request.

Determine the current Jira ticket check the current branch name. Generally if the branch is a Jira ticket it will start with `KAN-`, if it does not then ask what ticket you should be transitioning.

Once the ticket has been identified use the `acli jira workitem transition` command to move it from In Progress to In Review.

IMPORTANT: Other than updating the state of the Jira ticket do not change anything else.

An example command would be something such as:

```bash
acli jira workitem transition --key <branch> --status "In Review"
```

Placeholders to be replaced:
- "<branch>": The Jira ticket

If you are unsure of how the command should be structured read the `acli jira workitem view` documentation found at
> https://developer.atlassian.com/cloud/acli/reference/commands/jira-workitem-transition/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dextermb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
