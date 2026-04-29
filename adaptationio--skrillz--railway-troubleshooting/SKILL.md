---
name: railway-troubleshooting
description: Railway debugging and issue resolution. Use when deployments fail, builds error, services crash, performance degrades, or networking issues occur. Use when this capability is needed.
metadata:
  author: adaptationio
---

# Railway Troubleshooting

Systematic debugging and issue resolution for Railway.com deployments.

## Overview

This skill provides decision trees, diagnostic workflows, and recovery procedures for Railway platform issues. It covers build failures, runtime crashes, networking problems, database issues, and performance degradation.

## Quick Start

Use this decision tree to diagnose and resolve Railway issues:

```
Railway Issue?
│
├── Deployment Failed?
│   ├── Build Error → Operation 1: Diagnose Build Failures
│   ├── Deploy Error → Operation 1: Diagnose Deployment Failures
│   ├── Health Check Failed → Check service health endpoint
│   └── Timeout → Check build/deploy timeouts in settings
│
├── Service Crashing?
│   ├── Immediate crash → Operation 2: Debug Runtime Crashes
│   ├── Crash after time → Check memory limits, memory leaks
│   ├── Restart loop → Check startup command, dependencies
│   └── Exit code errors → Check application logs for specifics
│
├── Networking Issues?
│   ├── Service unreachable → Operation 3: Troubleshoot Networking
│   ├── Intermittent connectivity → Check DNS, service discovery
│   ├── SSL errors → Check domain configuration, certificates
│   └── Timeout errors → Check port configuration, firewalls
│
├── Build Issues?
│   ├── Nixpacks detection wrong → Operation 4: Fix Build Errors
│   ├── Dependencies failing → Check package.json, requirements.txt
│   ├── Build commands failing → Verify build scripts
│   └── Cache issues → Clear build cache, force rebuild
│
└── Database Problems?
    ├── Connection refused → Operation 5: Resolve Database Issues
    ├── Timeout errors → Check connection pools, query performance
    ├── Performance slow → Check indices, query optimization
    └── Data corruption → Check backups, recovery procedures
```

## Operations

### Operation 1: Diagnose Deployment Failures

Identify and resolve deployment failures through systematic log analysis.

**When to use**: Deployment status shows failed, builds succeed but deploys fail, health checks failing.

**Workflow**:

1. **Check Deployment Status**
   ```bash
   # CLI approach
   railway status
   railway logs --deployment

   # API approach (see references/debug-workflow.md for GraphQL)
   # Query deployment status and recent deploys
   ```

2. **Analyze Deploy Logs**
   - Check for port binding issues (Railway expects PORT env var)
   - Verify health check endpoint responding
   - Check startup command execution
   - Identify timeout issues

3. **Common Deploy Failures**
   - Port not bound: App must listen on `process.env.PORT`
   - Health check timeout: Increase timeout or fix endpoint
   - Missing environment variables: Check service variables
   - Startup command wrong: Verify start command in settings

4. **Fix and Redeploy**
   - Apply fix to code/configuration
   - Trigger new deployment
   - Monitor deployment logs
   - Verify service healthy

**See**: `references/common-errors.md` for specific error messages and solutions.

### Operation 2: Debug Runtime Crashes

Investigate and resolve service crashes and restart loops.

**When to use**: Service shows restarting, exit codes in logs, OOM errors, crash reports.

**Workflow**:

1. **Gather Crash Information**
   ```bash
   # Get runtime logs
   railway logs --tail 500

   # Check service metrics
   railway metrics

   # Use diagnostic script
   ./scripts/diagnose.sh [service-id] --verbose
   ```

2. **Identify Crash Pattern**
   - Immediate crash: Startup issue (missing deps, config error)
   - Crash after time: Memory leak, resource exhaustion
   - Intermittent crash: Race condition, external dependency
   - Exit code 137: Out of Memory (OOM) killed

3. **Check Resource Limits**
   - Memory usage trending up → Memory leak
   - CPU at 100% → Infinite loop, CPU-intensive operation
   - Disk full → Log rotation issue, temp files
   - Connection limits → Database pool exhausted

4. **Common Crash Causes**
   - OOM: Increase memory limit or fix memory leak
   - Missing dependencies: Check package installation
   - Uncaught exceptions: Add error handling
   - External service down: Add retry logic, circuit breakers

**See**: `references/debug-workflow.md` for systematic debugging steps.

### Operation 3: Troubleshoot Networking

Resolve networking issues including service discovery, DNS, and connectivity.

**When to use**: Services can't reach each other, DNS resolution fails, external access issues, SSL errors.

**Workflow**:

1. **Verify Service Discovery**
   ```bash
   # Check private networking enabled
   # Services use: [service-name].[project-name].railway.internal

   # Test DNS resolution
   railway run nslookup [service-name].[project-name].railway.internal
   ```

2. **Check Network Configuration**
   - Private networking enabled in project settings
   - Service names correct (use Railway-provided names)
   - Port configuration matches application
   - Environment variables for service URLs set

3. **Debug External Access**
   - Domain configured correctly in service settings
   - DNS records pointing to Railway
   - SSL certificate provisioned (check domain settings)
   - Generate domain option enabled for public access

