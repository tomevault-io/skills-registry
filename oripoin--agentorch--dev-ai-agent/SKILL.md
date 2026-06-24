---
name: dev-ai-agent
description: AI / LLM / Agent workflow development skill. Covers golden rules, model invocation routines, prompt management, security (injection / privilege escalation), observability, evaluation and testing, and anti-patterns. Use when building or reviewing agents, LLM integrations, and tool-call orchestration. Use when this capability is needed.
metadata:
  author: OriPoin
---

# AI / Agent Development Skill

## When to Use

- Writing or reviewing code that involves LLM calls, agent orchestration, tool calls, RAG, or vector retrieval.
- Designing prompts, evaluation sets, or model-version switch strategies.
- Investigating model-related stability, cost, security, or compliance issues.
- Collaboration protocol: see [ops-agent-collaboration](../ops-agent-collaboration/SKILL.md).

## Golden Rules

1. **Evidence first**: Read related artifacts and code before proposing solutions, changes, or conclusions; mark uncertainty explicitly when evidence is missing instead of guessing.
2. **Minimize changes**: Make only the changes requested or clearly necessary; do not casually refactor prompts or broaden tool permissions.
3. **Pin model versions**: Always use a concrete model identifier with date/hash; never use floating aliases.
4. **Structured contracts**: Model inputs and outputs go through schema validation; treat parse failures as errors rather than ignoring them.
5. **Distrust input**: Treat all user input and upstream tool output as untrusted; defend against injection and privilege escalation.
6. **LLM-call telemetry four-tuple**: every model call records at least "model version + token usage + latency + error code". General logs/metrics/traces three pillars: see [dev-logging-monitoring](../dev-logging-monitoring/SKILL.md).
7. **Evaluation first**: Any model or prompt change requires a baseline; the regression set is version-controlled.
8. **CI does not hit real models**: Unit tests mock; end-to-end evaluation runs on a separate channel.

## Risk Surface

- **Prompt injection**: upstream input overrides system instructions, induces tool calls, or escalates read/write privileges.
- **Data leakage**: writing secrets, PII, or internal documents into low-level logs or sending them to third parties.
- **Uncontrolled cost**: missing retry caps, timeouts, or token caps lead to bill and latency explosions.
- **Nondeterminism**: same input producing different outputs makes tests and regression unreliable.
- **Tool abuse**: invoking side-effecting tools (commands, file writes, network requests) without authorization or audit.

## Routines

### Routine A: Minimal Model Client Skeleton (language-agnostic)

Key points (apply in this order; none are optional):

1. Configuration injection: model ID, temperature, max tokens, timeout, API key source.
2. Input schema validation.
3. Explicit timeout + backoff retry + rate-limit (429/503) handling + retry cap.
4. Output schema validation; fall to error branch on failure.
5. Record observability fields; redact before returning.

Pseudocode:

```text
function call_model(input):
    validate(input, InputSchema)            # boundary validation
    started = now()
    for attempt in 1..MAX_ATTEMPTS:
        try:
            with timeout(REQUEST_TIMEOUT):
                raw = provider.complete(
                    model = MODEL_ID_PINNED,   # e.g., "gpt-x-2026-03-15"
                    messages = build_messages(input),
                    max_tokens = MAX_OUT_TOKENS,
                    temperature = TEMPERATURE,
                )
            break
        except RateLimited as e:
            sleep(backoff(attempt) + jitter())
            continue
        except Transient as e:
            if attempt == MAX_ATTEMPTS: raise
            sleep(backoff(attempt) + jitter())
        except Permanent as e:
            raise
    parsed = parse_or_raise(raw, OutputSchema)
    log_observability(
        model = MODEL_ID_PINNED,
        prompt_tokens = raw.usage.prompt,
        completion_tokens = raw.usage.completion,
        latency_ms = now() - started,
        attempt = attempt,
    )
    return parsed
```

### Routine B: File-Based and Version-Controlled Prompts

```
prompts/
  triage/
    system.v3.md          # system prompt, plain text
    user.template.md      # contains {{slot}} placeholders
    fixtures/             # evaluation samples
      hello.json
      injection-attempt.json
    eval.yaml             # evaluation assertions
```

- System prompts live in standalone files; code references the path and version only.
- Any change goes through code review and shares the code lifecycle; runtime "ad-hoc prompt assembly" is not allowed.
- Evaluation samples must include at least one injection attempt to verify adversarial hardening has not regressed.

### Routine C: Tool-Call Authorization and Audit

```
Tool registry:
  - name: read_file
    side_effect: read
    auth: allowed by default; restricted to a project-internal whitelist of paths
  - name: run_shell
    side_effect: execute
    auth: explicit authorization (per call or per session); whitelist of command prefixes
    audit: must log audit records (who/when/cmd/exit_code)
```

