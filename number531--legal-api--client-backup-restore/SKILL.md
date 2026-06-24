---
name: client-backup-restore
description: On-demand backup and restore for Super Legal MCP client deployments. Use when the user asks to "backup client", "restore database", "create backup", "restore from backup", "disaster recovery", "backup database", "export data", "restore client data", or any request to backup or restore a client's Cloud SQL database, reports, or raw sources. Supports full database export, point-in-time recovery, reports archive, and restore verification. Also use when the user says "/client-backup-restore". Use when this capability is needed.
metadata:
  author: Number531
---

# Client Backup & Restore — Super Legal MCP

## Overview

On-demand backup and restore for client deployments. Supplements Cloud SQL's automated daily backups (30-day retention, point-in-time recovery) with explicit, verifiable backup + restore operations.

**Automated backups (already running):** Cloud SQL daily at 02:00 UTC, 30-day retention, point-in-time recovery enabled.

**This skill adds:** On-demand full database export, reports directory archive, restore from backup, restore verification, and cross-client backup comparison.

## Usage

### Create Backup

```
/client-backup-restore backup <client_id>
```

```bash
export CLIENT_ID="acme-legal"
export BACKUP_TYPE="full"  # full | database-only | reports-only
bash /Users/ej/Super-Legal/.claude/skills/client-backup-restore/scripts/backup-client.sh
```

### Restore from Backup

```
/client-backup-restore restore <client_id> --from <backup_path>
```

```bash
export CLIENT_ID="acme-legal"
export RESTORE_FROM="gs://super-legal-worm-acme-legal/backups/db-20260421-020000.sql.gz"
export CONFIRM_RESTORE="true"
bash /Users/ej/Super-Legal/.claude/skills/client-backup-restore/scripts/restore-client.sh
```

### List Available Backups

```
/client-backup-restore list <client_id>
```

```bash
export CLIENT_ID="acme-legal"
bash /Users/ej/Super-Legal/.claude/skills/client-backup-restore/scripts/list-backups.sh
```

## Backup Workflow

### Step 1: Database Export (Cloud SQL → GCS)

```bash
gcloud sql export sql super-legal-db-{client_id} \
  gs://super-legal-worm-{client_id}/backups/db-{timestamp}.sql.gz \
  --database=super_legal \
  --offload  # runs export on secondary, no production impact
```

Exports: all tables, indexes, constraints, extensions, data. Compressed SQL format.

### Step 2: Reports Directory Archive (GCE → GCS)

```bash
# SSH into instance, tar the reports directory, upload to GCS
gcloud compute ssh {instance} --zone={zone} --command="
  docker exec $(docker ps -q) tar czf /tmp/reports-backup.tar.gz /app/reports/
"
gcloud compute scp {instance}:/tmp/reports-backup.tar.gz /tmp/ --zone={zone}
gcloud storage cp /tmp/reports-backup.tar.gz \
  gs://super-legal-worm-{client_id}/backups/reports-{timestamp}.tar.gz
```

Archives: session directories, raw source pool, manifests, specialist reports.

### Step 3: Backup Verification

- Verify GCS objects exist and have non-zero size
- Compare database row counts (sessions, reports, kg_nodes) pre and post export
- Generate backup manifest with checksums

### Step 4: Backup Report

```
═══════════════════════════════════════
 BACKUP COMPLETE: acme-legal
═══════════════════════════════════════
 Database:  gs://super-legal-worm-acme-legal/backups/db-20260421-143052.sql.gz (45 MB)
 Reports:   gs://super-legal-worm-acme-legal/backups/reports-20260421-143052.tar.gz (120 MB)
 Sessions:  47
 Reports:   312
 KG Nodes:  4,291
 Duration:  2m 34s
═══════════════════════════════════════
```

## Restore Workflow

### Pre-Restore Checks

1. **Verify backup exists** in GCS with non-zero size
2. **Confirm client_id matches** backup path (prevent cross-client restore)
3. **Check current database** — warn if restoring over non-empty database
4. **Require CONFIRM_RESTORE=true** (destructive — overwrites current data)

