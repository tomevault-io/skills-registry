---
name: sukebei
description: Search and download torrents from sukebei.nyaa.si. Trigger: "下片[名称]" to search. Uses qBittorrent skill for downloads with smart file filtering. Use when this capability is needed.
metadata:
  author: dest16
---

# Sukebei Search & Download

Search and download torrents from sukebei.nyaa.si (adult content BT tracker) with automatic file filtering.

## Trigger

**Start search**: Say "下片[名称]" or "download [name]"

Examples:
```
下片 MIDA-512
下片 midv-401
下片 SORA
download SORA
```

## Usage

### Step 1: Search (triggered by user)
```
User: "下片 MIDA-512"
```

### Step 2: Assistant searches
```bash
web_fetch "https://sukebei.nyaa.si/?q=MIDA-512&f=0&c=2_2"
```

### Step 3: User selects result
```
User: "下载第X个" or "download result X"
```

### Step 4: Assistant downloads
```bash
python3 scripts/sukebei_download.py "https://sukebei.nyaa.si/download/ID.torrent"
```

## Search URL Format

```
https://sukebei.nyaa.si/?q=关键词&f=0&c=2_2
```

| Parameter | Value | Description |
|-----------|-------|-------------|
| `q` | keywords | Search query (NOT `s=` which is blocked) |
| `f` | 0 | No filter |
| `c` | 2_2 | Category: Videos |

## Smart Download Workflow

The download script implements file filtering logic:

```bash
# 1. Add torrent (paused)
./qbit-api.sh add "URL" --paused

# 2. Get torrent hash from list
HASH=$(./qbit-api.sh list | python3 -c "...")

# 3. Get files and filter (logic here)
./qbit-api.sh files "$HASH" | python3 -c "
    # Filter: keep video files > 100MB
    # Call qBittorrent set-file-priority API
"

# 4. Resume download
./qbit-api.sh resume "$HASH"

# 5. Add tag
./qbit-api.sh add-tags "$HASH" "openclaw"
```

## File Filtering Rules

**Keep**: Video extensions + >100MB
```
.mp4, .mkv, .avi, .mov, .wmv, .flv, .webm, .m4v,
.mpg, .mpeg, .ts, .mts, .m2ts, .vob, .ogm, .ogv
```

**Skip**:
- Files < 100MB
- Non-video files (images, txt, nfo, srt, url, etc.)

## qBittorrent Commands Used

| Command | Purpose |
|---------|---------|
| `add <url> --paused` | Add torrent paused |
| `list` | Get torrent list |
| `files <hash>` | Get file list |
| `set-file-priority <hash> <id> <prio>` | Set file priority |
| `resume <hash>` | Resume download |
| `add-tags <hash> openclaw` | Add tag |
| `delete <hash> --files` | Delete torrent and files |

## Output Format

### Search Results
```
【结果 1】
标题: [Title]
大小: X.X GiB
做种: N | 下载: M
链接: https://sukebei.nyaa.si/view/XXXXXXX
种子: https://sukebei.nyaa.si/download/XXXXXXX.torrent
```

### Download Confirmation
```
✅ 已添加 "Title"

过滤结果:
  ✅ video.mp4 (4299 MB)
  ❌ sample.mp4 (跳过: <100MB)
  ❌ cover.jpg (跳过: 非视频)

开始下载...
  ✓ 已开始下载
  ✓ 添加标签 openclaw
```

## Example Session

```bash
# User triggers search
下片 MIDA-512

# Assistant searches
Assistant: web_fetch "https://sukebei.nyaa.si/?q=MIDA-512&f=0&c=2_2"

# User selects
下载第2个

# Assistant downloads
python3 scripts/sukebei_download.py "https://sukebei.nyaa.si/download/4509702.torrent"

# Output:
# 1. 添加种子: https://sukebei.nyaa.si/download/4509702.torrent
#    ✓ 已添加: MIDA-512
#
# 2. 等待文件列表加载 (5秒)...
#
# 3. 过滤文件 (>100MB 视频)...
#    ✅ MIDA-512.H265.mp4 (4299 MB)
#    ❌ manko.fun.mp4 - 跳过: <100MB
#    ❌ manko.fun.url - 跳过: 非视频
#
# 4. 开始下载...
#    ✓ 已开始下载
#
# 5. 添加标签 'openclaw'...
```

## Notes

- **Search**: Use `q=` parameter (NOT `s=` - blocked by anti-bot)
- **File filtering**: Implemented in download script
- **qBittorrent skill**: Only provides raw API access
- **No standalone search script**: Uses `web_fetch` directly

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dest16) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
