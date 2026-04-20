---
name: setup
description: Bootstrap a new business repo with Main Branch structure. Use when: (1) New user needs Claude Code environment configured (2) User says "set up", "get started", "initialize", "bootstrap", "create my repo", "new business" (3) User is new to Main Branch and needs full onboarding (4) Migrating existing business context into the Main Branch structure. Creates two-repo model, business repo with core structure. Gathers context aggressively until complete. Use when this capability is needed.
metadata:
  author: mainbranch-ai
---

# Repo Setup

Get a new user fully configured with Claude Code and their business repo.

---

## Before We Begin

**Need help?** Type `/help` + your question anytime. If conversation compacts (gets summarized), `/help` reloads fresh context.

---

## Workflow

### Pull Latest Updates (Always)

**Before anything else, ensure mb-free is up to date:**

```bash
# If mb-free is an added directory or we can find it
cd ~/Documents/GitHub/mb-free 2>/dev/null && git pull origin main 2>/dev/null && cd - >/dev/null || true
```

If updates pulled: briefly note "Pulled latest mb-free updates." then continue.
If offline or already current: continue silently.

---

### Check Location -- Create Business Repo if Needed (CRITICAL: DO THIS FIRST)

**This must happen BEFORE context gathering.** If the conversation compacts later, the essential config is already saved.

**Check if we're in the mb-free (engine) repository:**

```bash
# If this succeeds, we're in mb-free
ls .claude/skills/setup/SKILL.md 2>/dev/null
```

**If we're in mb-free, CREATE the business repo for them:**

> "You're in mb-free -- that's the engine. Let me create your business repo."

1. **Ask for business name:**
   > "What do you want to call your business folder? (e.g., 'my-agency', 'acme-coaching')"

2. **Create the folder and init git:**
   ```bash
   mkdir -p ~/Documents/GitHub/[business-name]
   cd ~/Documents/GitHub/[business-name] && git init
   ```

3. **IMMEDIATELY save to machine-local settings:**

   This ensures `/start` can find the business repo in future sessions.

   Create/update `~/.config/mb-free/local.yaml`:
   ```bash
   mkdir -p ~/.config/mb-free
   ```

   ```yaml
   # ~/.config/mb-free/local.yaml
   # Machine AND user specific -- NOT git-tracked
   default_repo: /Users/[username]/Documents/GitHub/[business-name]
   recent_repos:
     - /Users/[username]/Documents/GitHub/[business-name]

   # User identity lives here
   user:
     name: "[User's name]"
     experience: beginner  # beginner | intermediate | advanced
   ```

   Use the actual expanded path (not ~). Ask user for their name and experience level.

4. **Add it as a working directory:**
   ```
   /add-dir ~/Documents/GitHub/[business-name]
   ```

5. **Set the business repo as the target for all file writes:**
   From this point forward, write all files to `~/Documents/GitHub/[business-name]/` NOT to the current directory.

6. **Confirm the setup is saved:**
   > "Created [business-name] and saved the path. From now on, just run `/start` in mb-free and it'll load your business repo automatically.
   >
   > **Reminder:** If you ever get confused or the conversation compacts, type `/help` + your question. It has comprehensive answers about how everything works."

7. **Continue with setup** -- proceed to Step 1 and beyond.

**If NOT in mb-free:** You're already in the user's business repo. Check if mb-free path is configured, continue normally.

---

### 1. Confirm Git + Working Directory

```bash
git status  # Verify we're in a git repo
pwd         # Confirm working directory
```

If not a git repo:
```bash
git init
```

### 2. Ask Business Type

> What type of business is this?

| Type | What We Set Up |
|------|----------------|
| **Community/Skool** | Core reference + proof |
| **E-commerce** | Core reference + proof |
| **Coaching/Services** | Core reference + proof |
| **Agency** | Core reference + proof |
| **Other** | Core reference + proof |

**Note:** The free version creates the same core structure for all business types. Domain-specific folder structures (classroom, products, membership, etc.) are available with Main Branch Premium.

### 3. Gather Context (Be a Ruthless Journalist)

Your job: extract every fact possible. Don't settle for partial info. Users provide context in batches -- keep asking until YOU say "we have enough."

See **[references/context-gathering.md](references/context-gathering.md)** for:
- URL fetching fallback chain (WebFetch -> Chrome -> Playwright -> manual)
- Business-type specific checklists
- Completeness criteria

**Opening prompt:**
> Dump everything about this business -- sales pages, offer details, testimonials, notes, whatever exists.
>
> **Pro tip:** You can drag screenshots directly into this terminal window and I'll read them. If you have a Skool community, screenshot your about page, classroom, pricing -- drag them all in. Fastest way to get me up to speed.
>
> Paste text, share file paths, give me URLs to fetch, or drag in images. I'll sort it all into the right files.

**After each batch, assess gaps:**
> "Got it. I still need [X, Y, Z] to complete your reference files. Can you share those?"

**Only say "we have enough" when you can fill:**
- offer.md (price, mechanism, deliverables, guarantee)
- audience.md (who, pains, desires, objections)
- voice.md (tone, phrases, personality)
- testimonials.md (3-5 with specific outcomes)

