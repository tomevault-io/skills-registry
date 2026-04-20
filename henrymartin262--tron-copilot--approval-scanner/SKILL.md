---
name: approval-scanner
description: Scan wallet for token approvals and detect risky unlimited allowances. Critical for security. Use when this capability is needed.
metadata:
  author: henrymartin262
---

# Approval Scanner Skill

## When to use this skill

Use this skill to:
- Check all token approvals granted by a wallet
- Detect **unlimited approvals** (security risk!)
- Identify high-risk contract approvals
- Recommend approval revocations
- Monitor approval changes over time

## Security Risks of Approvals

### 🚨 Unlimited Approvals
When you interact with DeFi protocols, you often approve contracts to spend your tokens. **Unlimited approvals** mean a contract can spend ALL your tokens without asking again.

**Risk**: If the contract is:
- Hacked
- Malicious
- Has bugs

→ All your approved tokens can be stolen!

### Best Practice
✅ **Limited approvals**: Only approve exact amounts needed
❌ **Unlimited approvals**: Extremely risky, avoid if possible

## What This Skill Detects

### 1. 🔴 Critical Risks
- **Unlimited allowances** to unknown contracts
- Approvals to contracts with security issues
- Dormant approvals (granted long ago, still active)

### 2. ⚠️ Medium Risks
- High allowances to known DeFi protocols
- Multiple approvals to same contract
- Approvals to unverified contracts

### 3. ✅ Safe Approvals
- Limited allowances
- Approvals to verified, audited contracts
- Recent, actively used approvals

## Usage

```python
from skills.approval_scanner.scripts.scan_approvals import scan_approvals

# Scan all approvals
result = await scan_approvals("TXXXabc...")

# Get approval count
total = result['total_approvals']

# Get risky approvals
risky = result['risky_approvals']
```

## Output Example

```
🔍 Token Approval Scan: TXXXabc...abc
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Total Approvals: 12

🚨 CRITICAL RISKS: 2

  1. USDT → Unknown Contract (TYYYxyz...)
     Allowance: UNLIMITED ♾️
     Granted: 2025-10-15 (145 days ago)
     🚨 REVOKE IMMEDIATELY!
     
  2. USDC → Unverified DEX (TZZZabc...)
     Allowance: 1,000,000 USDC
     Granted: 2026-01-20 (19 days ago)
     ⚠️ HIGH RISK - Consider revoking

⚠️ MEDIUM RISKS: 3

Safe Approvals: 7

💡 Recommendation: Revoke 2 critical and review 3 medium-risk approvals
    Save ~$X in potential losses
```

## Integration

This skill should be run:
- **Before** large transfers or swaps
- **After** interacting with new DeFi protocols
- **Monthly** as security hygiene
- When prompted by security alerts

## Data Sources

- **TronScan API**: Approval events
- **TronGrid**: Contract verification status
- **Address Risk Checker**: Contract security ratings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/henrymartin262) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
