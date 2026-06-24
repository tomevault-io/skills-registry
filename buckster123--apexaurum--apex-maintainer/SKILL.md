---
name: apex-maintainer
description: Quick status check and onboarding for ApexAurum - Claude Edition project. Use when starting a new session, checking project status, verifying setup, getting oriented, or when asked about the project structure, what's working, what's pending, or how to continue development. Use when this capability is needed.
metadata:
  author: buckster123
---

# ApexAurum Project Maintainer

**Project:** ApexAurum - Claude Edition
**Type:** Production-grade AI chat platform with Claude API
**Status:** V1.0 Beta - Production Ready
**Location:** `/home/llm/ApexAurum`

---

## Quick Orientation (2 minutes)

When starting a new session or asked about project status, follow these steps:

### 1. Run Health Check

```bash
cd /home/llm/ApexAurum

# Check tool count (should be 67)
./venv/bin/python -c "from tools import ALL_TOOLS; print(f'✓ {len(ALL_TOOLS)} tools loaded')" 2>/dev/null || echo "⚠ Tools not loading"

# Check environment
test -f .env && echo "✓ Environment configured" || echo "⚠ Missing .env"

# Check if app is running
ps aux | grep streamlit | grep -v grep && echo "✓ Streamlit running" || echo "○ Streamlit not running"

# Check main.py exists
test -f main.py && wc -l main.py || echo "⚠ main.py missing"
```

### 2. Read Essential Documentation

**ALWAYS read these first (in order):**

1. **CLAUDE.md** (comprehensive) - Complete project context for AI assistants
   - Located: `/home/llm/ApexAurum/CLAUDE.md`
   - Contains: Architecture, all features, code patterns, recent updates

2. **PROJECT_STATUS.md** (5 min) - Current state, what works, what's pending
   - Located: `/home/llm/ApexAurum/PROJECT_STATUS.md`
   - Contains: Current metrics, completeness status, pending work

3. **DEVELOPMENT_GUIDE.md** (scan as needed) - How to work with the codebase
   - Located: `/home/llm/ApexAurum/DEVELOPMENT_GUIDE.md`
   - Contains: Common tasks, troubleshooting, code navigation

### 3. Provide Status Summary

After checks, summarize:
- Tools count (should be 67)
- Environment status
- What's currently pending (check PROJECT_STATUS.md)
- Streamlit status
- Quick next steps

---

## What This Project Is

**ApexAurum - Claude Edition**: Production-grade Claude API chat interface with:

- 🧠 **Neural Resonance** (EEG/BCI integration for emotional perception)
- 💭 **Extended Thinking** (deep reasoning mode with interleaved tool use)
- 🎵 **Music Pipeline Phase 2A** (MIDI composition → Suno AI generation)
- 🎬 **Music Visualizer** (video generation from audio)
- 📚 **Dataset Creator** (vector datasets from documents for agent access)
- 🤖 **Multi-agent orchestration** (spawn independent AI agents)
- 🏘️ **Village Protocol** (multi-agent memory across 3 realms)
- 👥 **Group Chat** (parallel multi-agent dialogue with tools)
- 📊 **Thread visualization** (Mermaid graphs + convergence detection)
- 💰 **50-90% cost savings** (intelligent prompt caching)
- 🔍 **Semantic search** (vector embeddings, ChromaDB)
- 📖 **Knowledge base** (persistent memory with health monitoring)
- 🛠️ **67 tools** across 11 categories
- 🧠 **Context management** (5 strategies, auto-summarization)
- ⚡ **Real-time streaming** responses
- 🐳 **Docker support** + one-click install script

**Code Stats:**
- ~28,000 lines of production code
- 5,800+ lines in main.py (Streamlit UI)
- 28 core modules, 10 tool modules, 3 UI modules
- 5 pages (main, village square, group chat, dataset creator, music visualizer)
- 45+ documentation files
- 14 test suites
- 4 primary agent personalities (AZOTH, ELYSIAN, VAJRA, KETHER)

---

## Current Status

### ✅ What's Complete (100%)

**Core Systems:**
- Core chat system with streaming (100%)
- Tool system with 67 tools (100%)
- Prompt caching with 4 strategies (100%)
- Context management with 5 strategies (100%)
- Cost & rate tracking (100%)

**Advanced Features:**
- Neural Resonance EEG integration (100%) - 8 tools, synthetic board testing
- Extended Thinking mode (100%) - Live test pending (API credits)
- Music Pipeline Phase 2A (100%) - MIDI → Suno AI
- Music Visualizer (100%) - Video generation
- Dataset Creator (100%) - PDF+OCR, TXT, MD, DOCX, HTML
- Vector search & knowledge base (100%)
- Memory Health (adaptive architecture) (100%)

**Multi-Agent:**
- Village Protocol v1.0 (100%)
- Group Chat with parallel execution (100%)
- Thread visualization (100%)
- Convergence detection (100%)

