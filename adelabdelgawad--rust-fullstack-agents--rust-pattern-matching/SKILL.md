---
name: rust-pattern-matching
description: > Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

# Rust Pattern Matching

## When to Use

Invoke this skill before:
- Writing or reviewing any `match` expression over a domain enum, `Option`, or `Result`.
- Diagnosing `error[E0004]: non-exhaustive patterns`.
- Investigating a runtime panic from `unreachable!()` or `.unwrap()` in a match arm.
- Introducing a new enum variant (each existing `_ =>` catch-all silently swallows it).
- Reviewing any nested `match`/`if let` pyramid that could use `let else` or `?`.

---

## Core Idioms

### `let else` for early-return on a missing value

```rust
// ✅ CORRECT — happy path stays at the top level; diverge on None/Err
let Some(user_id) = extract_user_id(&req) else {
    return Err(AppError::Unauthenticated);
};
// user_id: UserId is in scope here

// ❌ WRONG — indented match pyramid for what is really an early-return guard
let user_id = match extract_user_id(&req) {
    Some(id) => id,
    None => return Err(AppError::Unauthenticated),
};
```

The Book (ch06-03): "Notice that it stays on the 'happy path' in the main body of the
function this way, without having significantly different control flow for two branches the
way the `if let` did."

---

### `if let` only when the non-match arm is truly a no-op

```rust
// ✅ CORRECT — the else arm does nothing
if let Some(max) = config_max {
    println!("max = {max}");
}

// ❌ WRONG — using if let when both arms do real work; use match instead
if let Some(x) = opt {
    use_value(x);
} else {
    use_default();
}
// ✅ BETTER
match opt {
    Some(x) => use_value(x),
    None    => use_default(),
}
```

The Book (ch06-03): "Choosing between `match` and `if let` depends on what you're doing…
gaining conciseness is an appropriate trade-off for losing exhaustive checking."

---

### Match guard instead of nested `if` inside an arm

```rust
// ✅ CORRECT — guard is colocated with the pattern; compiler sees the shape
match num {
    Some(x) if x % 2 == 0 => println!("{x} is even"),
    Some(x)               => println!("{x} is odd"),
    None                  => (),
}

// ❌ WRONG — nested if buries the condition; harder to audit exhaustiveness
match num {
    Some(x) => {
        if x % 2 == 0 { println!("{x} is even"); }
        else           { println!("{x} is odd");  }
    }
    None => (),
}
```

Guards bind more loosely than `|`: `4 | 5 | 6 if y` means `(4 | 5 | 6) if y`.

---

### Destructure in the pattern, not in the body

```rust
// ✅ CORRECT — fields extracted at the match site
match msg {
    Message::Move { x, y } => move_to(x, y),
    Message::Write(text)   => log(text),
    Message::Quit          => shutdown(),
    Message::ChangeColor(r, g, b) => set_color(r, g, b),
}

// ❌ WRONG — match dispatches, body destructures again
match msg {
    Message::Move(inner) => {
        let x = inner.x;
        let y = inner.y;
        move_to(x, y);
    }
    // ...
}
```

---

### `@` binding to test and capture simultaneously

```rust
// ✅ CORRECT — id is bound and range-tested in one arm
match msg {
    Message::Hello { id: id @ 3..=7 } => println!("priority id {id}"),
    Message::Hello { id: 10..=12 }    => println!("secondary range"),
    Message::Hello { id }             => println!("other id {id}"),
}

// ❌ WRONG — range arm discards the value; body can't use it
match msg {
    Message::Hello { id: 3..=7 } => println!("priority id (unknown value)"),
    // ...
}
```

The Book (ch19-03): "`@` lets us test a value and save it in a variable within one pattern."

---

### Destructuring in `for` and function parameters

```rust
// ✅ CORRECT — destructure at the binding site
for (index, value) in v.iter().enumerate() { ... }

fn print_point(&(x, y): &(i32, i32)) { ... }

// ❌ WRONG — bind whole value, destructure in body
for pair in v.iter().enumerate() {
    let (index, value) = pair;
    // ...
}
```

---

## Forbidden Patterns

### Forbidden 1 — Catch-all `_ =>` on a Domain Enum

**Forbidden:** A `_ =>` wildcard (or a named catch-all `other =>`) as the only remaining arm
of a `match` over an application-defined enum.

