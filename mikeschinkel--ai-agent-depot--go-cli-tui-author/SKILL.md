---
name: go-cli-tui-author
description: Author and refactor Go CLI commands using go-cliutil's enforced architecture and Mike's Go house rules (ClearPath, doterr, go-dt). Use whenever creating/modifying commands. Use when this capability is needed.
metadata:
  author: mikeschinkel
---

# cliutil Command Author

This skill is for **creating or modifying commands** in applications that use `github.com/mikeschinkel/go-cliutil`.

## Mandatory references
Read and apply these references (in order):

1) `references/cliutil.md` (framework conventions + patterns)
2) `references/nonnegotiables.md`
3) `references/clearpath.md` (production code only)
4) `references/doterr.md`
5) `references/go-dt-paths.md`
6) `references/testing.md` (only when writing tests)

## Architectural requirements (must follow)
1) **Main entrypoint** must be: `cmd/main.go` → `<apppkg>.RunCLI()`.
2) Commands live in a dedicated package (commonly `<appname>cmds`) and are loaded via a blank import:
   - `_ "your/module/path/<appname>cmds"`
3) **One command per file**, predictable filenames:
   - parent: `db_cmd.go`
   - child:  `db_migrate_cmd.go`
4) Parent commands must be initialized as package-level vars so children can reference them safely before `init()` runs.

## Command implementation pattern (template)
When adding a new command, produce complete code in the repo’s conventions. Use this structure:

- File: `<package>/<command>_cmd.go`
- Package: `<appname>cmds` (or existing commands package)

### Skeleton (adapt names/fields)
```go
package myappcmds

import (
    "github.com/mikeschinkel/go-cliutil"
)

var _ cliutil.CommandHandler = (*GreetCmd)(nil)

type GreetCmd struct {
    *cliutil.CmdBase

    // flags/options (raw)
    loud bool
    name string
}

var greetCmd = &GreetCmd{
    CmdBase: cliutil.NewCmdBase(cliutil.CmdArgs{
        Name:        "greet",
        Usage:       "greet [options] <name>",
        Description: "Print a greeting",
        Order:       10,
        FlagDefs: []cliutil.FlagDef{
            {
                Name:     "loud",
                Shortcut: 'l',
                Usage:    "Use loud greeting",
                Bool:     &greetCmd.loud,
                Default:  false,
            },
        },
    }),
}

func init() {
    err := cliutil.RegisterCommand(greetCmd /*, optional parent cmd */)
    if err != nil {
        panic(err)
    }
}

func (c *GreetCmd) Handle() error {
    // Implement using doterr + ClearPath
    return nil
}
```

### Important constraints
- **No compound `if` init statements** (split into two lines).
- **Never ignore errors**.
- **Use doterr** for errors returned from `Handle()`.
- **Use go-dt** for file/path work.
- If you need parent/child, register child as:
  - `err := cliutil.RegisterCommand(childCmd, parentCmd)`
  - `if err != nil { panic(err) }`

## What to deliver
When the user asks to add/modify a command, you must:
- Identify/confirm the commands package and existing registration pattern in the repo.
- Create/modify the command file(s) using the framework conventions.
- Ensure `cmd/main.go` and `<apppkg>.RunCLI()` integration is correct (update blank imports if needed).
- Provide complete, compiling code (or clearly marked TODOs if dependencies are missing).

## Test code
If asked to add tests:
- Follow `references/testing.md` (tests do NOT use ClearPath patterns).
- Still follow non-negotiables (no ignored errors, etc.).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mikeschinkel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