### Step 1: Stop Active Sessions

```bash
# Set all in-progress sessions to 'halted' to prevent data corruption during restore
gcloud compute ssh {instance} --zone={zone} --command="
  docker exec $(docker ps -q) node -e \"
    const {getPool} = require('./src/db/postgres.js');
    getPool().query('UPDATE sessions SET status=\\'halted\\' WHERE status=\\'in_progress\\'')
      .then(r => console.log('Halted ' + r.rowCount + ' sessions'));
  \"
"
```

### Step 2: Import Database from Backup

```bash
# Option A: Cloud SQL import (recommended — handles extensions, schema)
gcloud sql import sql super-legal-db-{client_id} \
  gs://super-legal-worm-{client_id}/backups/db-{backup_timestamp}.sql.gz \
  --database=super_legal \
  --quiet

# Option B: Point-in-time recovery (for recent corruption, within 7-day window)
gcloud sql instances clone super-legal-db-{client_id} \
  super-legal-db-{client_id}-restored \
  --point-in-time={ISO_TIMESTAMP}
```

### Step 3: Restore Reports Directory

```bash
# Download archive from GCS
gcloud storage cp \
  gs://super-legal-worm-{client_id}/backups/reports-{backup_timestamp}.tar.gz \
  /tmp/reports-restore.tar.gz

# Upload to instance and extract
gcloud compute scp /tmp/reports-restore.tar.gz {instance}:/tmp/ --zone={zone}
gcloud compute ssh {instance} --zone={zone} --command="
  docker cp /tmp/reports-restore.tar.gz $(docker ps -q):/tmp/
  docker exec $(docker ps -q) tar xzf /tmp/reports-restore.tar.gz -C /
"
```

### Step 4: Verify Restore

- Compare row counts: restored DB vs backup manifest
- Health check: `/health` returns 200 with database connected
- Sample query: verify latest session exists with reports

## Cloud SQL Automated Backup Reference

Cloud SQL manages these automatically (no skill action needed):

| Setting | Value |
|---|---|
| Frequency | Daily at 02:00 UTC |
| Retention | 30 days |
| Point-in-time recovery | Enabled (7-day transaction log) |
| Storage | GCS (managed by Cloud SQL, not directly accessible) |
| On-demand backups | Available via `gcloud sql backups create` |

To list automated backups:
```bash
gcloud sql backups list --instance=super-legal-db-{client_id} \
  --format="table(id,startTime,status,type)"
```

To restore from automated backup:
```bash
gcloud sql backups restore {backup_id} \
  --restore-instance=super-legal-db-{client_id} \
  --quiet
```

## Backup Types

| Type | What's included | Size estimate | Duration |
|---|---|---|---|
| `full` | Database + reports directory | ~200-400 MB (v7.0.0+: includes transcript_events ~700KB-1MB/session) | 3-6 min |
| `database-only` | Cloud SQL export (all tables + data) | ~30-100 MB (larger on v7.0.0+ deployments due to transcript_events) | 1-3 min |
| `reports-only` | Reports directory (sessions, raw sources) | ~100-250 MB | 2-4 min |