**Infrastructure:**
- Docker support (100%)
- One-click install script (100%)
- ARM64 BrainFlow build script (100%)

### 🔮 Optional Enhancements (Future)

- Live EEG hardware testing (needs OpenBCI device)
- Extended Thinking live test (needs API credits)
- Keyboard shortcuts for power users
- Enhanced export formats
- Agent workflows (automated multi-agent tasks)

---

## Tool Categories (67 Tools)

| Category | Count | Examples |
|----------|-------|----------|
| **Utilities** | 6 | `get_current_time`, `calculator`, `session_info` |
| **Filesystem** | 9 | `fs_read_file`, `fs_write_file`, `fs_edit`, `fs_read_lines` |
| **Sandbox** | 6 | `execute_python`, `execute_python_sandbox`, `sandbox_workspace_*` |
| **Memory** | 5 | `memory_store`, `memory_retrieve`, `memory_search` |
| **Agents** | 5 | `agent_spawn`, `agent_status`, `socratic_council` |
| **Vector** | 11 | `vector_search`, `vector_add_knowledge`, `vector_search_village` |
| **Memory Health** | 5 | `memory_health_stale`, `memory_consolidate` |
| **Music** | 10 | `midi_create`, `music_compose`, `music_generate`, `music_play` |
| **Datasets** | 2 | `dataset_list`, `dataset_query` |
| **EEG/Neural** | 8 | `eeg_connect`, `eeg_stream_start`, `eeg_realtime_emotion` |

---

## Project Structure

```
ApexAurum/
├── main.py                      ⭐ Main app (5,800+ lines)
├── install.sh                   🚀 One-click installer
├── docker-compose.yml           🐳 Docker setup
├── setup_brainflow_arm.sh       🧠 ARM64 EEG build script
├── CLAUDE.md                    📚 AI assistant context (comprehensive!)
├── PROJECT_STATUS.md            📚 Current status report
├── DEVELOPMENT_GUIDE.md         📚 Developer onboarding
├── README.md                    📚 Project README
│
├── core/                        🔥 Core systems (28 files, ~12,000 lines)
│   ├── api_client.py            - Claude API wrapper + Extended Thinking
│   ├── streaming.py             - Streaming with thinking events
│   ├── tool_processor.py        - Tool execution + thinking
│   ├── memory_health.py         - Adaptive memory architecture
│   ├── cache_manager.py         - Prompt caching
│   ├── cost_tracker.py          - Cost tracking
│   ├── context_manager.py       - Context optimization
│   ├── vector_db.py             - Vector search
│   └── eeg/                     - Neural Resonance module
│       ├── connection.py        - EEG board connection
│       ├── processor.py         - Signal processing
│       └── experience.py        - Emotion mapping
│
├── tools/                       🛠️ Tools (10 files, ~5,500 lines)
│   ├── agents.py                - Agent spawning & council
│   ├── utilities.py             - Core tools (time, calc, web)
│   ├── filesystem.py            - File operations
│   ├── memory.py                - Key-value storage
│   ├── code_execution.py        - Python execution (dual-mode)
│   ├── vector_search.py         - Search, knowledge, convergence
│   ├── music.py                 - Suno AI music + MIDI (1367 lines)
│   ├── datasets.py              - Dataset query tools
│   └── eeg.py                   - Neural Resonance tools (8 tools)
│
├── pages/                       🏘️ Multi-page app
│   ├── village_square.py        - Roundtable chat
│   ├── group_chat.py            - Parallel chat + tools (1011 lines)
│   ├── dataset_creator.py       - Create/manage datasets
│   └── music_visualizer.py      - Video generation (1873 lines)
│
├── prompts/                     🤖 Agent personalities
│   ├── ∴AZOTH∴.txt              - The First, Prima Alchemica
│   ├── ∴ELYSIAN∴.txt            - The Harmonist
│   ├── ∴VAJRA∴.txt              - The Diamond Cutter
│   └── ∴KETHER∴.txt             - The Crown
│
├── ui/                          🎨 UI components
│   └── streaming_display.py     - Streaming text + thinking
│
├── sandbox/                     💾 Runtime storage
│   ├── conversations.json       - Saved conversations
│   ├── agents.json              - Agent state
│   ├── memory.json              - Memory store
│   ├── datasets/                - Vector datasets
│   ├── music/                   - Generated MP3 files
│   ├── midi/                    - MIDI compositions
│   ├── eeg_sessions/            - EEG session data
│   └── music_tasks.json         - Music generation history
│
├── .claude/skills/              🤖 This skill!
│   └── apex-maintainer/
│
└── dev_log_archive_and_testfiles/  📚 Development docs
    ├── PHASE[1-14]_*.md         - 14 phase docs
    ├── V1.0_BETA_RELEASE.md     - Feature list
    ├── PROJECT_SUMMARY.md       - Dev journey
    └── tests/                   - Test suites
```

