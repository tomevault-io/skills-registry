---
name: 12306-ticket-assistant
description: Intelligent 12306 train ticket assistant with natural language understanding and browser automation. Automatically applies user preferences (high-speed trains, 2nd class, Friday evening/Saturday morning departures), calculates weekend dates, recommends destinations, and queries tickets using browser automation. Use when user asks about weekend trips, travel recommendations, or ticket queries like 'where to go this weekend' or 'visit Zhangzhou this weekend'. Use when this capability is needed.
metadata:
  author: jarrett-au
---

# 12306 Intelligent Ticket Assistant

Smart train ticket querying with natural language understanding and automatic preference application.

## Core Capabilities

1. **Natural Language Understanding** - Parse casual requests like "这周末去哪玩" or "我想去漳州"
2. **Automatic Date Calculation** - Finds next weekend dates automatically
3. **User Preference Defaults** - Applies saved preferences without manual input
4. **Destination Recommendations** - Suggests suitable weekend destinations
5. **Batch Query Optimization** - Checks multiple routes/time slots efficiently

## User Preferences (Configurable)

Default preferences stored in `references/preferences.md`:

```yaml
# Default User Preferences
origin: 深圳北 (Shenzhen North)
origin_code: IOQ

# Train preferences
train_types: [GC, D]  # 高铁/城际, 动车
seat_class: 二等座

# Departure time preferences
friday_evening: "18:00-24:00"  # Friday evening
saturday_morning: "06:00-12:00"  # Saturday morning
sunday_evening: "18:00-24:00"  # Return Sunday evening
monday_morning: "06:00-12:00"  # Return Monday morning

# Trip duration preference
max_travel_time: "3:00"  # Maximum 3 hours for weekend trip
min_travel_time: "1:00"  # Minimum 1 hour

# Preferred destinations (categorized)
destinations:
  weekend_short:  # 1-2 hours, perfect for 2-day trip
    - 广州南 (1:30)
    - 赣州西 (1:23)
    - 郴州西 (1:30)
    - 潮汕 (2:00)
    - 厦门北 (3:00)

  weekend_medium:  # 2-3 hours
    - 长沙南 (3:20)
    - 衡阳东 (2:40)
    - 南昌西 (4:00)
    - 桂林北 (3:30)

  weekend_long:  # 3-5 hours
    - 武汉 (4:30)
    - 贵阳北 (5:00)
    - 杭州东 (6:00)
```

## Intelligent Workflows

### Workflow 1: "这周末推荐去哪玩"

**User Input:** "这周末推荐下可以什么地方去玩" / "周末有什么好玩的地方"

**Automatic Processing:**
1. Calculate next weekend dates (Saturday/Sunday)
2. Load preferred destinations from preferences
3. Query tickets for Friday evening AND Saturday morning
4. Filter by: max travel time ≤ 3 hours, has available seats
5. Present options with travel time, ticket availability, brief highlights

**Example Response:**
```
📅 本周末推荐 (2月27-28日)

🚄 [推荐] 赣州西 - 1小时23分钟
   周五晚: G3142 (19:30-20:53) 二等座 ¥189 有票
   周六早: G3142 (07:11-08:34) 二等座 ¥189 有票
   ✅ 江宋古城、客家文化、美食丰富

🚄 潮汕 - 2小时
   周五晚: G6342 (18:45-20:45) 二等座 ¥165 有票
   ✅ 潮汕美食、牛肉火锅、古城游览

🚄 长沙南 - 3小时20分钟
   周六早: G1004 (07:30-10:50) 二等座 ¥314 有票
   ✅ 橘子洲、岳麓山、湘菜、夜生活丰富
```

### Workflow 2: "我想这周去X地玩"

**User Input:** "我想这周去漳州玩" / "查查周末去厦门"

**Automatic Processing:**
1. Parse destination (漳州/厦门)
2. Calculate next weekend dates
3. Query departure options:
   - Friday evening (18:00+)
   - Saturday morning (06:00-12:00)
4. Query return options:
   - Sunday evening (18:00+)
   - Monday morning (06:00-12:00)
5. Apply filters: GC/D trains, 二等座 only
6. Present complete itinerary

**Example Response:**
```
🎯 周末去漳州 (需中转厦门)

📅 去程 - 2月27日周五
   G1664 深圳北→厦门北 18:20-21:15 (2:55) 二等座 ¥258 有票
   → 厦门北→漳州 D6xxx 约30分钟

📅 返程 - 3月2日周一
   G1661 厦门北→深圳北 09:15-12:10 (2:55) 二等座 ¥258 有票

💡 总预算: ¥516往返
⏰ 建议游玩: 2整天（漳州古城、火山岛、土楼）
```

