---
name: firmware-audit
description: Audit firmware and embedded code for security vulnerabilities and best practices. Use when user says "firmware audit", "embedded security", "IoT security check", or working with C/C++/Rust firmware. Checks for hardcoded credentials, buffer overflows, and insecure boot. Use when this capability is needed.
metadata:
  author: gr1m0h
---

Perform a security audit on firmware/embedded code.

## Context

Source files:
!`find . -name "*.c" -o -name "*.h" -o -name "*.cpp" -o -name "*.rs" 2>/dev/null | head -20`

Build system:
!`ls Makefile CMakeLists.txt build.rs Cargo.toml platformio.ini 2>/dev/null`

## Target: $ARGUMENTS

## Instructions

1. Scan for hardcoded credentials (passwords, keys, tokens)
2. Check for buffer overflow risks (unbounded copies, format strings)
3. Verify secure boot and update mechanisms
4. Audit communication protocols for encryption and authentication
5. Check physical interface security (debug ports, JTAG)
6. Generate structured security audit report

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gr1m0h) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
