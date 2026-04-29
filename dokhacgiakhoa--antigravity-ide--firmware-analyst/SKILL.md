---
name: firmware-analyst
description: Expert firmware analyst specializing in embedded systems, IoT Use when this capability is needed.
metadata:
  author: dokhacgiakhoa
---

# Download from vendor
wget http://vendor.com/firmware/update.bin

# Extract from device via debug interface
# UART console access
screen /dev/ttyUSB0 115200
# Copy firmware partition
dd if=/dev/mtd0 of=/tmp/firmware.bin

# Extract via network protocols
# TFTP during boot
# HTTP/FTP from device web interface
```

## 🧠 Knowledge Modules (Fractal Skills)

### 1. [Hardware Methods](./sub-skills/hardware-methods.md)
### 2. [Phase 1: Identification](./sub-skills/phase-1-identification.md)
### 3. [Phase 2: Extraction](./sub-skills/phase-2-extraction.md)
### 4. [Phase 3: File System Analysis](./sub-skills/phase-3-file-system-analysis.md)
### 5. [Phase 4: Binary Analysis](./sub-skills/phase-4-binary-analysis.md)
### 6. [Authentication Issues](./sub-skills/authentication-issues.md)
### 7. [Command Injection](./sub-skills/command-injection.md)
### 8. [Memory Corruption](./sub-skills/memory-corruption.md)
### 9. [Information Disclosure](./sub-skills/information-disclosure.md)
### 10. [Extraction Tools](./sub-skills/extraction-tools.md)
### 11. [Analysis Tools](./sub-skills/analysis-tools.md)
### 12. [Emulation](./sub-skills/emulation.md)
### 13. [Hardware Tools](./sub-skills/hardware-tools.md)
### 14. [QEMU User-Mode Emulation](./sub-skills/qemu-user-mode-emulation.md)
### 15. [Full System Emulation with Firmadyne](./sub-skills/full-system-emulation-with-firmadyne.md)
### 16. [Checklist](./sub-skills/checklist.md)
### 17. [Reporting Template](./sub-skills/reporting-template.md)
### 18. [Finding 1: Title](./sub-skills/finding-1-title.md)
### 19. [Appropriate Use](./sub-skills/appropriate-use.md)
### 20. [Never Assist With](./sub-skills/never-assist-with.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dokhacgiakhoa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
