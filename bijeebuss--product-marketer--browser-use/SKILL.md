---
name: browser-use
description: Browser Automation with browser-use. Use when when you are using browser-use tools. DO NOT use this skill if you are the browser-task-monitor agent. Use when this capability is needed.
metadata:
  author: bijeebuss
---

## Available MCP Tools

You have access to three browser-use MCP tools (prefixed with `mcp__browser-use__`):

## How browser-use Works

Unlike Playwright automation which requires you to use the tool to do individual browser actions like what to click and individual keystrokes, browser-use tasks are **spawned with complete instructions and run autonomously**:

1. **Describe the task** in natural language
2. **Spawn the task** using `browser_task`
3. **Monitor completion** using `monitor_task`
4. **Process results** when the task finishes

The AI agent running in the browser interprets your natural language instructions and completes the task autonomously.

## Best Practices

### Writing Effective Task Instructions

1. **DO NOT delegate your entire workflow to the browser agent**. Use the browser agent to complete browser specific tasks like "post a new tweet with 'CONTENT'" or "reply to the thread with 'REPLY'". You dont want to delegate thinking or decision making to the browser agent because we assume it will be more expensive, and less leffective than if you do those things yourself. Use tasks like "Give me a summary of my x.com notifications and then mark them all as read." then initiate new browser tasks to take follow-up actions. DO NOT use escape characters like \n when sending instructions to the browser agent, use actual newlines and separate post content from instructions with clear boundaries. Use 25 as a default for max_steps but you can increase it for more complex tasks

### Using Browser Profiles

**When to use profiles:**
- Tasks requiring authentication (email, social media)
- Maintaining session state across multiple tasks
- Preserving cookies and logged-in status

**Workflow:**
1. List available profiles: `list_browser_profiles()`
2. Identify the appropriate profile (e.g., "reddit-profile", "gmail-profile")
3. Use the profile_id in your browser_task

### Monitoring Tasks

- Delegate to the monitoring subagent browser-task-monitor. This saves your context while waiting. Pass a summary of the task to the browser-task-monitor subagent so that it can estimate how long to wait for the task to complete. Please keep track of how many steps tasks are taking so that we can better set the max_steps parameter in the future. 

## Error Handling

### Common Errors and Solutions

**Task timeouts:**
- Reduce task complexity
- Increase max_steps if too few
- Break into smaller sequential tasks

**Rate limiting:**
- Respect platform rate limits
- Add delays in task instructions when needed
- Space out multiple tasks over time

### Handling Failures

When a task fails:
1. Check the error message from `monitor_task`
2. Identify what step failed
3. Adjust task instructions accordingly
4. Retry with modified instructions
5. Report persistent failures to the user

## Integration Patterns

### Sequential Tasks
When tasks depend on each other:
```
1. Spawn task 1, use browser-task-monitor subagent to await completion
2. Extract results (e.g., post URL)
3. Use results in task 2 instructions
4. Spawn task 2, use browser-task-monitor subagent to await completion
```

### Parallel Tasks
When tasks are independent:
```
1. Spawn multiple browser_task calls
2. Monitor all task_ids concurrently with browser-task-monitor subagent
3. Process results as they complete
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bijeebuss) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