### 4. Create Folder Structure

```bash
mkdir -p reference/core reference/brand reference/proof/angles reference/domain
mkdir -p research decisions outputs
```

Full structure:
```
{business-name}/
├── CLAUDE.md              # Always loaded - business brain
├── README.md              # Human-readable overview
├── .gitignore             # Include .env
│
├── reference/             # Evergreen truth
│   ├── core/              # REQUIRED
│   │   ├── offer.md       # What you sell
│   │   ├── audience.md    # Who buys
│   │   └── voice.md       # How you sound
│   ├── brand/             # Deep brand systems (optional)
│   ├── proof/
│   │   ├── testimonials.md
│   │   └── angles/        # Proven messaging entry points
│   └── domain/            # Business-type specific (Premium feature)
│
├── research/              # Dated investigations
│   └── YYYY-MM-DD-topic-[source].md
│
├── decisions/             # Dated choices with rationale
│   └── YYYY-MM-DD-topic.md
│
└── outputs/               # Generated assets
    └── YYYY-MM-DD-batch-name/
```

### 4a. Create .gitignore

```bash
cat > .gitignore << 'EOF'
# Secrets
.env
*.env.local

# OS
.DS_Store

# Editor
.vscode/
.idea/
EOF
```

### 5. Sort Content into Files

**The repo is a precision instrument, not a dumping ground.** Not everything the user provides makes it into reference files. Filter for what helps LLMs produce great outputs.

Use templates from `references/templates.md`.

**Priority order:**
1. `reference/core/offer.md` -- What you sell
2. `reference/core/audience.md` -- Who buys
3. `reference/core/voice.md` -- How you sound
4. `reference/proof/testimonials.md` -- Social proof
5. `reference/proof/angles/` -- Messaging entry points

### 6. Draft CLAUDE.md

See `references/claude-md-guide.md` for structure.

**Key sections:**
- One-line description
- Engine reference (mb-free)
- Folder structure diagram
- Business summary
- Quick reference (audience, voice, offer)
- Key decisions/research index
- Reference tiers

### 7. Create README.md

Simple human-readable overview:
- What the business is
- How to use the repo with mb-free
- Quick stats

### 8. Initial Commit

```bash
git add -A
git commit -m "$(cat <<'EOF'
[init] Bootstrap business repo with Main Branch structure

- Created reference/core/ (offer, audience, voice)
- Created reference/proof/ (testimonials, angles)
- Drafted CLAUDE.md and README.md

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
EOF
)"
```

### 9. Report Gaps

| File | Status |
|------|--------|
| core/offer.md | Complete / Missing [X] |
| core/audience.md | Complete / Missing [X] |
| core/voice.md | Complete / Missing [X] |
| proof/testimonials.md | Has content / Empty |
| proof/angles/ | [N] angles / None yet |

Ask user for missing pieces or note for later.

---

## Git Workflow

Always use GitHub CLI with descriptive commits:

**Commit message format:**
```
[type] Brief description

- Detail 1
- Detail 2

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>
```

**Types:**
- `[init]` -- Initial setup
- `[add]` -- New files/features
- `[update]` -- Changes to existing
- `[fix]` -- Bug fixes
- `[refactor]` -- Structure changes
- `[docs]` -- Documentation only

See `references/git-workflow.md` for full guide.

---

## References

- **Context Gathering:** `references/context-gathering.md` -- Checklists by business type, completeness criteria
- **Templates:** `references/templates.md` -- All file templates
- **CLAUDE.md Guide:** `references/claude-md-guide.md` -- How to draft a good CLAUDE.md
- **Git Workflow:** `references/git-workflow.md` -- Commit messages and CLI usage

---

## Recovering from Compaction

If conversation compacts mid-setup:

**For the user:** Type `/setup` again and describe where you were:
- "We're in the middle of gathering context for my Skool community"
- "I was giving you screenshots, you hadn't created files yet"
- "You created the folder structure but we haven't sorted content"

**For Claude:** When resuming:
1. Check if business repo exists (look for `reference/core/`)
2. If exists, check which files are populated vs empty
3. Resume from the appropriate step based on what's done
4. Confirm with user: "I see [business-name] with [X] files. Looks like we're at step [N]. Continue?"

---

## After Setup: What's Next

Once setup is complete, tell the user:

> "Your business repo is ready! Here's what to do next:
>
> **Daily workflow:**
> ```
> cd ~/Documents/GitHub/mb-free
> claude
> /start
> ```
>
> **Key skills to try:**
> - `/think` -- Research topics, make decisions, update reference
> - `/help` -- Get answers anytime you're stuck
>
> **The core loop:** Use `/think` regularly. Research -> Decide -> Codify. This is how your reference files get smarter over time.
>
> **Want more?** Output generation skills (ads, organic content, video scripts, landing pages) are available with Main Branch Premium. Learn more at skool.com/main-branch
>
> **Remember:** Type `/help` + your question anytime. It has comprehensive answers about Terminal basics, the two-repo model, skills, troubleshooting, and more."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mainbranch-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
