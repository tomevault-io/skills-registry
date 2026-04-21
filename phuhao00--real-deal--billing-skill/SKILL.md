---
name: billing-skill
description: Billing and quota management system for real_deal platform including tiered storage limits, capacity packs, job slot quotas, usage metering, and charge tracking. Use when implementing billing features, managing quotas, tracking usage, creating capacity packs, or handling payment transactions. Use when this capability is needed.
metadata:
  author: phuhao00
---

# Billing & Quota Management

## Billing Model

### Storage Tiers
- **Free Tier**: 2GB storage, 5GB/month bandwidth, 60min/month transcoding
- **Capacity Packs**: One-time purchase for additional storage/bandwidth
- **Overage**: Metered billing with user confirmation

### Job Quotas
- **Free**: Max 2 active jobs
- **Quota Packs**: 5/20/50/100 job slots (one-time or subscription)
- **Expiration**: Requires explicit renewal

### Theme/Skin Store
- Fixed price or time-limited bundles
- Creator revenue sharing (with caps and tiered reductions)
- Performance and accessibility requirements enforced

## Data Models

### Usage Tracking
- `UsageMeter` - Track storage, bandwidth, transcoding time
- `Quota` - Current limits for user/company
- `CapacityPack` - Purchased capacity bundles
- `JobSlot` - Active job slot allocation

### Billing Records
- `ChargeRecord` - Payment history
- `UsageSnapshot` - Periodic usage snapshots

## Key Operations

### Storage Metering
```go
// Track upload size
func RecordUpload(userID string, size int64) error {
    usageMeter.UpdateStorage(userID, size)
    quota.CheckExceeded(userID)
}
```

### Job Slot Allocation
```go
// Allocate job slot
func AllocateJobSlot(companyID string) error {
    if !quota.Available(companyID) {
        return ErrQuotaExceeded
    }
    return jobSlot.Allocate(companyID)
}
```

### Capacity Pack Purchase
```go
// Process pack purchase
func PurchaseCapacityPack(userID string, packType string) error {
    pack := GetPack(packType)
    quota.Increase(userID, pack.Storage, pack.Bandwidth)
    charge := ChargeRecord{
        UserID: userID,
        Amount: pack.Price,
        Type:   "capacity_pack",
    }
    return charges.Save(charge)
}
```

## Usage Monitoring

### Real-time Metrics
- Storage usage per user/company
- Bandwidth consumption
- Job slot utilization
- Transcoding time used

### Alerts
- Quota approaching limit (80% warning)
- Exceeded quota (soft block)
- Overage confirmation required

## Billing Dashboard

### User View
- Current quota vs. used
- Usage history charts
- Capacity pack options
- Purchase history
- Estimated overage costs

### Admin View
- Revenue metrics
- Popular pack types
- Usage patterns
- Outstanding balances

## Common Tasks

### Add New Quota Type
1. Define quota field in `Quota` model
2. Implement metering logic in `UsageMeter`
3. Add tracking points in relevant handlers
4. Create UI for quota display
5. Set up alerts for limit approaching

### Create Capacity Pack
1. Define pack in `CapacityPack` collection
2. Set storage/bandwidth/transcoding limits
3. Configure pricing
4. Add to purchase flow

### Implement Overage Billing
1. Detect quota exceeded
2. Pause service (soft/hard block)
3. Prompt user for overage confirmation
4. Calculate overage charges
5. Generate `ChargeRecord`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuhao00) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