**v7.0.0+ database-only scope** — Cloud SQL export captures all tables, including the new compliance/observability tables introduced in v7.0.0:
- `transcript_events` — full SSE event history per session (~700KB-1MB per session × N sessions). **Largest growth vector** at 10K+ sessions.
- `code_executions` (now with 13+ reproducibility columns: model_id, llm_name, anthropic_request_id, anthropic_message_id, system_prompt_hash, python_code, container_id, tool_use_id, stop_reason, turn_count, pause_count, refusal_detected, etc.) — required for byte-replay envelope per EU AI Act Art. 15
- `code_execution_inputs` — data lineage junction (small table, 1-5 rows per execution)
- `citation_source_links` — citation→source bridge with confidence scores (1 row per matched citation)
- `citation_verdicts` — per-footnote G5 verdicts (v6.8.6 T1, PR #122). 1 row per verified footnote; ~300-500 rows per memo session that ran citation-websearch-verifier. FK ON DELETE CASCADE on reports + sessions — backed up automatically via pg_dump; no manual handling needed.
- `hook_audit_log` — now includes `bridge_metadata` JSONB column with `git_sha + sdk_version + container_id + system_prompt_hash` (regulator-replay envelope)
- **v7.x XLSX renderer (migrations 016 + 017 + 018)**:
  - `human_interventions.metadata` JSONB column (added in 016 — was 015 pre-merge with main) — carries Art. 17 cascade-erasure audit payloads + Art. 14 manual-override context
  - `xlsx_renders` — one row per workbook render **request** (template_id, render_status, audit_results JSONB, artifact_id, xlsx_safe_flip_count + 4 generated columns from migration 018: `audit_status`, `sheet_count`, `warnings_count`, `node_audit_ran`). Lifecycle: `'pending' → 'running' → 'completed'|'failed'|'built'|'reconciled_failed'`. Async-202 endpoint (Issue #88) inserts at `'pending'`; auto-trigger path identical. **Decision record per Art. 12 retention — preserved on GDPR erasure** (see `docs/compliance/xlsx-art17-scope.md`).
  - `xlsx_render_inputs` — junction table linking each render to its consumed `code_executions` rows

Restore verification (Phase 4) should confirm these row counts post-restore for v7.0.0+ deployments:
- `SELECT COUNT(*) FROM transcript_events` matches pre-backup count
- `SELECT COUNT(*) FROM code_executions WHERE model_id IS NOT NULL` matches pre-backup count (NULL model_id = pre-v6.8.4 row, allowed)
- `SELECT COUNT(*) FROM citation_source_links` matches pre-backup count
- `SELECT COUNT(*) FROM citation_verdicts` matches pre-backup count (zero rows is acceptable for sessions that ran before v6.8.6 OR that never invoked citation-websearch-verifier)
- `SELECT event_data->'bridge_metadata' IS NOT NULL FROM hook_audit_log WHERE tool_name='run_python_analysis'` — bridge_metadata preserved on restore
- `SELECT COUNT(*) FROM xlsx_renders` matches pre-backup count (when `XLSX_RENDERER=true`). Post-restore: any `render_status IN ('pending','running')` rows will be picked up by reconciliation within `STUCK_BUILD_THRESHOLD_MIN`=60min — this is expected (renders resume from snapshot state)
- `SELECT COUNT(*) FROM xlsx_render_inputs` matches pre-backup count
- `SELECT COUNT(*) FROM human_interventions WHERE metadata != '{}'::jsonb` — Art. 17 / Art. 14 audit-trail metadata preserved

## Storage Locations

All backups stored in the client's WORM bucket:

```
gs://super-legal-worm-{client_id}/
├── backups/
│   ├── db-20260421-020000.sql.gz          ← database export
│   ├── db-20260420-020000.sql.gz
│   ├── reports-20260421-020000.tar.gz     ← reports archive
│   └── manifest-20260421-020000.json      ← backup metadata
├── archive/                                ← offboarding archives
└── raw-sources/                            ← WORM-locked raw API responses
```

Backups in the WORM bucket inherit Object Retention Lock — they cannot be deleted or modified until retention expires.

## Troubleshooting

**Export fails with "OPERATION_FAILED"**: Check Cloud SQL disk space: `gcloud sql instances describe {instance} --format="value(settings.dataDiskSizeGb)"`. Export needs temporary space equal to database size.

**Import fails with "permission denied"**: The Cloud SQL service account needs `storage.objectViewer` on the GCS bucket. Run: `gsutil iam ch serviceAccount:{sa}:roles/storage.objectViewer gs://{bucket}`

**Point-in-time recovery fails**: Transaction logs only retained 7 days. For older recovery, use the explicit SQL export backups.

**Reports archive too large**: Older sessions with many raw sources can be large. Use `reports-only` backup type with a date filter, or archive older sessions to GCS first via the tiering daemon.

**Restore verification shows row count mismatch**: This is expected if sessions were created between backup and restore. The backup is consistent to its point-in-time; newer data is lost.

---
> Source: [Number531/Legal-API](https://github.com/Number531/Legal-API) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
