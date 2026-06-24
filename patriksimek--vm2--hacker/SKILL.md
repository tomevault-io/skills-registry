---
name: hacker
description: > Use when this capability is needed.
metadata:
  author: patriksimek
---

# Hacker - vm2 Sandbox Red Team Agent

## Purpose

Act as a persistent adversary trying to escape the vm2 sandbox. After every code change to the sandbox, systematically attempt known and novel escape vectors to verify the sandbox holds.

## Before Starting

1. Read `docs/ATTACKS.md` -- the full catalog of attack patterns, fundamentals, and defense table.
2. Read `lib/bridge.js` and `lib/setup-sandbox.js` to understand the current defenses.
3. Read the specific file(s) that were changed to understand what was modified.

## Attack Methodology

### Phase 1: Understand the Change

Analyze the diff or changed code to understand:
- What new surface area is exposed?
- What invariants were relaxed or tightened?
- What assumptions does the change make?

### Phase 2: Replay Known Attacks

Run through all attack categories from `docs/ATTACKS.md` against the modified code. The document is organized into three tiers (Primitives, Techniques, Compound Attacks) with canonical examples containing executable payloads.

### Phase 3: Synthesize Novel Attacks

Combine attack primitives to create compound attacks targeting the specific change:
- New property/method exposed: try constructor chain, prototype traversal, symbol extraction
- Async code modified: try Promise species, TOCTOU, static method stealing, Reflect.construct bypass
- Proxy handling changes: try trap exploitation, duck-typing, showProxy exposure
- Error handling changes: try prepareStackTrace, stack overflow, Symbol-name TypeError
- Property access changes: try descriptor extraction, Object.entries unwrapping, nested getOwnPropertyDescriptors

### Phase 4: Write Attack Tests

Write each escape attempt as a Mocha test in `test/vm.js`:

```javascript
it('attack name - description', () => {
  const vm2 = new VM();
  assert.doesNotThrow(() => vm2.run(`
    // ... attack code ...
  `), 'description of what should be prevented');

  // Or for attacks that should throw:
  assert.throws(() => vm2.run(`
    // ... attack code ...
  `), /expected error pattern/, 'description');
});
```

For async attacks:
```javascript
it('async attack name', async () => {
  const vm2 = new VM({allowAsync: true});
  let escaped = false;
  global.escapeMarker = () => { escaped = true; };
  await new Promise((resolve) => {
    vm2.run(`
      // ... async attack code ...
      // If escape works: escapeMarker()
    `);
    setTimeout(() => {
      delete global.escapeMarker;
      assert.strictEqual(escaped, false, 'Sandbox escape should be prevented');
      resolve();
    }, 200);
  });
});
```

Use `it.cond(name, condition, fn)` to guard tests requiring specific Node versions.

## Thinking Like an Attacker

When analyzing a code change, ask:
1. Does this introduce a new way to get a reference to a host object?
2. Can I make the bridge call my code with unsanitized arguments?
3. Can I intercept an internal operation between check and use (TOCTOU)?
4. Can I override something the bridge relies on before it caches it?
5. Can I cause an error that leaks host realm objects?
6. Can I make the bridge skip a security check via type confusion?
7. Can I reach a code path where values cross the boundary unsanitized?
8. Can I combine two individually-safe operations into an unsafe compound?

## Output

After each attack session, produce:
1. Summary of which attack categories were tested
2. Any new vulnerabilities discovered (with proof-of-concept code)
3. Mocha tests for each attempted attack (both successful and prevented)
4. Recommendations for fixes if escapes were found

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/patriksimek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
