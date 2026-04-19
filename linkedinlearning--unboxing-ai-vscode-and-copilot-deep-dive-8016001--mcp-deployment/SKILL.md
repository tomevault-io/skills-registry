---
name: mcp-deployment
description: Plan and execute MCP server deployment to production environments Use when this capability is needed.
metadata:
  author: linkedinlearning
---

# MCP Deployment Skill

## Overview

The MCP Deployment Skill manages the complete deployment lifecycle for Model Context Protocol servers. It handles pre-deployment validation, deployment execution, health verification, and post-deployment monitoring.

## When to Use This Skill

- **Scenario 1**: Deploying a new MCP server to production
- **Scenario 2**: Updating an existing deployed server
- **Scenario 3**: Rolling back a failed deployment
- **Scenario 4**: Scaling up deployed servers
- **Scenario 5**: Monitoring and maintaining production servers

## Key Capabilities

### 1. Pre-Deployment Validation

- Code quality verification
- Test coverage validation
- Security scanning
- Configuration validation
- Dependencies verification

### 2. Deployment Planning

- Deployment strategy selection
- Environment preparation
- Rollback plan creation
- Communication planning
- Risk assessment

### 3. Deployment Execution

- Server build and packaging
- Configuration management
- Environment setup
- Service deployment
- Health checks

### 4. Verification & Validation

- Connectivity testing
- Tool discoverability verification
- Sample invocations
- Performance baselines
- Error scenario testing

### 5. Monitoring & Maintenance

- Metrics collection setup
- Alert configuration
- Log aggregation
- Health monitoring
- Performance tracking

## Quick Start

### Basic Deployment

```
Request: "Deploy weather MCP server to production"
[Provide server code and configuration]
Skill: Validates, packages, deploys, and verifies
Output: Deployment complete with monitoring active
```

### Advanced Deployment

```
Request: "Deploy with blue-green strategy, capture baseline metrics"
[Provide server, config, performance targets]
Skill: Executes phased deployment with validation
Output: Deployment with A/B comparison metrics
```

## Pre-Deployment Checklist

### Code Quality Gate

```
✓ All tests passing (pytest --cov, 80%+ coverage)
✓ No linting errors (pylint, black)
✓ Type checking passes (mypy)
✓ Code reviewed and approved
✓ All tools quality score 8+/10
```

### Security Gate

```
✓ No hardcoded secrets
✓ Environment variables configured
✓ API keys rotated (if needed)
✓ .env file not in git
✓ Permissions properly scoped
```

### Operations Gate

```
✓ Dependencies listed in requirements.txt
✓ Configuration externalized (not hardcoded)
✓ Health check endpoint ready
✓ Logging configured
✓ Monitoring setup planned
```

### Documentation Gate

```
✓ Deployment runbook written
✓ Troubleshooting guide prepared
✓ Rollback procedure documented
✓ Alert escalation paths defined
✓ API documentation current
```

## Deployment Strategies

### Strategy 1: Local Deployment (Development/Testing)

**When**: Before first production deployment
**Process**:

1. Install dependencies: `pip install -r requirements.txt`
2. Configure .env file
3. Start server: `python src/server/index.py`
4. Test connectivity and tools

**Verification**:

```bash
# Check server running
ps aux | grep "python src/server"

# Test tool discovery
mcp-cli list-tools

# Test invocation
mcp-cli invoke weather get_current_weather --lat 51.5 --lon -0.1
```

### Strategy 2: Docker Deployment (Containerized)

**When**: Consistent environments, cloud deployment
**Process**:

1. Build image: `docker build -t mcp-weather:1.0.0 .`
2. Test locally: `docker run -e OPENWEATHERMAP_API_KEY=key mcp-weather:1.0.0`
3. Push to registry: `docker push myregistry.azurecr.io/mcp-weather:1.0.0`
4. Deploy to production

**Dockerfile Example**:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY src/ ./src/
ENV PYTHONUNBUFFERED=1
CMD ["python", "src/server/index.py"]
```

**Verification**:

```bash
docker run --name mcp-test myregistry/mcp-weather:1.0.0
# Wait 5 seconds
docker logs mcp-test
docker stop mcp-test
```

### Strategy 3: Prefect Horizon (Managed Hosting)

**When**: Minimal DevOps effort, managed scaling
**Process**:

1. Push code to GitHub
2. Connect repo to Prefect
3. Configure deployment settings
4. Prefect handles deployment, scaling, monitoring

**Benefits**:

- Automatic scaling
- Built-in monitoring
- Free tier available
- Zero infrastructure management

### Strategy 4: Cloud Functions (Serverless)

**When**: Low traffic, cost-sensitive
**Process**: Adapt server for function runtime (different I/O model)

## Deployment Execution Steps

### Step 1: Pre-Deployment Preparation

```bash
# Verify code and configuration
pytest tests/ --cov=src --cov-report=term-missing
mypy src/

# Build artifact
docker build -t mcp-weather:1.0.0 .
docker run --rm mcp-weather:1.0.0 # Quick validation

# Prepare environment
source .env.production
# Verify all required env vars set
```

### Step 2: Deploy

```bash
# Tag image for production
docker tag mcp-weather:1.0.0 myregistry/mcp-weather:1.0.0

# Push to registry
docker push myregistry/mcp-weather:1.0.0