4. **Common Network Issues**
   - Service discovery: Use full internal domain name
   - Port mismatch: App must listen on PORT env var
   - SSL not working: Allow time for cert provisioning (5-10 min)
   - Timeout: Check for firewall rules, rate limiting

**See**: `references/common-errors.md` Network Errors section.

### Operation 4: Fix Build Errors

Resolve build failures, nixpacks configuration issues, and dependency problems.

**When to use**: Build fails, wrong builder detected, dependencies not installing, build commands fail.

**Workflow**:

1. **Check Build Logs**
   ```bash
   railway logs --build

   # Identify build phase failure:
   # - Detection phase: Nixpacks provider detection
   # - Install phase: Dependencies installation
   # - Build phase: Build commands execution
   ```

2. **Verify Builder Configuration**
   - Check nixpacks.toml or railway.toml for custom config
   - Verify build command in service settings
   - Check for language version specification
   - Ensure correct provider detected (Node, Python, Go, etc.)

3. **Fix Dependency Issues**
   - Lock file present (package-lock.json, yarn.lock, requirements.txt)
   - Dependencies compatible with build environment
   - Private packages have auth configured
   - Build dependencies vs runtime dependencies separated

4. **Force Rebuild if Needed**
   ```bash
   # Clear cache and rebuild
   ./scripts/force-rebuild.sh [service-id] --no-cache

   # Or via CLI
   railway up --detach
   ```

**Common Build Errors**:
- Wrong nixpacks provider: Add nixpacks.toml with correct provider
- Dependency resolution: Update lock files, fix version conflicts
- Build timeout: Optimize build, increase timeout in settings
- Cache issues: Clear build cache with force rebuild

**See**: `references/common-errors.md` Build Errors section.

### Operation 5: Resolve Database Issues

Debug database connection problems, timeouts, and performance issues.

**When to use**: Connection refused, database timeouts, slow queries, connection pool exhausted.

**Workflow**:

1. **Verify Database Connection**
   ```bash
   # Check database service status
   railway status

   # Test connection with database URL
   railway run psql $DATABASE_URL -c "SELECT 1"
   ```

2. **Check Connection Configuration**
   - DATABASE_URL environment variable set correctly
   - Connection pool size appropriate for service plan
   - Connection timeout settings reasonable
   - SSL mode configured if required

3. **Debug Connection Issues**
   - Connection refused: Database not started, wrong host/port
   - Timeout: Network issue, slow queries, pool exhausted
   - Auth failed: Wrong credentials, user permissions
   - Too many connections: Pool size exceeded, connection leak

4. **Performance Troubleshooting**
   - Slow queries: Check query plans, add indices
   - High CPU: Identify expensive queries, optimize
   - Connection pool exhausted: Increase pool size or fix leaks
   - Disk space: Clean up old data, increase storage

**Emergency Recovery**:
- Restart database service: `railway restart [service-id]`
- Check backups: Railway auto-backups available
- Scale vertically: Upgrade database plan if needed
- Connection leak: Restart application services

**See**: `references/recovery-procedures.md` for emergency procedures.

## Related Skills

- `railway-auth`: Authentication setup for Railway CLI/API
- `railway-logs`: Advanced log querying and analysis
- `railway-deployment`: Deployment workflows and strategies
- `railway-api`: GraphQL API queries and operations

## When to Use This Skill

Use railway-troubleshooting when you encounter:
- ❌ Deployment failures or build errors
- 🔄 Service restart loops or crashes
- 🌐 Networking or connectivity issues
- 🐛 Runtime errors or performance problems
- 💾 Database connection or query issues
- ⚡ Performance degradation
- 🔧 Configuration or environment issues

## Quick Diagnostic

Run the diagnostic script for automated issue detection:

```bash
cd /mnt/c/data/github/skrillz/.claude/skills/railway-troubleshooting/scripts
./diagnose.sh [service-id] --verbose
```

The script will:
- Check service health status
- Analyze recent deployment logs
- Scan for common error patterns
- Check resource utilization
- Provide specific recommendations

## Additional Resources

- **Common Errors Guide**: `references/common-errors.md` - 20+ documented errors with solutions
- **Debug Workflow**: `references/debug-workflow.md` - Systematic debugging methodology
- **Recovery Procedures**: `references/recovery-procedures.md` - Emergency recovery steps
- **Diagnostic Script**: `scripts/diagnose.sh` - Automated diagnostics
- **Force Rebuild**: `scripts/force-rebuild.sh` - Clear cache and rebuild

## Best Practices

1. **Always check logs first**: Build logs, deploy logs, runtime logs
2. **Verify environment variables**: Missing vars cause most deployment failures
3. **Check resource limits**: Memory/CPU limits appropriate for workload
4. **Test locally first**: Reproduce issues locally when possible
5. **Monitor metrics**: Use Railway dashboard for trends
6. **Document solutions**: Update common-errors.md with new patterns
7. **Use private networking**: For inter-service communication
8. **Enable health checks**: Catch deployment issues early

## Support

For issues not covered by this skill:
- Railway Documentation: https://docs.railway.com
- Railway Discord: Active community support
- Railway Status: https://status.railway.com
- GitHub Issues: https://github.com/railwayapp/railway/issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/adaptationio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
