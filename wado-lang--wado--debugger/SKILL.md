---
name: debugger
description: Use rust-gdb to inspect variables without modifying code. Prefer this over print debugging when investigating compiler internals or runtime behavior. Use when this capability is needed.
metadata:
  author: wado-lang
---

# Debugger

Debug wado compiler with rust-gdb.

## Usage

```sh
cat > /tmp/gdb_commands.txt << 'EOF'
file ./target/debug/wado
set pagination off
break wado-compiler/src/codegen.rs:5985
run compile -o /tmp/out.wasm example/hello.wado
info locals
print *expr
bt 5
quit
EOF
rust-gdb --batch -x /tmp/gdb_commands.txt
```

## Common commands

| Command       | Description                   |
| ------------- | ----------------------------- |
| `info locals` | Show local variables          |
| `print *expr` | Dereference and print pointer |
| `bt 5`        | Backtrace (top 5 frames)      |
| `continue`    | Resume execution              |

## Notes

lldb does not work in Claude Code Web due to ptrace restrictions:

```
error: Cannot launch '...': personality get failed: Invalid argument
```

Use rust-gdb instead.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wado-lang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