# Deploy to target environment
# (Cloud-specific commands vary)
```

### Step 3: Verify Deployment

```bash
# Wait for server to start
sleep 10

# Test connectivity
curl http://localhost:8000/health

# Test tool discovery
mcp-cli list-tools

# Test sample invocation
mcp-cli invoke weather get_current_weather --lat 51.5 --lon -0.1

# Monitor logs for errors
docker logs mcp-weather | head -20
```

### Step 4: Validate Performance

```bash
# Measure response times
time mcp-cli invoke weather get_current_weather --lat 51.5 --lon -0.1

# Check error rates (from monitoring)
# Target: <0.1% error rate in first hour

# Verify memory/CPU usage
docker stats mcp-weather
```

## Health Check & Validation

### Connectivity Test

```python
# Verify server responds to requests
response = client.get("http://localhost:8000/health")
assert response.status_code == 200
```

### Tool Discovery Test

```python
# Verify all tools are discoverable
tools = mcp_client.list_tools()
assert len(tools) == 5  # Expect 5 tools
assert any(t.name == "get_current_weather" for t in tools)
```

### Invocation Test

```python
# Test actual tool invocation
result = await mcp_client.invoke(
    "get_current_weather",
    {"lat": 51.5, "lon": -0.1}
)
assert "error" not in result
assert result["temperature"] is not None
```

### Error Handling Test

```python
# Verify error handling works
result = await mcp_client.invoke(
    "get_current_weather",
    {"lat": 95, "lon": -0.1}  # Invalid latitude
)
assert "error" in result
assert "latitude" in result["error"].lower()
```

## Monitoring & Metrics

### Key Metrics to Track

```
- Request count (tools invocations per minute)
- Error rate (% of failed invocations)
- Response time (p50, p95, p99 percentiles)
- Success rate (% of successful tools)
- Memory usage (MB)
- CPU usage (%)
```

### Alert Configuration

```
Alert if:
- Error rate > 5% (something is wrong)
- Response time p95 > 10 seconds (performance degradation)
- Memory > 500MB (resource leak)
- Server restarts unexpectedly
```

### Logging Configuration

```
Log levels:
- ERROR: Tool invocation failures
- WARN: Slow responses (>5s)
- INFO: Tool invocation counts, deployment events
- DEBUG: Parameter details, response samples
```

## Rollback Procedure

### If Deployment Fails

```bash
# Immediately roll back
docker stop mcp-weather
docker run -d --name mcp-weather myregistry/mcp-weather:PREVIOUS_VERSION

# Verify previous version working
curl http://localhost:8000/health

# Investigate failure
docker logs mcp-weather-failed > /tmp/deployment.log

# Fix issue
# Commit fix, redeploy
```

### Rollback Decision Criteria

- Roll back if error rate >10% in first 5 minutes
- Roll back if response times >20 seconds (p95)
- Roll back if unable to discover tools
- Roll back if critical security issue found

## Post-Deployment

### First Hour Monitoring

- Watch error logs closely
- Monitor resource usage
- Track early adoption patterns
- Respond to any alerts

### Performance Baseline

```
Document baseline metrics:
- Response time: 2.3s p95
- Error rate: 0.02%
- Memory: 128MB
- CPU: 15% average
```

### Success Criteria

- Server stable for >30 minutes
- Error rate <1%
- All tools responding
- No critical alerts
- Performance metrics acceptable

## Common Deployment Issues

| Issue                  | Cause                 | Fix                              |
| ---------------------- | --------------------- | -------------------------------- |
| Tools not discoverable | Deployment incomplete | Restart server, check logs       |
| Timeouts on invocation | Slow API responses    | Check external API status        |
| High memory usage      | Resource leak         | Restart server, add gc.collect() |
| API key not working    | Env var not set       | Verify .env configuration        |
| Connection refused     | Server not running    | Check if process crashed         |

## Deployment Runbook Template

```markdown
# MCP Weather Server Deployment Runbook

## Pre-Deployment (15 min)

1. Code review complete and approved
2. All tests passing (pytest)
3. Security scan passed
4. Rollback plan reviewed

## Deployment (10 min)

1. Build Docker image
2. Push to registry
3. Deploy to production
4. Verify connectivity

## Validation (10 min)

1. Health check passes
2. Tools discovered
3. Sample invocations work
4. No errors in logs

## Monitoring (Ongoing)

1. Alert for error rate >5%
2. Alert for response time >10s
3. Daily metrics review
4. Weekly performance review

## Rollback (If needed)

1. Stop current server
2. Revert to previous version
3. Verify working
4. Investigate and fix

## Estimated Deployment Time: 35-45 minutes
```

## Success Metrics

### Deployment Successful If

- ✅ Server running and stable
- ✅ All tools discoverable and functional
- ✅ Error rate <1% in first hour
- ✅ Response times meet baselines
- ✅ Logs show normal operations
- ✅ No critical alerts
- ✅ Team confident in stability

### Deployment Failed If

- ❌ Server crashes on startup
- ❌ Tools not discoverable
- ❌ Error rate >10%
- ❌ Response times >20s (p95)
- ❌ Critical errors in logs
- ❌ External dependencies unavailable

## Reference Materials

- `prompts/deploy-mcp-server.prompt.md` - Deployment planning template
- `README.md` - Project setup and configuration
- `.github/copilot/tech-stack.md` - Technology details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/linkedinlearning) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
