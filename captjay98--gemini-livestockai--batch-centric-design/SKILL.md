---
name: batch-centric-design
description: Use when working with the batch is the atomic unit - UI patterns centered around batch management
metadata:
  author: captjay98
---

# Batch-Centric Design

In LivestockAI, the "Batch" (a group of animals) is the atomic unit of the farm.

## Core Principle

- **Wrong:** A "Feed Log" page where you select a batch.
- **Right:** A "Batch Dashboard" where you click "Log Feed."

All operations flow from the batch context.

## Batch Header (North Star)

Every batch-related page starts with this anchored header:

```
┌─────────────────────────────────────────────┐
│ 🐔 Broiler Batch A          Week 6    ● Synced │
│ 450/500 birds • Sunrise Poultry Farm         │
└─────────────────────────────────────────────┘
```

- Always visible (sticky on scroll)
- Shows: Species icon, name, age, sync status
- Tap to expand batch details

## Health Pulse Card

Color-coded status at a glance:

```
┌─────────────────────────────────────────────┐
│ 🟢 ON TRACK                                  │
│ Mortality: 2.1% • FCR: 1.8 • Weight: 1.2kg  │
└─────────────────────────────────────────────┘
```

## Action Grid

High-frequency actions as large touch targets:

```
┌──────────┬──────────┬──────────┐
│   🍗     │    💀    │    💰    │
│  Feed    │  Death   │   Sale   │
├──────────┼──────────┼──────────┤
│   ⚖️     │    💉    │    💧    │
│  Weigh   │   Vax    │  Water   │
└──────────┴──────────┴──────────┘
```

## Command Center Layout

Every Batch Detail view MUST follow this structure:

1. **Header (Static):** Batch Name | Age (Weeks) | Species Icon | Sync Status
2. **Health Pulse (Dynamic):** Color-coded status card
3. **KPI Strip:** Mortality % | FCR | Current Weight
4. **Action Grid:** Large buttons for high-frequency tasks

## Data Tables (Mobile)

On mobile, tables transform to cards:

```
┌─────────────────────────────────────────────┐
│ Jan 15, 2026                    ₦45,000     │
│ 50 birds @ ₦900/bird                        │
│ Customer: Alhaji Musa           [View →]    │
└─────────────────────────────────────────────┘
```

## Navigation Hierarchy

### Operations (The "Now")

- Farm Overview
- Batches
- Tasks

### Inventory (The "Resources")

- Feed Store
- Medicine Cabinet

### Analysis (The "Business")

- Credit Passport
- Financial Reports

### Ecosystem (The "Network")

- Customers
- Suppliers

## Related Skills

- `rugged-utility` - Touch targets and visual design
- `offline-first` - Sync status indicators

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/captjay98) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
