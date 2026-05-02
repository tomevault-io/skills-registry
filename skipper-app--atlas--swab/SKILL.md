---
name: swab
description: Address all unresolved PR review comments - fix issues, commit, push, reply, and resolve threads Use when this capability is needed.
metadata:
  author: skipper-app
---

# Swab

Address all unresolved review comments on the current branch's pull request.

## Workflow

1. **Fetch unresolved comments**
   If the PR number is not already known, use `gh pr view --json number` to get it. Then use the GraphQL API to fetch
   review threads. Filter to unresolved threads and process newest comments first.

2. **Fix each issue**
   Read and understand each unresolved comment. Make the requested code changes.
   If a comment requests something that should not be done, note your reasoning.

3. **Commit and push**
   Stage all changes, commit with a conventional message (e.g., `fix: Address review comments`),
   and push to the PR branch.

4. **Reply and resolve threads (batched)**
   Batch all reply and resolve operations into a single GraphQL mutation to minimize API calls.
   For each comment, include a reply explaining how you resolved the issue or why you chose not to.

## Commands Reference

Get PR number:
gh pr view --json number

Fetch review threads via GraphQL (replace OWNER, REPO, PR_NUMBER):
gh api graphql -f query='
query {
  repository(owner: "<OWNER>", name: "<REPO>") {
    pullRequest(number: <PR_NUMBER>) {
      reviewThreads(first: 100) {
        nodes {
          id
          isResolved
          path
          line
          comments(first: 10) {
            nodes {
              body
              author { login }
            }
          }
        }
      }
    }
  }
}'

Batch reply and resolve multiple threads in a single API call:
gh api graphql -f query='
  mutation {
    reply1: addPullRequestReviewThreadReply(input: {
      pullRequestReviewThreadId: "<THREAD_ID_1>",
      body: "<REPLY_1>"
    }) { comment { id } }
    resolve1: resolveReviewThread(input: {
      threadId: "<THREAD_ID_1>"
    }) { thread { isResolved } }
    reply2: addPullRequestReviewThreadReply(input: {
      pullRequestReviewThreadId: "<THREAD_ID_2>",
      body: "<REPLY_2>"
    }) { comment { id } }
    resolve2: resolveReviewThread(input: {
      threadId: "<THREAD_ID_2>"
    }) { thread { isResolved } }
  }'

Use unique aliases (reply1, resolve1, reply2, resolve2, etc.) for each operation in the batch.

Note: Both `pullRequestReviewThreadId` (in `addPullRequestReviewThreadReply`) and `threadId`
(in `resolveReviewThread`) refer to the same thread ID obtained from the `reviewThreads` query.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/skipper-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
