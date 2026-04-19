---
name: custom-statusline
description: Set up custom Claude Code status line with Nerd Font icons, colors, git status, usage tracking, system info, and project context. NO EMOJIS - only Nerd Font icons and symbols. Use when configuring statusline, setting up status bar, or customizing Claude UI. Triggers with statusline, status bar, custom theme. Use when this capability is needed.
metadata:
  author: zeulewan
---

# Custom Status Line Setup

Sets up a comprehensive Claude Code status line with **Nerd Font icons only** (no emojis), ANSI colors, real-time usage tracking, git integration, and system monitoring.

**IMPORTANT**: This setup uses **ONLY Nerd Font icons** - no emoji characters. All icons are from Nerd Fonts using hex escape codes.

## Features

### Git Integration
- ✓/✗ Clean/dirty status indicator
- Modified file count
- Ahead/behind tracking (↑X ↓Y)
- Stash count display
- Branch name with color coding

### Usage Tracking
- **5-hour and 7-day usage percentages** from Anthropic API
- Color-coded thresholds (green <50%, yellow 50-80%, red >80%)
- Warning indicator (⚠) showing which limit is closer
- Cached for 60 seconds to avoid API spam

### Token Display
- Input/output tokens for current turn
- **Cache read tokens** (⚡) shown separately (10x cheaper!)
- Session context window percentage

### Project Context
- Python version () when in Python project - Nerd Font Python icon
- Node version () when in Node project - Nerd Font Node icon
- Running Docker containers () - Nerd Font Docker icon

### System Info
- Battery level () with color coding - Nerd Font battery icons (full/half/low)
- WiFi network name () - Nerd Font WiFi icon

### Cost & Duration
- Session cost in USD
- Total duration in minutes

## Workflow

### Step 1: Create Status Line Script

Create `~/.claude/statusline.sh` with the complete enhanced script. See the full script in the implementation section below.

**IMPORTANT**: Make sure there's a space between icons and text in the printf statements:
```bash
# CORRECT - space after icon hex code before %s
printf "${MAGENTA}$(printf '\xee\x82\xa0')  %s${RESET}" "$git_display"

# WRONG - no space
printf "${MAGENTA}$(printf '\xee\x82\xa0')%s${RESET}" "$git_display"
```

### Step 2: Make Script Executable

```bash
chmod +x ~/.claude/statusline.sh
```

### Step 3: Update Claude Settings

Update `~/.claude/settings.json`:
```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh"
  }
}
```

### Step 4: Test Status Line

**IMPORTANT**: Always test with real Claude input data to validate the script works correctly.

First, add this debug line to capture real data (temporary):
```bash
# Add this at the top of ~/.claude/statusline.sh after "input=$(cat)"
echo "$input" > /tmp/statusline_debug.json
```

Then run Claude Code and let it update the statusline. After that, test with the real data:
```bash
cat /tmp/statusline_debug.json | ~/.claude/statusline.sh
```

**Remove the debug line after testing.**

For quick testing without real data:
```bash
echo '{}' | ~/.claude/statusline.sh
```

Expected output should show colors and icons properly, without raw ANSI codes like `\033[0;32m` visible in the text. If you see escape codes, the printf formatting needs fixing.

Good output example:
```
  ~/path  |   main ✓  |  Sonnet 4.5  |  ◔ 8%  |  ↓7 ↑5 ⚡37463  |  $0.56  |   5m  |  5h:40% 7d:6%⚠  |   3.14  |   95%
```

Note: Icons shown as  are Nerd Font glyphs that render as proper icons in terminals with Nerd Fonts installed.

Bad output example (shows raw codes):
```
~/path  |  main \033[0;32m✓\033[0m  |  ...
```

## Display Elements

### Core Elements
- **  path** - Folder icon + relative path (cyan)
- **  branch ✓/✗ ~X ↑Y ↓Z ⋮N** - Git status (magenta/green/red/yellow/gray)
  - Branch name
  - ✓ (clean) or ✗ (dirty)
  - ~X modified files
  - ↑Y commits ahead
  - ↓Z commits behind
  - ⋮N stash count
- **model** - Model name (blue)
- **◔ XX%** - Context window usage (yellow)
- **↓X ↑Y** - Input/output tokens (green)
- **⚡X** - Cache read tokens (gray) - only shown when >0
- **$X.XX** - Session cost (red)
- **  Xm** - Duration (gray)

### Usage Tracking
- **  5h:XX% 7d:YY%⚠** - API usage limits
  - Green (<50%), Yellow (50-80%), Red (>80%)
  - ⚠ appears next to higher percentage
  - Updates every 60 seconds from Anthropic API

### Project Context (conditional)
- ** X.Y** - Python version (blue) - shown in Python projects - Nerd Font Python icon
- ** XX** - Node version (green) - shown in Node projects - Nerd Font Node icon
- ** X** - Docker containers (cyan) - shown when containers running - Nerd Font Docker icon