Key points:

- All tools that "modify the outside world" require explicit authorization; the model must not decide on its own.
- Audit logs use a separate channel from business logs to avoid being drowned by high-volume traffic.

### Routine D: Evaluation and Regression

```
eval/
  datasets/
    triage.golden.jsonl       # locked labeled samples
  runners/
    run_eval.py               # offline scoring
  scorecards/
    triage.v3.md              # current baseline scores
```

Process:

1. Run the current baseline before any prompt or model change and record scores.
2. Run the same dataset after the change; regression beyond the threshold (e.g., -2pp) is not allowed.
3. On regression, localize to specific samples; add new adversarial samples to the set.

## Anti-Patterns (Must Avoid)

### Anti-Pattern 1: Using floating model aliases

```text
# [BAD]
model = "gpt-fast"   # vendor upgrade silently changes behavior
```

Fix: use a concrete ID with date/hash, e.g., `"gpt-fast-2026-03-15"`, and include the version in configuration review.

### Anti-Pattern 2: Ad-hoc system-prompt assembly

```text
# [BAD]
messages = [
    {"role": "system", "content":
     "You are helpful. Answer in JSON. " + extra_hint + " " + user_prompt},
]
```

Problems:

- User input concatenated directly into the system message creates extreme injection risk.
- Prompts scattered across code make changes hard to trace.

Fix: load system prompts from files; user input only enters user messages; any concatenation goes through explicit templates and escaping.

### Anti-Pattern 3: Swallowing parse failures

```text
# [BAD]
try:
    data = json.loads(raw)
except Exception:
    data = {}        # silent degradation; errors hidden forever
```

Fix: classify parse failures (format error / missing field / type error), handle them as errors, and emit observability alerts.

### Anti-Pattern 4: Logging full prompts and raw responses at INFO

```text
# [BAD]
logger.info("prompt=%s response=%s", full_prompt, full_response)
```

Problems:

- May contain PII, secrets, or internal document fragments.
- INFO volume is huge, drowning real signal and increasing storage cost.

Fix:

- INFO level records metadata only (model version, token count, latency, error code, correlation ID).
- Full content is sampled only at DEBUG/TRACE level with sensitive fields redacted.

### Anti-Pattern 5: Missing timeouts and retry caps

```text
# [BAD]
while True:
    try: return client.complete(...)
    except Exception: continue
```

Consequence: infinite retries during upstream failures explode both bill and latency. Fix: every call has a timeout; retries have caps and backoff; transient and permanent errors are distinguished.

### Anti-Pattern 6: CI talking to real models

```text
# [BAD] unit test calls the real API
def test_summarize():
    out = client.complete(...)   # slow, expensive, flaky, coupled to vendor
    assert "..." in out
```

Fix: unit tests mock the model client; evaluation runs in a separate offline/nightly channel using versioned datasets.

### Anti-Pattern 7: High-privilege tools at the model's discretion

```text
# [BAD]
tools = [
    {"name": "run_shell", "description": "Run any shell command"},
]
```

Fix: high-privilege tools must have a command whitelist or human authorization gate; audit logs are written to a separate channel.

### Anti-Pattern 8: No adversarial cases

```text
# [BAD] evaluation set has only "well-behaved" samples; injection and privilege regressions go unnoticed.
```

Fix: keep a set of adversarial samples (injection attempts, privilege-escalation requests, malformed inputs) in the evaluation set and evolve them with versions.

## Security Checklist (inspired by OWASP LLM Top 10)

- [ ] User input enters only user messages and is validated/truncated.
- [ ] System prompts are file-based and versioned; never concatenated with user input.
- [ ] Side-effecting tools require explicit authorization and audit.
- [ ] Output is schema-validated before being passed downstream; never `eval`/`exec`-ed directly.
- [ ] Credentials and endpoints come from environment variables / secret management; never persisted or logged.
- [ ] Low-level logs do not contain full prompts, raw responses, or sensitive fields.
- [ ] The evaluation set includes adversarial samples; it has been run pre-release without regression.

## Review Checklist

- Is the model ID pinned to a concrete version?
- Are timeouts, backoff, rate limiting, and retry caps in place?
- Are inputs and outputs schema-validated with failure handling?
- Are the minimum three observability fields present (version / token / latency)?
- Are prompts file-based and free of user-input concatenation?
- Are tool calls authorized and audited?
- Does CI avoid talking to real models?
- Are baseline and adversarial samples ready?

---
> Source: [OriPoin/AgentOrch](https://github.com/OriPoin/AgentOrch) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
