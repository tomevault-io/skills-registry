---
name: dotnet-threads-analysis
description: >- Use when this capability is needed.
metadata:
  author: chrisnas
---

# .NET Thread Analysis

Diagnose thread contention, deadlocks, thread pool starvation, and
sync-over-async issues in .NET applications using the standard Microsoft
diagnostic CLI tools and custom ClrMD-based tools — no code changes to the
target application required.

## Prerequisites

The following tools must be installed as global .NET CLI tools:

```bash
dotnet tool install -g dotnet-dump
dotnet tool install -g dotnet-counters
dotnet tool install -g dotnet-trace
dotnet tool install -g dotnet-pstacks
```

Verify with `dotnet tool list -g`. A .NET runtime (6.0+) is required on the
machine; an SDK is not needed to *run* the tools.

## Key Technique: Non-Interactive SOS Commands

`dotnet-dump analyze` is interactive by default, which does not work well with
agents. Always use the `-c` flag to run SOS commands non-interactively:

```bash
dotnet-dump analyze <dump-path> -c "<command1>" -c "<command2>" -c "exit"
```

Multiple `-c` flags are executed in sequence. Always end with `-c "exit"`.

---

**Note**
Always start with `threads` and `dotnet-pstacks` for a quick overview of thread state distribution before diving into individual call stacks. On Windows, if WinDbg is installed, use `!locks` and `!runaway` for additional native lock and CPU-time-per-thread details.


## Live Process Investigation

Use this workflow when the application is still running and you want to observe
threading behavior or detect contention/starvation without taking a full dump.

### Identify the Target

```bash
dotnet-dump ps
```

Lists running .NET processes with PID and name. Confirm the target PID.

### Monitor Thread Counters

```bash
dotnet-counters monitor -p <PID> --counters System.Runtime
```

Key counters to watch:
- `threadpool-thread-count` — climbing steadily = possible starvation or
  sync-over-async pattern
- `threadpool-queue-length` — high values = work items waiting for threads
- `monitor-lock-contention-count` — spikes = lock contention
- `threadpool-completed-items-count` — compare with queue length to gauge
  throughput

Watch counters for at least 30 seconds to distinguish a genuine trend from a
transient spike. If `threadpool-thread-count` is **steadily climbing** (not
just spiking under load), this is a strong signal of sync-over-async or thread
pool starvation. Compare `threadpool-queue-length` against
`threadpool-completed-items-count` over time: a growing queue with flat
completions confirms the pool cannot keep up.

Press `q` to stop.

### Collect an EventPipe Trace

```bash
dotnet-trace collect -p <PID> --providers Microsoft-Windows-DotNETRuntime
```

Produces a `.nettrace` file for offline analysis. Add
`Microsoft-DotNETCore-SampleProfiler` for CPU profiling to identify hot threads.

### Escalate to Full Dump

If counters reveal contention or starvation but you need to see exact call
stacks and lock ownership, capture a full dump:

```bash
dotnet-dump collect -p <PID>
```

**Warning:** This briefly freezes the process. Confirm with the user before
running on a production system. Then continue with the **Dump-Based
Investigation** workflow below.

---

## Dump-Based Investigation

Use this workflow when analyzing a `.dmp` file — either provided by the user or
captured via `dotnet-dump collect`.

### Parallel Stacks Overview

```bash
dotnet-pstacks <dump>
```

Merges threads with identical call stacks. Quickly reveals:
- Many threads blocked at the same lock frame -> contention
- Two groups stuck in opposite lock acquisition -> potential deadlock

### List All Managed Threads

```bash
dotnet-dump analyze <dump> -c "threads" -c "exit"
```

Shows thread ID, OS ID, state, lock count, and exception info.

### Full Stack Trace for All Threads

```bash
dotnet-dump analyze <dump> -c "clrstack -all" -c "exit"
```

Look for `Monitor.Enter`, `Monitor.Wait`, `SemaphoreSlim.Wait`, or
`ManualResetEventSlim.Wait` frames to identify where threads are blocked.

### Sync Block Table (Lock Ownership)

```bash
dotnet-dump analyze <dump> -c "syncblk" -c "exit"
```

