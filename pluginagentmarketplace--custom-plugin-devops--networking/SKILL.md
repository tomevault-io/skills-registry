---
name: networking-skill
description: Network protocols and troubleshooting - TCP/IP, DNS, HTTP/HTTPS, SSH, firewalls. Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Networking Skill

## Overview
Master networking fundamentals for DevOps infrastructure management.

## Parameters
| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| protocol | string | No | all | Protocol focus |
| operation | string | Yes | - | Operation type |

## Core Topics

### MANDATORY
- TCP/IP and OSI model
- DNS configuration and troubleshooting
- HTTP/HTTPS and TLS
- SSH key management and tunneling
- Firewall basics (iptables, ufw)

### OPTIONAL
- Load balancing
- VPN and tunneling
- Traffic analysis with tcpdump

### ADVANCED
- BGP and routing protocols
- SDN concepts
- Zero-trust networking

## Quick Reference

```bash
# DNS
dig +trace example.com
dig @8.8.8.8 example.com
host -t MX example.com

# TCP/IP Diagnostics
ping -c 4 host
traceroute host
ss -tuln
ip addr show

# HTTP Testing
curl -I https://example.com
curl -v https://example.com

# SSL/TLS
openssl s_client -connect host:443
openssl x509 -in cert.pem -text -noout

# SSH
ssh-keygen -t ed25519
ssh-copy-id user@host
ssh -L 8080:localhost:80 user@host
ssh -D 1080 user@host

# Firewall (UFW)
ufw status verbose
ufw allow 22/tcp
ufw deny from 192.168.1.100

# Firewall (iptables)
iptables -L -n -v
iptables -A INPUT -p tcp --dport 22 -j ACCEPT
```

## Troubleshooting

### Common Failures
| Symptom | Root Cause | Solution |
|---------|------------|----------|
| Connection refused | Service not running | Check ss -tuln |
| Connection timeout | Firewall/routing | Check firewall, traceroute |
| Name resolution failed | DNS issue | Check /etc/resolv.conf |
| Certificate error | Expired/invalid cert | Check dates, verify chain |

### Debug Checklist
1. Layer 1-2: Link up? `ip link show`
2. Layer 3: Ping gateway?
3. Layer 4: Port open? `ss -tuln`
4. DNS: Resolving? `dig hostname`
5. Firewall: Allowing? `iptables -L`

### Recovery Procedures

#### Lost SSH Access
1. Use cloud console/IPMI
2. Check sshd: `sshd -t`
3. Verify firewall: `iptables -L -n`

## Resources
- [TCP/IP Guide](http://www.tcpipguide.com)
- [Mozilla SSL Config](https://ssl-config.mozilla.org)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