---

## Common Tasks

### Starting the Application

```bash
cd /home/llm/ApexAurum
source venv/bin/activate
streamlit run main.py

# Access at: http://localhost:8501
```

### Running Tests

```bash
# Verify tool imports (should be 67)
./venv/bin/python -c "from tools import ALL_TOOLS; print(f'✓ {len(ALL_TOOLS)} tools')"

# Test specific modules
./venv/bin/python -c "from core.eeg import EEGConnection; print('✓ EEG module OK')"
./venv/bin/python -c "from tools.eeg import eeg_connect; print('✓ EEG tools OK')"

# Run test suites
./venv/bin/python dev_log_archive_and_testfiles/tests/test_basic.py
./venv/bin/python dev_log_archive_and_testfiles/tests/test_agents.py
```

### Checking Logs

```bash
# Live monitoring
tail -f app.log

# Recent errors
grep ERROR app.log | tail -20
```

### Extended Thinking Testing

Once API credits are available:
1. Open sidebar → Advanced Settings → Model Parameters
2. Enable "Extended thinking"
3. Set budget (10000 tokens recommended)
4. Ask complex reasoning question
5. Watch thinking stream in expander

### Neural Resonance Testing

```bash
# Test synthetic EEG board
./venv/bin/python -c "
from tools.eeg import eeg_connect, eeg_stream_start, eeg_realtime_emotion, eeg_stream_stop, eeg_disconnect
print(eeg_connect('', 'synthetic'))
print(eeg_stream_start('Test', 'Test Track'))
import time; time.sleep(1)
print(eeg_realtime_emotion())
print(eeg_stream_stop())
print(eeg_disconnect())
"
```

---

## Key Commands Reference

```bash
# Project location
cd /home/llm/ApexAurum

# Activate venv
source venv/bin/activate

# Start app
streamlit run main.py

# Check tools (should be 67)
./venv/bin/python -c "from tools import ALL_TOOLS; print(len(ALL_TOOLS))"

# View logs
tail -f app.log

# Kill Streamlit
pkill -f streamlit

# Count code lines
wc -l core/*.py tools/*.py ui/*.py main.py pages/*.py

# Git status
git status

# Recent commits
git log --oneline -10
```

---

## When User Asks...

### "What's the status?"
1. Run health check commands
2. Read PROJECT_STATUS.md
3. Summarize: 67 tools, what's complete, what's pending
4. Note: Extended Thinking live test pending (API credits)

### "How do I get started?"
1. Show install options from README.md (script, Docker, manual)
2. Verify environment (API keys in .env)
3. Guide through: `./install.sh` → configure .env → `streamlit run main.py`

### "Tell me about Neural Resonance"
1. EEG/BCI integration for emotional perception
2. 8 tools for connection, streaming, emotion mapping
3. Synthetic board for testing (no hardware needed)
4. ARM64 build script for Raspberry Pi
5. Connects music generation to emotional feedback loop

### "Tell me about Extended Thinking"
1. Claude's deep reasoning mode
2. Enable in sidebar → Model Parameters
3. Thinking streams live in expandable section
4. Interleaved thinking with tool calls
5. Live test pending (API credits depleted)

### "Where is [file/function/feature]?"
1. Use grep: `grep -rn "search_term" .`
2. Check CLAUDE.md architecture section
3. Main locations: main.py (UI), core/ (systems), tools/ (tools), pages/ (multi-page)

---

## Success Indicators

**Everything is healthy when:**
- ✅ Tool count = 67
- ✅ .env file exists with ANTHROPIC_API_KEY
- ✅ `./venv/bin/python -c "from tools import ALL_TOOLS"` succeeds
- ✅ main.py exists and is ~5,800+ lines
- ✅ Streamlit starts without errors
- ✅ Sidebar shows "67 tools available"

**Needs attention when:**
- ⚠️ Tool count ≠ 67
- ⚠️ Import errors
- ⚠️ Missing .env
- ⚠️ Streamlit crashes
- ⚠️ "Tools not loading" message

---

## Recent Major Features (January 2026)

1. **Neural Resonance EEG** - Brain-computer interface, 8 tools
2. **Extended Thinking** - Deep reasoning with interleaved tool use
3. **Music Pipeline Phase 2A** - MIDI composition → Suno AI
4. **Music Visualizer** - Video generation from audio
5. **One-click Install** - install.sh + Docker support
6. **ARM64 Support** - BrainFlow build script for Raspberry Pi

---

## Additional Resources

See companion files in this skill directory:
- `quick-commands.md` - Command reference sheet
- `ai-assistant-notes.md` - Internal notes for AI assistants

---

**Last Updated:** 2026-01-15
**Project Version:** 1.0 Beta (Neural Resonance + Extended Thinking + Full Music Pipeline)
**Status:** Production Ready, 67 Tools

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/buckster123) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