**Why (Book ch06-02):** The compiler enforces exhaustiveness for explicitly listed arms. A
`_ =>` arm absorbs every variant not listed above it, including *variants added in the
future*. Adding a new variant silently compiles; the new case falls through to `_ =>` with
no warning and no forced review. The compiler's exhaustiveness guarantee — "Rust even knows
which pattern we forgot!" — is completely bypassed.

```rust
// ❌ FORBIDDEN — new variants fall silently through
match status {
    AgentStatus::Available => handle_available(),
    AgentStatus::OnBreak   => handle_break(),
    _                      => {}   // hides future variants
}

// ✅ CORRECT — adding a new variant breaks the build at every match site
match status {
    AgentStatus::Available  => handle_available(),
    AgentStatus::OnBreak    => handle_break(),
    AgentStatus::Wrapup     => handle_wrapup(),
    AgentStatus::Offline    => {}
}
```

**Exception:** `_ =>` is acceptable when matching *external* types you do not control
(e.g., `std::io::ErrorKind`, third-party enums), or as the last arm of a `match` over an
integer/string literal set. Mark with `// exhaustive: external type`.

```bash
# Detector — catch-all arms over local enums (heuristic; review every hit)
grep -rn '_ =>' src/ | grep -v '// exhaustive: external type' | grep -v '#\[cfg(test'
```

---

### Forbidden 2 — `match opt { Some(x) => x, None => panic!() }` Instead of `?` / `let else`

**Forbidden:** Manually destructuring `Option` or `Result` in a `match` or `if let` where
the non-matching case panics, calls `unreachable!()`, or silently returns a default.

**Why (Book ch09-02, ch06-03):** Rust provides the `?` operator and `let else` exactly for
this. Manual panic arms bypass the error-propagation chain, lose context, and are invisible
to callers. `.unwrap()` in production paths is forbidden by the project rules; this is the
structural equivalent.

```rust
// ❌ FORBIDDEN — manual panic in match arm
let conn = match pool.get() {
    Ok(c)  => c,
    Err(e) => panic!("no connection: {e}"),
};

// ❌ FORBIDDEN — unreachable!() masking a real case
let value = match opt {
    Some(v) => v,
    None    => unreachable!("should always be Some here"),
};

// ✅ CORRECT — propagate with ?
let conn = pool.get()?;

// ✅ CORRECT — diverge with let else
let Some(value) = opt else {
    return Err(AppError::MissingValue);
};
```

```bash
# Detector — panic!/unreachable! inside match/if-let arms
grep -rn 'panic!\|unreachable!' src/ | grep -v '#\[cfg(test\|// @invariant'
```

---

### Forbidden 3 — Nested `match`/`if let` Pyramids Instead of `?` or `let else`

**Forbidden:** More than one level of `match` or `if let` nesting when the outer level only
exists to unwrap an `Option`/`Result` before passing to the inner logic.

**Why (Book ch06-03):** Nesting escalates indentation, inverts the happy path, and makes
the control flow opaque. `?` or `let else` flattens the pyramid and keeps the happy path
linear.

```rust
// ❌ FORBIDDEN — pyramid of doom
async fn process(req: Request) -> Result<Response, AppError> {
    if let Some(session) = req.session() {
        if let Ok(user) = db.find_user(session.user_id).await {
            if let Some(profile) = user.profile {
                return Ok(build_response(profile));
            }
        }
    }
    Err(AppError::Unauthorized)
}

// ✅ CORRECT — flat happy path
async fn process(req: Request) -> Result<Response, AppError> {
    let session = req.session().ok_or(AppError::Unauthorized)?;
    let user    = db.find_user(session.user_id).await?;
    let profile = user.profile.ok_or(AppError::Unauthorized)?;
    Ok(build_response(profile))
}
```

```bash
# Detector — nested if let (heuristic)
# Caveat: fires on ALL if-let occurrences, not only nested ones — manually inspect each hit
# for actual nesting (a second if-let inside the body of the first).
grep -rn 'if let' src/ --include='*.rs' -A3 | grep 'if let'
```

---

### Forbidden 4 — `unreachable!()` / `_ => unreachable!()` Masking Real Cases

**Forbidden:** Using `unreachable!()` as the body (or entire else branch) of a match arm
over a domain enum in production code, outside a `#[cfg(test)]` block.

