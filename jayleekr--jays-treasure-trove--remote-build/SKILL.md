---
name: remote-build
description: Execute builds and tests on remote servers with Claude Code. Use when building CCU2, Yocto images, or running integration tests on build servers. Use when this capability is needed.
metadata:
  author: jayleekr
---

# Remote Build Agent

리모트 빌드 서버에서 Claude Code를 실행하여 빌드/테스트 수행.

## 🖥️ Registered Build Servers

| Alias | Host | Purpose | Claude Code |
|-------|------|---------|-------------|
| `ccu2-builder` | 192.168.1.100 | CCU2 Host 빌드 | ✅ Installed |
| `yocto-builder` | 192.168.1.101 | Yocto 이미지 빌드 | ✅ Installed |
| `test-runner` | 192.168.1.102 | 통합 테스트 | ✅ Installed |

## 📊 Current Remote Status

!`for h in ccu2-builder yocto-builder test-runner; do echo -n "$h: "; ssh -o ConnectTimeout=2 $h "echo OK" 2>/dev/null || echo "OFFLINE"; done`

## 🚀 Usage

### Basic Build
```
/remote-build container-manager
/remote-build container-manager on ccu2-builder
```

### Build with Branch
```
/remote-build container-manager --branch CCU2-16964
```

### Run Tests
```
/remote-build test container-manager on test-runner
```

### Yocto Image Build
```
/remote-build yocto linux-s32 on yocto-builder
```

## 🔧 Workflow

### 1. Parse Request

사용자 요청에서 추출:
- **Target**: 빌드할 모듈 (container-manager, vam, linux-s32, etc.)
- **Server**: 빌드 서버 (기본: 자동 선택)
- **Action**: build | test | build-and-test
- **Branch**: Git 브랜치 (optional)

### 2. Execute Remote Claude Code

**Headless 모드로 실행**:
```bash
ssh $SERVER "cd $WORKDIR && claude -p '$PROMPT' \
  --allowedTools 'Bash,Read,Edit,Grep' \
  --output-format json \
  --max-turns $MAX_TURNS \
  --dangerously-skip-permissions"
```

**프롬프트 템플릿**:
```
Build and test $TARGET module.

1. Check current branch: git status
2. Pull latest changes if needed
3. Run build: ./build.py -m $TARGET
4. If errors occur, analyze and fix them
5. Re-run build until success or max attempts
6. Run relevant tests
7. Report final status
```

### 3. Parse Results

Claude Code JSON 출력에서 추출:
- `result`: 최종 응답
- `session_id`: 세션 ID (재개용)
- `usage`: 토큰 사용량

### 4. Report to User

**성공시**:
```
## ✅ Remote Build Complete

**Server**: ccu2-builder
**Target**: container-manager
**Duration**: 15m 23s
**Status**: SUCCESS

### Build Output
- Compiled 42 files
- No errors, 3 warnings

### Test Results
- Passed: 28/28
- Coverage: 85%
```

**실패시**:
```
## ❌ Remote Build Failed

**Server**: ccu2-builder
**Target**: container-manager
**Duration**: 8m 12s
**Status**: FAILED

### Error Summary
- Build error in src/docker_client.cpp:142
- Claude attempted 3 fixes but could not resolve

### Recommended Actions
1. Check the error log on server
2. Resume session: ssh ccu2-builder "claude --resume $SESSION_ID"
```

## 🔄 Advanced: Parallel Builds

여러 서버에서 동시 빌드:

```bash
# 로컬에서 spawn
/remote-build container-manager on ccu2-builder &
/remote-build vam on ccu2-builder &
/remote-build linux-s32 on yocto-builder &

# 결과 모니터링
/tasks
```

## 📁 Scripts

### remote-claude.sh
```bash
#!/bin/bash
# Usage: ./remote-claude.sh <host> <workdir> "<prompt>" [max_turns]

HOST=$1
WORKDIR=$2
PROMPT=$3
MAX_TURNS=${4:-15}

ssh -o ConnectTimeout=30 "$HOST" << EOF
cd "$WORKDIR"
claude -p "$PROMPT" \
  --allowedTools "Bash,Read,Edit,Grep,Glob" \
  --output-format json \
  --max-turns $MAX_TURNS \
  --dangerously-skip-permissions
EOF
```

### result-parser.py
```python
#!/usr/bin/env python3
import json
import sys

def parse_result(json_output):
    data = json.loads(json_output)
    return {
        "status": "success" if data.get("result") else "failed",
        "result": data.get("result", ""),
        "session_id": data.get("session_id", ""),
        "tokens": data.get("usage", {}).get("total_tokens", 0)
    }

if __name__ == "__main__":
    print(json.dumps(parse_result(sys.stdin.read()), indent=2))
```

## ⚙️ Server Setup

### Prerequisites (on each build server)

1. **Install Claude Code**:
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

2. **Authenticate**:
```bash
claude auth login
# 또는 API key 설정
export ANTHROPIC_API_KEY="sk-ant-..."
```

3. **SSH Key Setup** (from local):
```bash
ssh-copy-id user@ccu2-builder
```

4. **Verify**:
```bash
ssh ccu2-builder "claude -p 'Hello' --output-format json"
```

## 🛡️ Security Notes

- `--dangerously-skip-permissions` 사용시 주의
- 빌드 서버는 격리된 환경 권장
- API 키는 환경 변수로만 관리
- SSH는 키 기반 인증만 사용

## 📝 Memory

이 스킬은 `memory: project`로 설정되어 있어서:
- 이전 빌드 결과 기억
- 반복되는 에러 패턴 학습
- 서버별 최적 설정 추적

---

*Remote Build Agent v1.0 | 2026-02-06*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jayleekr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