### Workflow 3: Multi-destination Comparison

**User Input:** "对比下赣州、郴州、衡阳哪个更好"

**Processing:**
1. Query all three destinations simultaneously
2. Compare: travel time, ticket availability, price
3. Highlight pros/cons of each

## Implementation Details

### ⚠️ CRITICAL: Browser Input Methods

**IMPORTANT:** 12306 input fields require special handling. Simple `fill` or `type` commands DO NOT work and will result in incorrect station selection.

**You MUST read** [browser-input-methods.md](references/browser-input-methods.md) for the correct input procedure.

**Quick Summary:**
1. Use `--headed` flag to see browser window
2. Simulate keyboard events via JavaScript (keydown → input → keyup)
3. Select dropdown options using ArrowDown + Enter keys
4. Verify selection by screenshot

**Incorrect methods that will fail:**
- ❌ `agent-browser fill e40 "郴州西"` - Does not trigger dropdown
- ❌ `agent-browser type e40 "郴州西"` - Does not trigger dropdown
- ❌ Direct value assignment via JS - Bypasses event handlers

**See** [browser-input-methods.md](references/browser-input-methods.md) **for complete working examples.**

### Date Calculation Logic

```javascript
// Find next weekend
function getNextWeekend() {
  const today = new Date();
  const dayOfWeek = today.getDay(); // 0=Sunday, 6=Saturday

  let saturday, sunday;

  if (dayOfWeek === 6) {  // Today is Saturday
    saturday = today;
    sunday = new Date(today.getTime() + 86400000);
  } else if (dayOfWeek === 0) {  // Today is Sunday
    saturday = new Date(today.getTime() + 6 * 86400000);
    sunday = new Date(today.getTime() + 7 * 86400000);
  } else {
    const daysUntilSaturday = 6 - dayOfWeek;
    saturday = new Date(today.getTime() + daysUntilSaturday * 86400000);
    sunday = new Date(today.getTime() + (daysUntilSaturday + 1) * 86400000);
  }

  return {
    saturday: formatDate(saturday),  // YYYY-MM-DD
    sunday: formatDate(sunday),
    friday: formatDate(new Date(saturday.getTime() - 86400000)),
    monday: formatDate(new Date(sunday.getTime() + 86400000))
  };
}
```

### Smart Query Execution

**IMPORTANT:** When querying 12306, always use the correct input method. See [browser-input-methods.md](references/browser-input-methods.md) for detailed instructions.

**Basic pattern for a single query:**

```bash
# 1. Open browser (must use --headed)
agent-browser --headed open https://www.12306.cn/index/index.html

# 2. Input departure station (example: 深圳)
agent-browser eval "<use-pattern-from-reference>"
sleep 1
agent-browser focus e39 && agent-browser press ArrowDown && agent-browser press Enter

# 3. Input destination station (example: 郴州西)
agent-browser eval "<use-pattern-from-reference>"
sleep 1
agent-browser focus e40 && agent-browser press ArrowDown && agent-browser press Enter

# 4. Input date and submit query
# ... (see reference for complete example)
```

**For parallel queries:** Use separate browser sessions to avoid state conflicts:

```bash
# Terminal 1: Query Ganzhou
agent-browser --session query-ganzhou --headed open "{URL_GANZHOU}"
# ... input stations using correct method
agent-browser screenshot /tmp/ganzhou.png

# Terminal 2: Query Chenzhou (simultaneous)
agent-browser --session query-chenzhou --headed open "{URL_CHENZHOU}"
# ... input stations using correct method
agent-browser screenshot /tmp/chenzhou.png
```

### Natural Language Parsing Patterns

| User Input Pattern | Intent | Parameters Extracted |
|-------------------|--------|---------------------|
| "这周末去哪玩" | Recommend destinations | Time=next weekend, origin=default |
| "周末有什么地方" | List options | Time=next weekend |
| "去{city}玩" | Query specific | Destination={city}, time=implied weekend |
| "查查{date}去{city}" | Query specific | Date={date}, dest={city} |
| "{date}晚上的票" | Time filter | Time=evening |
| "早点出发" | Time preference | Time=morning |

## Batch Query Template

