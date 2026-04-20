---
name: test-tcl
description: Run TCL parser tests Use when this capability is needed.
metadata:
  author: unknownbreaker
---

Run the TCL parser test suite.

## Usage

`/test-tcl` - Run all TCL tests
`/test-tcl json` - Run only JSON module tests
`/test-tcl procedures` - Run only procedure parser tests

## Commands

Run all TCL tests:
```bash
tclsh tests/tcl/core/ast/run_all_tests.tcl
```

Run specific module test:
```bash
tclsh tcl/core/ast/$ARGUMENTS.tcl
```

Run parser-specific test:
```bash
tclsh tests/tcl/core/ast/parsers/test_$ARGUMENTS.tcl
```

After running tests, report:
1. Total tests run
2. Pass/fail count
3. Any failing test details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unknownbreaker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
