---
name: command-design
description: Design and implement intuitive, user-friendly bot command interfaces with comprehensive help systems Use when this capability is needed.
metadata:
  author: forever19735
---

## When to use this skill

Use this skill when you need to:
- Design new bot commands and syntax
- Implement command parsing and validation
- Create help and documentation systems
- Handle command errors gracefully
- Design multi-parameter command formats
- Implement command aliases and shortcuts
- Build hierarchical help menus

## How to use it

### Command Design Principles

1. **Prefix Convention**: Use `@` to distinguish bot commands
2. **Simple Syntax**: Keep commands short and memorable
3. **Clear Feedback**: Always respond with success/error messages
4. **Help System**: Provide comprehensive, contextual help
5. **Validation**: Validate inputs before processing

### Command Categories

#### 1. Query Commands (Read-Only)
Commands that display information without modifying state:

```python
@schedule      # View broadcast schedule
@members       # View member rotation table
@help          # Show help menu
@help schedule # Show specific help topic
```

**Implementation Pattern:**
```python
if command == 'schedule':
    schedule_info = get_group_schedule_for_display(group_id)
    reply_text = format_schedule_display(schedule_info)

elif command == 'members':
    reply_text = get_member_schedule_summary(group_id)

elif command == 'help':
    topic = args[0] if args else None
    reply_text = get_help_message(topic)
```

#### 2. Configuration Commands (Settings)
Commands that modify group settings:

```python
@time 18:00               # Set broadcast time
@day mon,wed,fri          # Set broadcast days
@cron mon,thu 20 15       # Set days and time together
```

**Implementation Pattern:**
```python
elif command == 'time':
    if not args:
        reply_text = "❌ 請指定時間，例如：@time 18:00"
    else:
        try:
            time_str = args[0]
            hour, minute = parse_time(time_str)
            
            # Update schedule
            if group_id not in group_schedules:
                group_schedules[group_id] = {}
            group_schedules[group_id]['hour'] = hour
            group_schedules[group_id]['minute'] = minute
            save_group_schedules(group_schedules)
            
            reply_text = f"✅ 推播時間已設為 {hour:02d}:{minute:02d}"
        except ValueError as e:
            reply_text = f"❌ 時間格式錯誤：{e}"

def parse_time(time_str):
    """Parse time string HH:MM"""
    parts = time_str.split(':')
    if len(parts) != 2:
        raise ValueError("請使用 HH:MM 格式，例如 18:00")
    
    hour = int(parts[0])
    minute = int(parts[1])
    
    if not (0 <= hour <= 23):
        raise ValueError("小時必須在 0-23 之間")
    if not (0 <= minute <= 59):
        raise ValueError("分鐘必須在 0-59 之間")
    
    return hour, minute
```

#### 3. Data Management Commands
Commands that modify member data:

```python
@week 1 Alice,Bob              # Set week 1 members
@addmember 1 Charlie           # Add member to week 1
@removemember 1 Alice          # Remove member from week 1
@clearweek 1                   # Clear week 1
@reset                         # Reset all data (dangerous!)
```

**Implementation Pattern:**
```python
elif command == 'week':
    if len(args) < 2:
        reply_text = "❌ 格式：@week 週數 成員1,成員2\n例如：@week 1 小明,小華"
    else:
        try:
            week_num = int(args[0])
            members_str = args[1]
            members = [m.strip() for m in members_str.split(',')]
            
            result = update_member_schedule(week_num, members, group_id)
            
            if result['success']:
                reply_text = f"✅ {result['message']}"
            else:
                reply_text = f"❌ {result['message']}"
        except ValueError:
            reply_text = "❌ 週數必須是數字"
```

### Help System Design