**Why (Book ch06-02, ch19-03):** `unreachable!()` is a runtime panic. When a future
variant is added or when an assumption is violated, the process crashes instead of failing
gracefully or producing a compile error. It also suppresses the exhaustiveness signal — the
compiler sees the arm and considers the match exhaustive; you lose the forced-review
property you'd get from a missing arm.

```rust
// ❌ FORBIDDEN
match event {
    CallEvent::Started  => handle_start(),
    CallEvent::Ended    => handle_end(),
    _                   => unreachable!("unexpected call event"),
}

// ✅ CORRECT — enumerate every variant; the compiler breaks the build on future additions
match event {
    CallEvent::Started   => handle_start(),
    CallEvent::Ended     => handle_end(),
    CallEvent::Held      => handle_hold(),
    CallEvent::Resumed   => handle_resume(),
}
```

```bash
# Detector
grep -rn 'unreachable!' src/ | grep -v '#\[cfg(test\|// @invariant'
```

---

### Forbidden 5 — Cargo-Culted `&Some(ref x)` Binding (Pre-2018 Style)

**Forbidden:** Writing `&Some(ref x)`, `&Ok(ref e)`, or manually sprinkling `ref` inside
patterns when matching a reference.

**Why (Rust Reference):** Rust's *default binding modes* (Edition 2018+) automatically
dereference the scrutinee and bind variables by reference when matching a `&T`. There is no
need to write `ref` or `&` inside patterns in ordinary match expressions. The cargo-culted
form is unnecessary noise in Edition 2018+ — binding modes handle the reference
automatically, and the explicit form confuses readers familiar with modern Rust.

```rust
// ❌ FORBIDDEN — pre-2018 cargo-cult
match &some_option {
    &Some(ref x) => use_ref(x),
    &None        => {},
}

// ✅ CORRECT — binding modes handle the ref automatically
match &some_option {
    Some(x) => use_ref(x),   // x: &T
    None    => {},
}

// ✅ ALSO CORRECT — match the owned value directly
match some_option {
    Some(x) => consume(x),   // x: T
    None    => {},
}
```

```bash
# Detector — explicit ref keyword inside patterns (rare legitimate uses exist; review each)
# Caveat: matches ref in matches!() macros and in owned-value patterns — expected false
# positives. Restrict to Rust source only to avoid Cargo.lock/doc noise.
grep -rn '\bref\b' src/ --include='*.rs' | grep -v '//' | grep -v '#\[cfg(test'
```

---

### Forbidden 6 — Ignoring `Result::Err` via `_` or Silently Swallowing It

**Forbidden:** Matching `Result` with a `_ =>` arm or `Ok(_) | Err(_) => ()` construction
that discards the error path without logging or propagating.

**Why (Book ch06-02):** The exhaustiveness guarantee exists to prevent exactly this. A
`Result::Err` that falls into `_` is a silent failure — the operation failed but the caller
receives no signal. Clippy's `unused_must_use` lint catches the top-level case but not a
`match` that explicitly handles every arm (even if the Err arm does nothing).

```rust
// ❌ FORBIDDEN — Err arm silently discarded
match db.save(record).await {
    Ok(row) => process(row),
    _       => {}   // DB error: swallowed
}

// ❌ FORBIDDEN — if let discards the Err silently
if let Ok(row) = db.save(record).await {
    process(row);
}
// (acceptable ONLY if the caller intentionally ignores failure AND that is documented)

// ✅ CORRECT — propagate
let row = db.save(record).await?;
process(row);

// ✅ CORRECT — log and continue deliberately
match db.save(record).await {
    Ok(row)  => process(row),
    Err(err) => tracing::error!(?err, "save failed; skipping record"),
}
```

```bash
# Detector 1 — explicit Err(_) arm discarded (matches only the Err(_) => {} form)
grep -rn -E 'Err\(_\)\s*=>\s*\{\}|Err\(_\)\s*=>\s*\(\)' src/ --include='*.rs' | grep -v '//'
# Detector 2 — wildcard arm on a Result-producing expression (catches the _ => {} example above)
grep -rn -E '_ =>\s*\{\}|_ =>\s*\(\)' src/ --include='*.rs' -B3 | grep -E 'match.*Result|\.await'
```

---

## Refutability Quick Reference

