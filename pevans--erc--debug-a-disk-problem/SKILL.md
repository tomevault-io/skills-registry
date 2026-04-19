---
name: debug-a-disk-problem
description: Enable an agent to debug disk problems Use when this capability is needed.
metadata:
  author: pevans
---

# Debug a disk problem

If you need to see what might be wrong with reading or writing to a disk, you
can look at the debug file that ends in `.disklog`. This file is the disk log.
It records the time of each byte read or written, and what instruction was
executed to perform the operation.

Each line has "RD" (for reads) or "WR" (for writes) to indicate what took
place.

You can also find the physical encoding of a disk image in a debug file that
ends in `.physical`.

When debugging anything, several other files are helpful:

- the metrics file (how many times certain events occur; ends in `.metrics`)
- the instruction log (all instructions sorted by place in memory it was
  executed; file ends in `.asm`)
- the timeset file (how much time each instruction took; ends in `.time`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pevans) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
