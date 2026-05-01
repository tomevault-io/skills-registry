---
name: sysadmin
description: Manage Linux servers with user administration, process control, storage, and system maintenance. Use when this capability is needed.
metadata:
  author: openclaw
---

# System Administration Rules

## User Management
- Create service accounts with `--system` flag — no home directory, no login shell
- `sudo` with specific commands, not blanket ALL — principle of least privilege
- Lock accounts instead of deleting: `usermod -L` — preserves audit trail and file ownership
- SSH keys in `~/.ssh/authorized_keys` with restrictive permissions — 600 for file, 700 for directory
- `visudo` to edit sudoers — catches syntax errors before saving, prevents lockout

## Process Management
- `systemctl` for services, not `service` — systemd is standard on modern distros
- `journalctl -u service -f` for live logs — more powerful than tail on log files
- `nice` and `ionice` for background tasks — don't compete with production workloads
- Kill signals: SIGTERM (15) first, SIGKILL (9) last resort — SIGKILL doesn't allow cleanup
- `nohup` or `screen`/`tmux` for long-running commands — SSH disconnect kills regular processes

## File Systems and Storage
- `df -h` for disk usage, `du -sh *` to find culprits — check before disk fills completely
- `lsof +D /path` finds processes using a directory — needed before unmounting
- `ncdu` for interactive disk usage — faster than repeated du commands
- Mount options matter: `noexec`, `nosuid` for security on data partitions
- Resize filesystems with care: grow is safe, shrink risks data loss — always backup first

## Logs and Monitoring
- `logrotate` prevents disk fill — configure size limits and retention
- Centralize logs to external system — local logs lost if server dies
- `/var/log/auth.log` or `/var/log/secure` for login attempts — watch for brute force
- `dmesg` for kernel messages — hardware errors, OOM kills appear here
- Monitor inode usage, not just disk space — many small files exhaust inodes

## Permissions and Security
- `chmod 600` for secrets, `640` for configs, `644` for public — world-writable is almost never correct
- Sticky bit on shared directories (`chmod +t`) — users can only delete their own files
- `setfacl` for complex permissions — when traditional owner/group/other isn't enough
- `chattr +i` makes files immutable — even root can't modify without removing flag
- SELinux/AppArmor in enforcing mode — permissive logs but doesn't protect

## Package Management
- `apt update` before `apt upgrade` — upgrade without update uses stale package lists
- Unattended security updates: `unattended-upgrades` — critical patches shouldn't wait
- Pin package versions in production — unexpected upgrades cause unexpected outages
- Remove unused packages: `apt autoremove` — reduces attack surface and disk usage
- Know your package manager: apt/yum/dnf/pacman — commands differ, concepts similar

## Backups
- Test restores regularly — backups that can't restore are worthless
- Include package lists and configs, not just data — recreating environment is painful
- Offsite backups mandatory — local backups don't survive disk failure or ransomware
- Backup before any risky change — "I'll just quickly edit" famous last words
- Document restore procedure — 3am disaster is wrong time to figure it out

## Performance
- `top`/`htop` for live view, `vmstat` for trends — understand baseline before diagnosing
- `iotop` for disk I/O bottlenecks — slow disk often blamed on CPU
- Load average: 1.0 per core is healthy — consistently higher means queuing
- Swap usage isn't inherently bad — but consistent swapping indicates memory shortage
- `sar` for historical data — retroactively diagnose what happened during incident

## Networking Basics
- `ss -tulpn` shows listening ports — `netstat` is deprecated
- `ip addr` and `ip route` replace `ifconfig` and `route` — learn the new tools
- Check both host firewall and cloud security groups — traffic blocked at either level fails
- `/etc/hosts` for local overrides — quick testing without DNS changes
- `curl -v` shows full connection details — headers, timing, TLS handshake

## Common Mistakes
- Running services as root — one exploit owns the system
- No monitoring until something breaks — reactive is expensive
- Editing config without backup — `cp file file.bak` takes two seconds
- Rebooting to "fix" issues — masks the problem, it'll return
- Ignoring disk space warnings — 100% full causes cascading failures
- Forgetting timezone configuration — logs from different servers don't correlate

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
