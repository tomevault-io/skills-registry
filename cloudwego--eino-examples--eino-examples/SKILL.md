---
name: log-analyzer
description: Use when working with a skill to analyze log files for errors and warnings using a Python script.
metadata:
  author: cloudwego
---

# Log Analyzer Skill

This skill analyzes a log file to count the occurrences of "ERROR" and "WARNING" and lists the lines where they appear.

## Capability

The skill provides a Python script named `analyze.py` located in this directory. You can use this script to analyze any text file.

## Usage

To use this skill, execute the `analyze.py` script with the target log file as an argument.

### Example

```bash
python3 {{.BaseDirectory}}/scripts/analyze.py /path/to/logfile.log
```

**Note**: Replace `/path/to/logfile.log` with the actual path of the file you want to analyze.

---
> Source: [cloudwego/eino-examples](https://github.com/cloudwego/eino-examples) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