#### 1. Main Help Menu
```python
def get_help_message(topic=None):
    """
    Get help message for specific topic or general help
    
    Args:
        topic: Help topic (schedule, members, groups) or None for main menu
    """
    if topic is None:
        return """
📖 垃圾輪值提醒 Bot 使用指南

🔍 查詢指令：
  @schedule - 查看本群組推播排程
  @members - 查看本群組完整輪值表

⚙️ 排程設定：
  @time 18:30 - 設定本群組推播時間
  @day mon,thu - 設定本群組推播星期
  @cron tue,fri 20 15 - 同時設定星期和時間

👥 成員管理：
  @week 1 Alice,Bob - 設定第1週成員
  @addmember 1 Charlie - 添加成員到第1週
  @removemember 1 Alice - 從第1週移除成員

💡 詳細說明：
  @help schedule - 排程管理指令說明
  @help members - 成員管理指令說明
  @help groups - 群組管理指令說明
"""
    
    elif topic == 'schedule':
        return """
⚙️ 排程管理指令詳細說明

📅 查看排程：
  @schedule
  顯示本群組的推播時間、星期和下次執行時間

⏰ 設定推播時間：
  @time 18:00
  設定每天推播的時間（24小時制）
  
📆 設定推播星期：
  @day mon,wed,fri
  設定一週中哪幾天要推播
  可用的星期：mon, tue, wed, thu, fri, sat, sun

🔧 一次設定時間和星期：
  @cron mon,thu 20 15
  等同於：@day mon,thu + @time 20:15
  
💡 範例：
  @time 09:00        → 設定每天 09:00 推播
  @day mon,wed,fri   → 設定週一三五推播
  @cron tue,thu 17 30 → 設定週二四 17:30 推播
"""
    
    elif topic == 'members':
        return """
👥 成員管理指令詳細說明

📋 查看成員：
  @members
  顯示所有週次的成員安排和目前是第幾週

➕ 設定週次成員：
  @week 1 小明,小華
  設定第1週由小明和小華負責
  如果該週已有成員，會被新成員取代

✏️ 添加成員到週次：
  @addmember 2 小強
  將小強加入第2週（不會覆蓋原有成員）

➖ 移除週次成員：
  @removemember 1 小明
  將小明從第1週移除

🗑️ 清空週次：
  @clearweek 2
  清空第2週的所有成員

💡 範例：
  @week 1 Alice,Bob     → 第1週：Alice, Bob
  @week 2 Charlie       → 第2週：Charlie
  @addmember 1 David    → 第1週：Alice, Bob, David
  @removemember 1 Bob   → 第1週：Alice, David
"""
    
    elif topic == 'groups':
        return """
🏢 多群組功能說明

本 Bot 支援同時管理多個群組，每個群組都有獨立的：
  • 成員輪值表
  • 推播時間設定
  • 推播星期設定

🤖 自動群組管理：
  • Bot 加入群組時會自動記錄
  • Bot 離開群組時會自動清理資料
  • 每個群組的排程完全獨立運作

📊 群組資料隔離：
  • A群組的成員設定不會影響B群組
  • A群組設定週一推播，B群組可設定週二推播
  • 所有指令都只影響當前群組

💡 最佳實踐：
  1. 在每個群組中分別設定推播時間
  2. 使用 @schedule 確認設定正確
  3. 使用 @members 查看當前群組輪值表
"""
    
    else:
        return f"❌ 未知的說明主題：{topic}\n\n使用 @help 查看可用主題"
```

#### 2. Contextual Help
Provide help when users make mistakes:

```python
# Missing arguments
if len(args) < 2:
    reply_text = (
        "❌ 參數不足\n\n"
        "正確格式：@week 週數 成員1,成員2\n"
        "範例：@week 1 小明,小華\n\n"
        "使用 @help members 查看詳細說明"
    )

# Invalid format
except ValueError:
    reply_text = (
        "❌ 格式錯誤\n\n"
        "時間格式：HH:MM (24小時制)\n"
        "範例：@time 18:00\n\n"
        "使用 @help schedule 查看詳細說明"
    )
```

### Input Validation

#### 1. Number Validation
```python
def validate_week_number(week_str):
    """Validate week number input"""
    try:
        week_num = int(week_str)
        if week_num < 1:
            raise ValueError("週數必須大於 0")
        return week_num
    except ValueError as e:
        raise ValueError(f"週數必須是正整數：{e}")
```

#### 2. Time Validation
```python
def validate_time(time_str):
    """Validate time string HH:MM"""
    if ':' not in time_str:
        raise ValueError("請使用 HH:MM 格式")
    
    parts = time_str.split(':')
    if len(parts) != 2:
        raise ValueError("請使用 HH:MM 格式")
    
    hour, minute = int(parts[0]), int(parts[1])
    
    if not (0 <= hour <= 23):
        raise ValueError("小時必須在 0-23 之間")
    if not (0 <= minute <= 59):
        raise ValueError("分鐘必須在 0-59 之間")
    
    return hour, minute
```

#### 3. Day Validation
```python
def validate_days(days_str):
    """Validate days string"""
    valid_days = {'mon', 'tue', 'wed', 'thu', 'fri', 'sat', 'sun'}
    days = [d.strip().lower() for d in days_str.split(',')]
    
    invalid = [d for d in days if d not in valid_days]
    if invalid:
        raise ValueError(
            f"無效的星期：{', '.join(invalid)}\n"
            f"可用的星期：{', '.join(sorted(valid_days))}"
        )
    
    return days
```

### Best Practices

1. **Consistent Prefix**
   - Always use `@` for commands
   - Makes it clear what's a command vs normal chat

2. **Short Memorable Names**
   - `@schedule` not `@show_schedule_information`
   - `@time` not `@set_broadcast_time`

3. **Progressive Disclosure**
   - Main help shows overview
   - Topic help shows details
   - Error messages show examples

4. **Emoji Usage**
   - ✅ for success
   - ❌ for errors
   - 📅, 🗑️, 👥 for visual categorization
   - Helps scanning/readability

5. **Multi-Language Support (Optional)**
   ```python
   LANG = os.getenv('BOT_LANGUAGE', 'zh-TW')
   
   MESSAGES = {
       'zh-TW': {
           'help_title': '📖 使用指南',
           'success': '✅ 設定成功',
           # ...
       },
       'en-US': {
           'help_title': '📖 Help Guide',
           'success': '✅ Success',
           # ...
       }
   }
   ```

### Testing Commands

```python
# Test command parsing
test_inputs = [
    ("@time 18:00", "time", ["18:00"]),
    ("@week 1 Alice,Bob", "week", ["1", "Alice,Bob"]),
    ("@help schedule", "help", ["schedule"]),
]

for input_text, expected_cmd, expected_args in test_inputs:
    parts = input_text[1:].split()
    command = parts[0].lower()
    args = parts[1:] if len(parts) > 1 else []
    
    assert command == expected_cmd
    assert args == expected_args
```

### Reference Links

- See `main.py` for full command implementations
- See `README.md` for user-facing command documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/forever19735) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
