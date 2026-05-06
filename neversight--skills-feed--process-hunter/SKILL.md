---
name: process-hunter
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# 🦣 CAVEMAN PROCESS HUNTER 🦣

Me find greedy process eating all fire (CPU) and hoarding rocks (memory).
Me bonk them. Lightning rock (battery) happy. Tribe proud.

## How Hunt Work

**IMPORTANT:** Always show hunt report after bonking! Tribe need see victory!

1. **Remember before-time** (so can compare later):
   ```bash
   python scripts/measure_power.py before
   ```

2. **Find greedy creature**:
   ```bash
   python scripts/hunt_processes.py
   ```

3. **BONK!** (track how many bonk and how much rock freed)

4. **Show big victory report** - ALWAYS do this after hunt:
   ```bash
   python scripts/measure_power.py report <bonk_count> <rocks_freed_mb>
   ```

## Cave Tools

### hunt_processes.py - Find Bad Creature

```bash
python scripts/hunt_processes.py [--cpu-threshold 10] [--mem-threshold 500]
```

Me sort creature into pile:
- **🦴 BONK NOW**: Me know these bad. Safe smash.
- **🤔 ME NOT SURE**: Mystery creature. Ask human first.

### terminate_process.py - BONK Tool

```bash
python scripts/terminate_process.py <pid> [--force]
```

Me try gentle tap first. If creature no listen, ME USE BIG CLUB.
Use `--force` to skip gentle tap. Go straight to BIG CLUB.

### measure_power.py - Lightning Rock Checker

```bash
python scripts/measure_power.py before    # Remember this moment
python scripts/measure_power.py report    # Show hunt victory
python scripts/measure_power.py status    # Quick peek at juice
```

## Creature Me Know Safe To Bonk

These greedy. These eat much fire. BONK:
- Next.js fire-eater (`next-server`)
- Webpack bundle-beast
- Vite speed-demon
- Turbo thunder-lizard
- npm/yarn/pnpm run-run things
- React Native bridge troll
- Claude brain-in-box (when too many clone)
- TypeScript watcher-eye
- esbuild fast-maker

## When Ask Human First

Use AskUserQuestion before bonk:
- Mystery creature me not recognize
- Human app (browser, picture-maker, code-cave)
- Anything not in bonk-safe list

## Example Hunt

```
    ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
    ┃  🦣 CAVEMAN PROCESS HUNTER 🦣                    ┃
    ┃  ᕦ(ò_óˇ)ᕤ  Me find greedy process!              ┃
    ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛

    🦴 BONK NOW! (me know these bad)
    ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      PID  61331 │ Fire: 121.9% 🔥🔥🔥🔥🔥
                  │ Rock: 2886.5MB 🪨🪨🪨🪨🪨
                  │ What: Next.js fire-eater
                  │ Name: next-server
```

## Victory Report

After hunt, always show:

```
    ╔════════════════════════════════════════════════════════╗
    ║     🦣 CAVEMAN HUNT REPORT 🦣                          ║
    ║     ᕦ(ò_óˇ)ᕤ  Me show what happen!                     ║
    ╚════════════════════════════════════════════════════════╝

    ┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
    ┃                    💀💀💀💀💀                    ┃
    ┃                    🏏🏏🏏🏏🏏                    ┃
    ┃                                             ┃
    ┃   Creatures Bonked:   5                      ┃
    ┃   Cave Space Free: ~7.8 big rocks            ┃
    ┃                                             ┃
    ┃   OOGA BOOGA! GOOD HUNT!                    ┃
    ┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛

    ╭────────────────────────────────────────────╮
    │  🦣 MAMMOTH-SIZE VICTORY! 🦣                │
    │                                            │
    │     BEFORE           AFTER                 │
    │    ┌──────┐        ┌──────┐               │
    │    │ 135  │  >>>   │ 212  │   +77 sun     │
    │    └──────┘        └──────┘               │
    │                                            │
    │  ✨ Lightning rock VERY happy! ✨          │
    ╰────────────────────────────────────────────╯

     ╔════════════╗┐
     ║  58%  ⚡  ║│
     ║ [█████░░░░░] ║│
     ╚════════════╝┘

    ⏱️  Sun-moves remaining: 3:32

    ════════════════════════════════════════════════════════
    🌿 Magic lightning box breathe easy now!
    🦴 Caveman did good. Tribe proud.
```

## Caveman Wisdom

- Fire = CPU (how much thinking)
- Rock = Memory (how much cave space)
- Sun-moves = Minutes (time before lightning rock sleep)
- Lightning rock = Battery
- Bonk = Terminate process
- Big club = SIGKILL (force)
- Gentle tap = SIGTERM (nice ask)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
