---
name: s-test
description: > Use when this capability is needed.
metadata:
  author: falkicon
---

# Testing WoW Addons

Expert guidance for testing WoW addons using Sandbox, Desktop, and In-Game methods.

## Related Commands

- [c-test](../../commands/c-test.md) - Run unit tests workflow
- [c-review](../../commands/c-review.md) - Full code review (includes test step)

## CLI Commands (Use These First)

> **MANDATORY**: Always use CLI commands before manual exploration.

| Task | Command |
|------|---------|
| Generate Stubs | `mech call sandbox.generate` |
| Run Sandbox Tests | `mech call sandbox.test -i '{"addon": "MyAddon"}'` |
| Run Busted Tests | `mech call addon.test -i '{"addon": "MyAddon"}'` |
| Test Coverage | `mech call addon.test -i '{"addon": "MyAddon", "coverage": true}'` |
| Sandbox Status | `mech call sandbox.status` |

## Capabilities

1. **Sandbox Testing** — Fast, offline tests using generated WoW API stubs
2. **Desktop Testing (Busted)** — Integration tests with custom mocks
3. **In-Game Testing** — Runtime verification via MechanicLib registration
4. **Coverage Analysis** — Identify untested code paths

## Routing Logic

| Request type | Load reference |
|--------------|----------------|
| Sandbox, Busted, In-Game guides | [../../docs/integration/testing.md](../../docs/integration/testing.md) |
| Busted spec patterns | [references/busted-patterns.md](references/busted-patterns.md) |
| Mocking WoW APIs | [references/wow-mocking.md](references/wow-mocking.md) |
| MechanicLib test registration | [../../docs/integration/mechaniclib.md](../../docs/integration/mechaniclib.md) |

## Quick Reference

### Recommended Workflow
1. **Sandbox (Core)**: Fast feedback for business logic.
2. **Desktop (Integration)**: Test interactions between modules.
3. **In-Game (Verification)**: Final check against live APIs.

### Example Sandbox Test
```lua
describe("MyAddon Core", function()
    it("calculates values correctly", function()
        local result = Core.Calculate(10, 20)
        assert.equals(30, result)
    end)
end)
```

### Running Tests
```bash
# Generate stubs once
mech call sandbox.generate

# Run tests frequently
mech call sandbox.test -i '{"addon": "MyAddon"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/falkicon) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
