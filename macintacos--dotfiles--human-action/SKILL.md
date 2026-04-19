---
name: human-action
description: Request human intervention for GUI operations, manual testing, or tasks requiring physical interaction. Use when you need the user to interact with applications like Xcode, perform manual testing, access authenticated systems, or complete multi-step IDE workflows. Use when this capability is needed.
metadata:
  author: macintacos
---

# Human Action Request

Use this skill when you need the human user to perform operations you cannot do yourself, such as:

- Interact with GUI applications (Xcode, IntelliJ, Figma, browsers)
- Perform manual testing (run the app, verify behavior, check UI)
- Access systems you cannot reach (local servers, physical devices, authenticated UIs)
- Complete multi-step IDE workflows (build configurations, provisioning, code signing)
- Verify real-world behavior (hardware interactions, push notifications)

## Step 1: Gather Request Details

Before presenting the request, the agent **MUST** assemble these fields internally:

### Required

| Field | Description |
|-------|-------------|
| Task | Brief description of what the user needs to do |
| Reason | Why the agent cannot perform this operation itself |
| Steps | Numbered steps to complete the task |
| Expected Outcome | What success looks like when the user is done |

### Optional

| Field | Description |
|-------|-------------|
| Estimated Time | How long the operation should take (see Timeout Guidelines below) |
| References | URLs to relevant documentation |

## Step 2: Present Action Request

The agent **MUST** render the request as a structured markdown block:

```markdown
### Action Required

| Field | Value |
|-------|-------|
| Task | <brief description> |
| Reason | <why agent can't do this> |
| Estimated Time | <timeout> |

**Steps:**

1. <step 1>
2. <step 2>
3. ...

**Expected Outcome:** <outcome description>

**References:**
- <url 1>
- <url 2>

> Type **continue** when complete.
```

The agent **MUST** omit optional fields (Estimated Time, References) if they are not applicable rather than showing empty values.

## Step 3: Wait for User

After presenting the request, the agent **MUST** wait for the user to type `continue` before proceeding. The agent **MUST NOT** assume completion based on time elapsed.

If the user types something other than `continue` (like describing an issue), the agent **MUST** address their concern before proceeding.

## Timeout Guidelines

| Operation Type | Recommended Timeout |
|---------------|---------------------|
| Simple GUI action | 5 minutes |
| Multi-step workflow | 10 minutes |
| Build/compile cycle | 15 minutes |
| Manual testing | 15-30 minutes |
| Complex setup | 30+ minutes |

When setting a timeout, the agent **SHOULD** consider:
- The user may need to read the instructions
- The user may need to consult documentation
- GUI operations may require waiting for Xcode/IDE to respond
- Testing may reveal issues requiring back-and-forth

The agent **SHOULD** err on the side of longer timeouts.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macintacos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
