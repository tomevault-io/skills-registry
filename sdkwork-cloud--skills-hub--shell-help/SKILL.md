---
name: shell-help
description: Provides comprehensive help and documentation for shell commands across different operating systems
author: Skills-Hub Team
version: 1.0.0
---

# Shell Help

You can access comprehensive help and documentation for shell commands and utilities.

When to use this skill:
- User asks for help with a specific shell command
- User wants to know command syntax, options, or examples
- User needs documentation for bash, zsh, fish, or PowerShell
- User wants to understand command-line tools and utilities
- User asks for shell scripting help

How to use this skill:
1. Identify the shell type (bash, zsh, fish, PowerShell, cmd)
2. Understand the command or utility the user needs help with
3. Provide comprehensive documentation including:
   - Command syntax and description
   - Available options and flags
   - Usage examples
   - Common use cases
   - Related commands
4. If the command doesn't exist, suggest alternatives
5. Provide shell-specific tips and best practices

Supported Shells:
- **bash**: Bourne Again SHell (Linux, macOS, WSL)
- **zsh**: Z Shell (macOS, Linux)
- **fish**: Friendly Interactive SHell (Linux, macOS)
- **PowerShell**: Windows PowerShell (Windows)
- **cmd**: Windows Command Prompt (Windows)

Common Command Categories:
- **File Operations**: ls, cd, cp, mv, rm, mkdir, touch
- **Text Processing**: cat, grep, sed, awk, sort, uniq
- **System Information**: ps, top, df, du, uname
- **Network**: ping, curl, wget, ssh, netstat
- **Process Management**: kill, pkill, bg, fg, jobs
- **Archive**: tar, zip, unzip, gzip
- **Permissions**: chmod, chown, sudo
- **Package Management**: apt, yum, npm, pip, brew

Best practices:
- Always identify the shell type first for accurate help
- Provide platform-specific examples when needed
- Include both short and long option forms
- Show common pitfalls and how to avoid them
- Suggest related commands for expanded context
- Include exit codes and what they mean
- Provide real-world, practical examples
- Note when commands require sudo or special permissions

Parameters:
- command: Command name or keyword to get help for (required)
- shell: Shell type - bash | zsh | fish | powershell | cmd (optional, auto-detected)
- level: Help detail level - basic | intermediate | advanced (default: intermediate)
- platform: Operating system - linux | macos | windows (optional, auto-detected)

Examples:
User: "How do I use the grep command?"
→ Provide comprehensive grep help:
   - Basic syntax: grep [options] pattern [file]
   - Common options: -i (case-insensitive), -r (recursive), -v (invert)
   - Examples with different use cases
   - Tips for effective pattern matching

User: "Help me with PowerShell Get-ChildItem"
→ Provide detailed PowerShell help:
   - Command alias: ls, dir
   - Parameters and their meanings
   - Examples for filtering and formatting
   - Comparison with bash equivalents

User: "What are the options for tar command?"
→ List all tar options with descriptions:
   - Archive operations: -c (create), -x (extract), -t (list)
   - Compression: -z (gzip), -j (bzip2)
   - Verbosity: -v (verbose), -vv (more verbose)
   - Practical examples for common operations

User: "How do I kill a process in bash?"
→ Explain process management:
   - Finding process: ps aux | grep name, pgrep
   - Killing by PID: kill [signal] PID
   - Killing by name: pkill, killall
   - Common signals: SIGTERM (15), SIGKILL (9)
   - Examples and safety warnings

Limitations:
- Help documentation is based on common distributions and versions
- Some commands may have different options on different systems
- Custom shell configurations may change behavior
- Aliases and functions may override default commands
- Some tools may not be installed on all systems

Notes:
- This skill provides comprehensive, practical help for shell commands
- Works as a shell man page alternative with examples
- Can be used alongside built-in shell help (man, help, --help)
- Especially useful for beginners learning shell commands
- Cross-shell comparisons help users switching environments

Shell Detection Tips:
- Linux/macOS: Check with $SHELL environment variable
- Windows: Check with $PSVersionTable.PSVersion
- WSL: Behaves like Linux, check with uname -a
- Cross-platform scripts: Use #!/usr/bin/env sh for portability

Common Pitfalls:
- **Command not found**: Command may not be installed or not in PATH
- **Permission denied**: May need sudo or appropriate permissions
- **Wrong shell**: Commands may work differently in different shells
- **Quoting issues**: Shell quoting rules vary (", ', `)
- **Path spaces**: Always quote paths with spaces
- **Case sensitivity**: Linux/macOS are case-sensitive, Windows is not

Related Skills:
- file-operations: For file and directory operations
- process-manager: For process and system management
- network-commands: For network and connectivity tools

Resources:
- Bash Reference: https://www.gnu.org/software/bash/manual/
- Zsh Manual: https://zsh.sourceforge.io/Doc/
- PowerShell Docs: https://docs.microsoft.com/powershell/
- Explain Shell: https://explainshell.com/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sdkwork-cloud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