```bash
#!/bin/bash
# weekend_query.sh - Automated weekend trip query

# Load preferences
source /path/to/references/preferences.sh

# Calculate dates
DATES=$(node -e "console.log(JSON.stringify(getNextWeekend()))")
FRIDAY=$(echo $DATES | jq -r '.friday')
SATURDAY=$(echo $DATES | jq -r '.saturday')
SUNDAY=$(echo $DATES | jq -r '.sunday')

# Query each destination
for DEST in "${PREFERRED_DESTINATIONS[@]}"; do
  echo "Querying $DEST for $SATURDAY..."

  # Friday evening departure
  agent-browser --session weekend \
    open "https://...fs=${ORIGIN},IOQ&ts=${DEST},CODE&date=${FRIDAY}..."

  agent-browser eval 'set_time_filter("18:00-24:00")'
  agent-browser screenshot "/tmp/${DEST}_friday_evening.png"

  # Saturday morning departure
  agent-browser --session weekend \
    open "https://...&date=${SATURDAY}..."
  agent-browser eval 'set_time_filter("06:00-12:00")'
  agent-browser screenshot "/tmp/${DEST}_saturday_morning.png"
done
```

## Destination Recommendation Logic

### Scoring System

Each destination scored on:
- **Travel Time** (40%): Shorter is better (1-3hr ideal)
- **Ticket Availability** (30%): More options = higher score
- **Price** (20%): Budget-friendly preferred
- **Attraction Variety** (10%): Food, culture, nature

### Recommendation Tiers

**Tier 1: Perfect Weekend Getaway (1-2hr, ¥100-250)**
- 赣州西, 广州南, 郴州西
- ✅ Short travel, more time to explore
- ✅ Budget-friendly
- ✅ Good for 2-day trip

**Tier 2: Extended Weekend (2-3.5hr, ¥250-400)**
- 厦门北, 长沙南, 衡阳东
- ✅ Worth the travel time
- ✅ Rich attractions
- ⚠️ Need 2.5-3 days minimum

**Tier 3: Long Weekend (3.5-5hr, ¥400+)**
- 武汉, 贵阳北, 杭州
- ✅ Major cities, lots to see
- ⚠️ Best for 3+ day holiday

## Response Format

### Standard Recommendation Response

```markdown
🎯 周末旅行推荐

📅 时间: 本周末 {DATE_RANGE}
🚄 出发: {ORIGIN} (深圳北)

【推荐一】{DESTINATION_1} ⭐⭐⭐⭐⭐
⏰ 车程: {DURATION}
💰 参考价: {PRICE} 往返
🎫 票况: {AVAILABILITY}

🚞 去程方案:
  周五晚: {TRAIN_NO} {TIME} 二等座 ¥{PRICE}
  周六早: {TRAIN_NO} {TIME} 二等座 ¥{PRICE}

🚞 返程方案:
  周日晚: {TRAIN_NO} {TIME} 二等座 ¥{PRICE}
  周一早: {TRAIN_NO} {TIME} 二等座 ¥{PRICE}

✨ 推荐理由:
  - {HIGHLIGHT_1}
  - {HIGHLIGHT_2}
  - {HIGHLIGHT_3}

📌 2日行程建议:
  Day 1: {ITINERARY_DAY1}
  Day 2: {ITINERARY_DAY2}
```

## Advanced Features

### Feature 1: Historical Price Tracking

Track price trends for preferred routes (if accessible via API).

### Feature 2: Seat Availability Alerts

Monitor specific routes and alert when seats become available.

### Feature 3: Group Trip Planning

Query multiple tickets simultaneously for group travel.

### Feature 4: Alternative Route Suggestions

If direct trains unavailable, suggest routes with transfers.

## Configuration Files

### preferences.md (Create this file)

```yaml
# User Preferences - Customize these
user_profile:
  name: "Weekend Traveler"
  home_station: "深圳北"
  home_code: "IOQ"

travel_preferences:
  train_types: ["GC", "D"]
  seat_preference: "二等座"
  max_budget_per_trip: 500
  max_travel_time: "3:30"

weekend_pattern:
  departure_friday_evening: true
  departure_saturday_morning: true
  return_sunday_evening: true
  return_monday_morning: false

favorite_destinations:
  - name: "赣州西"
    code: "GZQ"
    rating: 5
    visit_count: 3
  - name: "长沙南"
    code: "CSQ"
    rating: 4
    visit_count: 2
```

## Troubleshooting

**Issue:** Input fields don't trigger dropdown menu
**Solution:** You are NOT using the correct input method. See [browser-input-methods.md](references/browser-input-methods.md). Do NOT use `fill` or `type` commands - they don't work on 12306.

**Issue:** Query returns wrong stations or no results
**Solution:** The station code field (hidden) is not populated. Must select from dropdown using ArrowDown + Enter keys after typing.

**Issue:** Natural language misunderstood
**Solution:** Be more specific with dates and destinations

**Issue:** Weekend date calculation wrong
**Solution:** Verify system date and timezone settings

**Issue:** Recommendations don't match preferences
**Solution:** Update `references/preferences.md` with correct values

**Issue:** Query too slow
**Solution:** Use parallel queries with separate sessions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jarrett-au) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
