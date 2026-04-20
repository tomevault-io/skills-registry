---
name: sandbox
description: Deploy and test applications in Buddy Sandbox cloud environments. Use when user asks about "deploy app", "create sandbox", "test in cloud", "isolated environment", "remote environment", "run app in sandbox", or mentions deploying, testing, or running applications in cloud sandboxes. Use when this capability is needed.
metadata:
  author: sztwiorok
---

# Buddy Sandbox Deployment

On-demand cloud environments for deploying and testing applications with public HTTP/TCP endpoints.

## Preinstalled Software

Ubuntu 24.04.4 LTS
Sandboxes come with the following software preinstalled:

- **Node.js 24** 
- **Python 3.12** 
- **go 1.25**
- **Git**, **curl**, **jq**, **ripgrep**, 

No need to install these — they're ready to use immediately after sandbox creation.

## Authentication Errors

If any `bdy` command returns `Token not provided` or `Not logged in`, ask the user to run `bdy login` in a separate terminal (interactive browser auth — AI cannot do it).

## Workflow

### 1. Create Sandbox

**Basic creation:**
```bash
bdy sandbox create -i my-app --resources 2x4 --wait-for-running
```


**Options:**
- Resources: `1x2`, `2x4`, `4x8`, `8x16`, `12x24` (CPUxRAM)
- `--wait-for-configured` - wait until install commands complete

### 2. Copy Files to Sandbox

```bash
bdy sandbox cp ./src my-app:/app > /dev/null 2>&1
```

**Redirect output to `/dev/null`** - without it, file copy floods stdout and breaks execution.

### 3. Execute Commands

Commands run in **background by default**.

**With `--wait`** for operations that must complete before next step, logs are visible in output:
```bash
bdy sandbox exec command my-app "cd /app && npm install" --wait
```

**Without `--wait`** for long-running processes (servers):
```bash
bdy sandbox exec command my-app "cd /app && npm start"
```

**Check status and logs:**
```bash
bdy sandbox exec list my-app                        # list commands
bdy sandbox exec logs my-app <command-id>           # view logs
bdy sandbox exec logs my-app <command-id> --wait    # wait for completion and show logs
```


**Creating config files:** Write locally, then copy - avoids escaping issues with heredoc through exec:
```bash
# 1. Write file locally
cat > config.json << 'EOF'
{"host": "localhost", "port": 3000}
EOF

# 2. Copy to sandbox
bdy sandbox cp config.json my-app:/app/ > /dev/null 2>&1
```

### 4. Add Endpoints (optional)

```bash
bdy sandbox endpoint add my-app -n web -e 3000
```

**Application MUST bind to `0.0.0.0`** (not `127.0.0.1` or `localhost`) - otherwise endpoint won't work.

**With authentication:**
```bash
bdy sandbox endpoint add my-app -n web -e 3000 --auth BASIC --username admin --password secret
```

**Check endpoint URL:**
```bash
bdy sandbox endpoint list my-app
```

**Reverse proxy:** Apps run behind reverse proxy. Handle `X-Forwarded-Proto` for correct HTTPS detection:
```js
// Express.js
app.set('trust proxy', true);
```
```php
// PHP
if ($_SERVER['HTTP_X_FORWARDED_PROTO'] === 'https') $_SERVER['HTTPS'] = 'on';
```

### 5. Copy Files from Sandbox

```bash
# Copy file from sandbox
bdy sandbox cp my-app:/app/result.txt ./result.txt > /dev/null 2>&1

# Copy directory from sandbox
bdy sandbox cp my-app:/app/output ./output > /dev/null 2>&1
```

**When destination already exists**, use `--merge` or `--replace`:
```bash
# Replace existing file/directory
bdy sandbox cp my-app:/app/results ./results --replace > /dev/null 2>&1

# Merge into existing directory
bdy sandbox cp my-app:/app/results ./results/ --merge > /dev/null 2>&1
```

**Tip:** Run `bdy sandbox cp --help` for detailed examples of merge/replace behavior with files and directories.           

## CRITICAL: Read Examples Before Deploying These Tech Stacks

- [Node.js](references/examples/nodejs.md)
- [Python Flask](references/examples/python-flask.md)
- [Node.js + PostgreSQL](references/examples/nodejs-postgresql.md)
- [WordPress + MySQL](references/examples/wordpress.md)
- [phpMyAdmin](references/examples/phpmyadmin.md) - database management UI for WordPress/MySQL sandbox

## References

- [Full command reference](references/commands.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sztwiorok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
