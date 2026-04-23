---
name: dev-start
description: Start development servers for a project with health checks. Use when starting local development, spinning up services, or launching dev environment. Use when this capability is needed.
metadata:
  author: allanninal
---

# Development Server Starter

Start development servers for projects with proper health checks and dependency management.

## Arguments
- `$ARGUMENTS`: Project name or path (optional - uses current directory if not specified)

## Known Projects Configuration

| Project | Port | Start Command | Health Endpoint |
|---------|------|---------------|-----------------|
| eruditiontx-services-mvp | 8000 | `uv run python server/app.py` | `/health` |
| mathmatterstx-services | 8002 | `uv run python main.py` | `/health` |
| eruditiontx-client-mvp | 3000 | `npm run dev` | `/` |
| notaryo.ph | 3000 | `pnpm dev` | `/` |
| bocs-turbo | 3000 | `pnpm dev` | `/` |
| agila-tax (frontend) | 3000 | `npm run dev` | `/` |
| agila-tax (backend) | varies | `npm run dev` | `/health` |

## Execution Steps

### 1. Detect Project Type

Check for configuration files:
- `pyproject.toml` or `requirements.txt` → Python project
- `package.json` → Node.js project
- `pnpm-lock.yaml` → Use pnpm
- `uv.lock` → Use uv for Python

### 2. Check Dependencies

**Python:**
```bash
# If uv.lock exists
uv sync
# else
pip install -r requirements.txt
```

**Node.js:**
```bash
# Check package manager
if [ -f "pnpm-lock.yaml" ]; then
    pnpm install
elif [ -f "yarn.lock" ]; then
    yarn install
else
    npm install
fi
```

### 3. Check Port Availability

```bash
lsof -i :PORT
```

If port is in use, warn the user and offer to kill the process.

### 4. Start Server

Run the appropriate start command based on project detection.

**FastAPI (Python):**
```bash
cd ~/Projects/$PROJECT
uv run python server/app.py &
# or
uv run uvicorn app.main:app --reload --port 8000 &
```

**Next.js/React:**
```bash
cd ~/Projects/$PROJECT
npm run dev &
# or
pnpm dev &
```

### 5. Health Check

Wait for server to be ready:
```bash
# Poll health endpoint (max 30 seconds)
for i in {1..30}; do
    if curl -s http://localhost:PORT/health > /dev/null 2>&1; then
        echo "Server is ready!"
        break
    fi
    sleep 1
done
```

## Multi-Service Mode

For full-stack development, start multiple services:

```bash
# Example: Start Erudition full stack
/dev-start eruditiontx-services-mvp  # Backend on 8000
/dev-start eruditiontx-client-mvp    # Frontend on 3000
```

## Output Format

```
Starting: [project-name]
Directory: ~/Projects/[project]
Command: [start command]
Port: [port]

[Server output...]

Health check: PASSED
Server running at: http://localhost:[port]
```

## Troubleshooting

If server fails to start:
1. Check if port is already in use
2. Verify environment variables (.env file exists)
3. Check dependencies are installed
4. Review error logs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