| Construct | Accepts | Compiler reaction on wrong kind |
|---|---|---|
| `let x = …` | Irrefutable only | `E0005` — refutable pattern in local binding |
| `fn f(x: T)` | Irrefutable only | same |
| `for x in …` | Irrefutable only | same |
| `if let` / `while let` | Both (refutable preferred) | `warn(irrefutable_let_patterns)` — Rust emits a lint warning, no W-code |
| `let … else` | Refutable (irrefutable makes `else` dead code) | warning |
| `match` last arm | Irrefutable (covers remainder) | `E0004` if total is not exhaustive |
| `match` non-last arms | Refutable | — |

The Book (ch19-02): "Function parameters, `let` statements, and `for` loops can only accept
irrefutable patterns, because the program cannot do anything meaningful when values don't
match."

---

## Binding Modes (Default Ref Bindings)

When you match a `&T` value, Rust Edition 2018+ automatically adjusts the binding mode so
that variables in the pattern bind by reference. You do not need `ref` or `&` decorators
inside the pattern.

```rust
let points: Vec<Point> = vec![Point { x: 1, y: 2 }];

// x and y bind as &i32 — no explicit ref needed
for Point { x, y } in &points {
    println!("{x}, {y}");
}
```

Only write `ref` when you explicitly need a reference inside a pattern where the scrutinee
is owned (rare, usually in complex nested destructuring).

---

## Book References

All citations from *The Rust Programming Language* (2024 edition):

| Topic | Chapter | Canonical URL |
|---|---|---|
| Enums and `match` introduction | Ch 6.0 | https://doc.rust-lang.org/book/ch06-00-enums.html |
| `match` exhaustiveness, E0004, wildcards, binding | Ch 6.2 | https://doc.rust-lang.org/book/ch06-02-match.html |
| `if let`, `let else` | Ch 6.3 | https://doc.rust-lang.org/book/ch06-03-if-let.html |
| All the places patterns can be used | Ch 19.1 | https://doc.rust-lang.org/book/ch19-01-all-the-places-for-patterns.html |
| Refutability | Ch 19.2 | https://doc.rust-lang.org/book/ch19-02-refutability.html |
| Pattern syntax (or-patterns, ranges, @, guards, destructuring, `_`, `..`) | Ch 19.3 | https://doc.rust-lang.org/book/ch19-03-pattern-syntax.html |

> **Note on chapter numbering:** The 2024 edition renumbered the Patterns chapter from
> Ch 18 to Ch 19. The original URLs (`ch18-*`) redirect to the `ch19-*` equivalents.

---

## Verification Hooks

Run all six detectors in a single sweep to audit a codebase for pattern-matching violations:

```bash
echo "=== F1: catch-all _ => on domain enums ==="
grep -rn '_ =>' src/ --include='*.rs' | grep -v '// exhaustive: external type' | grep -v '#\[cfg(test'

echo "=== F2: manual panic/unreachable in match arms ==="
grep -rn 'panic!\|unreachable!' src/ --include='*.rs' | grep -v '#\[cfg(test\|// @invariant'

echo "=== F3: nested if-let (heuristic — fires on ALL if-let; inspect for nesting) ==="
grep -rn 'if let' src/ --include='*.rs' -A3 | grep 'if let'

echo "=== F4: unreachable! outside test context ==="
grep -rn 'unreachable!' src/ --include='*.rs' | grep -v '#\[cfg(test\|// @invariant'

echo "=== F5: cargo-culted ref inside patterns ==="
grep -rn '\bref\b' src/ --include='*.rs' | grep -v '//' | grep -v '#\[cfg(test'

echo "=== F6a: explicit Err(_) arm silently discarded ==="
grep -rn -E 'Err\(_\)\s*=>\s*\{\}|Err\(_\)\s*=>\s*\(\)' src/ --include='*.rs' | grep -v '//'
echo "=== F6b: wildcard arm on Result-producing expression ==="
grep -rn -E '_ =>\s*\{\}|_ =>\s*\(\)' src/ --include='*.rs' -B3 | grep -E 'match.*Result|\.await'
```

---

## Related Skills

- `rust-panic-vs-result` — when to use `?` vs explicit match vs `unwrap`; the full taxonomy
  of panic-vs-propagate decisions.
- `rust-traits-generics` — when pattern matching interacts with `impl Trait` or generic
  bounds (e.g., matching on a `Box<dyn Error>` requires `downcast_ref`, not patterns).
- `rust-error-handling` — `AppError` enum design, `#[from]` chains, and how exhaustive
  `match` on `AppError` in `IntoResponse` is the correct mapping layer.

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