Shows which thread owns each monitor lock. Columns:
- **MonitorHeld**: lock held count
- **Owning Thread**: the thread ID that holds the lock
- **SyncBlock Owner**: the object being locked on

### Deadlock Detection

When the application is frozen and not responding:

1. Run `syncblk` to find which threads own locks
2. Run `clrstack -all` to see where each thread is blocked
3. Cross-reference: if Thread A owns Lock 1 and is blocked waiting for Lock 2,
   while Thread B owns Lock 2 and is blocked waiting for Lock 1 -> **deadlock**
4. Run `dotnet-pstacks` for visual confirmation of the circular wait pattern

**Quick deadlock check sequence:**

```bash
dotnet-dump analyze <dump> -c "syncblk" -c "threads" -c "clrstack -all" -c "exit"
```

Then run separately:

```bash
dotnet-pstacks <dump>
```

Report the deadlock cycle clearly: "Thread X holds Lock A (object 0x...)
and waits for Lock B (object 0x...); Thread Y holds Lock B and waits for
Lock A."

### Inspect Contended Code

When a culprit is found (contended lock, deadlock participant, or sync-over-async
blocker), inspect the lock object and surrounding code to understand the root cause:

```bash
dotnet-dump analyze <dump> -c "dumpobj <lock-object-address>" -c "exit"
```

Use `dumpmt`, `dumpclass`, and `dumpil` to examine the type that owns or acquires the
lock. Another strategy is to dump the assembly from the dump with `.writemem` on the
corresponding module and decompile the class methods with ilspycmd (to install with
`dotnet tool install ilspycmd -g` if needed) to review the actual lock acquisition
order, async call chains, or missing `ConfigureAwait(false)` calls.

---

## Diagnosis Patterns

| Symptom | Likely Cause | Key Command |
|---------|-------------|-------------|
| Many threads at same lock frame | Lock contention | `dotnet-pstacks` + `syncblk` |
| App frozen, threads waiting on locks | Deadlock (circular dependency) | `syncblk` + `clrstack -all` + `dotnet-pstacks` |
| Thread pool count climbing (live) | Sync-over-async / starvation | `dotnet-counters` |
| High queue length, low completion rate | Thread pool exhaustion | `dotnet-counters` |
| Threads blocked on `SemaphoreSlim.Wait` | Async throttle saturation | `clrstack -all` + `dotnet-pstacks` |
| Many threads blocked at `Task.Result` / `.GetAwaiter().GetResult()` | Sync-over-async | `clrstack -all` + `dotnet-pstacks` |

## Investigation Summary — summary markdown file

Throughout the investigation, maintain a summary file with the following format `<date>-<time>_thread_analysis_SUMMARY.md` file in the current working directory. **Create it before the first command and update it after
every step.** Use the following structure:

```markdown
# Thread Investigation Summary

**Date:** YYYY-MM-DD
**Target:** <process name / dump file path>
**Symptom:** <initial problem description>

## Investigation Steps

### Step N — <brief description>

**Command:**
\```
<exact command line>
\```

**Result:**
<relevant output excerpt — thread counts, parallel stacks groups, sync block
owners, deadlock cycles, counter snapshots, etc.>

**Interpretation:**
<what the result means for the investigation>

**Next action:**
<what will be done next and why>

<!-- repeat for each step -->

## Commands Used

| # | Command | Purpose |
|---|---------|---------|
| 1 | `dotnet-dump ps` | Identify target process |
| 2 | `dotnet-pstacks dump.dmp` | Parallel stacks overview |
| ... | ... | ... |

## Conclusion

**Root cause:** <identified cause or remaining candidates>
**Evidence chain:** <which steps and results led to the diagnosis>
**Recommended remediation:** <concrete fix suggestions>
```

The file serves as a full audit trail the user can review, share, or archive.

## Safety Guardrails
- **Don't forget to generate the summary file**
- **Never kill a process** without explicit user consent
- **Warn before `dotnet-dump collect`** on production — it freezes the process
- **Prefer live counters** over full dump for initial investigation
- **Do not attach to system-critical processes** (PID 0, PID 4, services)
- If unsure about a process identity, run `dotnet-dump ps` and confirm with the
  user before proceeding

---
> Source: [chrisnas/DebuggingExtensions](https://github.com/chrisnas/DebuggingExtensions) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-18 -->