### System Info
- ** XX%** - Battery (green/yellow/red based on level) - Nerd Font battery icons (different for full/medium/low)
- ** Name** - WiFi network (gray, truncated if long) - Nerd Font WiFi icon

## Requirements

### Essential
- **Nerd Font installed** - For icons (JetBrains Mono, Hack, FiraCode, etc.)
- **jq installed** - For JSON parsing (`brew install jq`)
- **Git installed** - For git features
- **curl** - For API usage fetching

### Platform-Specific (macOS)
- **security** - Keychain access for API token
- **pmset** - Battery info
- **networksetup** - WiFi info

### Optional
- **Python** - For Python version display
- **Node** - For Node version display
- **Docker** - For container count

## Implementation

The complete script includes:

1. **Git Status Detection** - Uses `git status --porcelain` for dirty/clean check
2. **Ahead/Behind Tracking** - Compares with upstream branch
3. **API Usage Caching** - Fetches from `https://api.anthropic.com/api/oauth/usage`
4. **Token Extraction** - Reads from macOS Keychain via `security find-generic-password`
5. **Color Coding** - Dynamic colors based on usage thresholds
6. **Conditional Display** - Only shows relevant project/system info
7. **Cache Token Display** - Highlights cheaper cache reads separately
8. **Nerd Font Icons** - All icons use hex escape codes (NO EMOJIS)

### Nerd Font Icon Hex Codes Used

**DO NOT use emoji characters - only these hex codes:**

- **Python**: `\xee\x9c\xbc` () - Nerd Font Python logo
- **Node**: `\xee\x9c\x98` () - Nerd Font Node logo
- **Docker**: `\xef\x8c\x88` () - Nerd Font Docker logo
- **Battery Full**: `\xef\x89\x80` () - Battery icon ≥80%
- **Battery Medium**: `\xef\x89\x82` () - Battery icon 30-80%
- **Battery Low**: `\xef\x89\x84` () - Battery icon <30%
- **WiFi**: `\xef\x87\xab` () - WiFi icon

Pattern for printf: `$(printf '\xXX\xXX\xXX')`

## Customization

Edit `~/.claude/statusline.sh` to:

### Change Colors
```bash
# Modify threshold colors
if [ "$five_hr_int" -ge 80 ]; then
  five_hr_color="${RED}${BOLD}"  # Change to your preference
```

### Adjust Thresholds
```bash
# Change when colors kick in (default: 50%, 80%)
elif [ "$five_hr_int" -ge 50 ]; then  # Change 50 to your threshold
```

### Hide/Show Elements
```bash
# Comment out to hide battery
# [ -n "$battery_info" ] && printf "  |  %s" "$battery_info"
```

### Change Cache Update Frequency
```bash
cache_max_age=60  # Change to 120 for 2-minute cache
```

### Modify Icons
```bash
# Use different Nerd Font icons (NEVER use emojis!)
# Find hex codes at: https://www.nerdfonts.com/cheat-sheet
# Convert Unicode to hex: U+E73C → \xee\x9c\xbc

# Example: Change folder icon
printf '\xef\x81\xbc'  # Current folder icon
# Replace with different icon by changing hex code
```

**IMPORTANT**: Always use hex escape codes `\xXX\xXX\xXX` format, NEVER emoji characters.

## Troubleshooting

**Icons not showing**: Install a Nerd Font and configure your terminal to use it

**No git branch**: Script shows "no-git" in gray when not in a git repository

**Usage shows "--"**: API call failed. Check:
- Keychain has "Claude Code-credentials"
- Internet connection active
- API endpoint accessible

**No battery/WiFi info**: Commands are macOS-specific. On Linux, replace:
- `pmset` → `acpi` or `/sys/class/power_supply/`
- `networksetup` → `iwgetid` or `nmcli`

**Slow status line**: Increase `cache_max_age` to reduce API calls

**Cache tokens not showing**: They only appear when `cache_read_input_tokens > 0`

**Colors wrong in terminal**: Ensure terminal supports ANSI color codes

**Raw ANSI codes showing (e.g., `\033[0;32m`)**: The printf statements are embedding ANSI codes in variables instead of using direct printf formatting. Solution:
- Don't embed ANSI codes in variables that will be printed later
- Use direct `printf "${COLOR}text${RESET}"` instead of `var="${COLOR}text${RESET}"; printf "%s" "$var"`
- Build git status separately and add color during printf, not before

## Advanced Tips

### Performance Optimization
- Git operations cached by Git itself
- API calls cached for 60 seconds
- Docker check only runs if docker installed
- Conditional checks avoid unnecessary processing

### Security Note
The script reads your Claude Code OAuth token from macOS Keychain but only uses it to fetch usage statistics from Anthropic's API. The token is never logged or stored outside the system keychain.

### Spacing Guide
Always use double space (`  `) between icon hex codes and variable placeholders:
```bash
# Pattern: icon + 2 spaces + variable
$(printf '\xef\x81\xbc')  %s
```

This ensures consistent spacing throughout the status line display.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zeulewan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
