---
name: wta-install
description: Install the WTA Rust CLI from source. Clone the public repository, build with cargo, and place the wta binary on PATH. Use when the user wants to start using WTA but does not yet have the wta binary installed, or wants to upgrade to the latest commit on main. Use when this capability is needed.
metadata:
  author: langgenius
---

# Install WTA

WTA is a Rust binary CLI distributed as source on GitHub. There is no
crates.io publish or prebuilt binary today, so installation always
goes through `cargo build`.

## Prerequisites

Confirm the user has these before continuing. If any are missing,
stop and explain what to install:

- `cargo` and `rustc` (Rust 1.70 or newer). Check with `rustc --version`.
- `git` with SSH or HTTPS access to GitHub.
- A Unix-like shell. WTA is tested on macOS and Linux.

Verify the cargo bin directory is on `PATH`:

```sh
echo "$PATH" | tr ':' '\n' | grep -F "$HOME/.cargo/bin"
```

If the output is empty, instruct the user to add `~/.cargo/bin` to
their shell rc file (`.zshrc`, `.bashrc`, etc.) and re-source it
before continuing.

## Steps

1. Clone the public WTA source:

   ```sh
   git clone https://github.com/langgenius/wta-workflow.git
   cd wta-workflow
   ```

2. Build and install with `cargo install`:

   ```sh
   cargo install --path . --locked
   ```

   This compiles WTA in release mode and copies the resulting binary
   to `~/.cargo/bin/wta`.

3. Verify the install:

   ```sh
   wta --help
   ```

   The output should list the WTA top-level commands (`init`,
   `doctor`, `info`, `board`, `intent`, `task`, `release`, ...). If
   `wta` is not found, return to the prerequisites step and check
   `PATH`.

## Upgrading later

To upgrade to the latest commit:

```sh
cd /path/to/wta-workflow
git pull --ff-only
cargo install --path . --locked --force
```

## Common failures

- **`error: rustc 1.x is not supported`** — run `rustup update stable`.
- **`error: failed to run custom build command`** — usually a missing
  system dependency (`pkg-config`, `libssl-dev` on Debian, etc.).
  Read the error and install the named library.
- **`wta: command not found` after install** — the cargo bin
  directory is not on `PATH`. Re-read the cargo install output for
  the exact install location.
- **`Permission denied (publickey)` when cloning** — use HTTPS instead
  of SSH for the clone, or run `ssh-add` to load the user's key.

## After install

WTA is now available as `wta`. The next step depends on the user's
goal:

- To start operating an existing WTA project or set up a new one,
  load the `wta-using-wta` skill.
- To create a new WTA project from scratch, run `wta project init`
  inside an empty directory and then `wta project plan` to preview
  the setup.

---
> Source: [langgenius/wta-workflow-plugin](https://github.com/langgenius/wta-workflow-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
