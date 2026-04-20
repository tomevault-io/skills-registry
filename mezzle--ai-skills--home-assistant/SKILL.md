---
name: home-assistant
description: Control and monitor your smart home with Home Assistant. Get device states, call services to control lights/switches/climate, fire events, and query history. Use when the user asks about smart home, Home Assistant, lights, switches, sensors, thermostats, automation, or home control. Use when this capability is needed.
metadata:
  author: mezzle
---

# Home Assistant Smart Home Control

Control and monitor your smart home through the Home Assistant REST API. Manage lights, switches, sensors, climate controls, and automations.

## Prerequisites

Before using this Skill, you must:

1. Install Node.js dependencies:
```bash
cd scripts && npm install && npm run build
```

2. Have a running Home Assistant instance accessible via HTTP/HTTPS

3. Generate a Long-Lived Access Token (see [references/SETUP.md](references/SETUP.md))

4. Set environment variables:
```bash
export HOME_ASSISTANT_URL="http://your-home-assistant:8123"
export HOME_ASSISTANT_TOKEN="your-long-lived-access-token"
```

## Instructions

### Getting Entity States

To retrieve current state of all entities or a specific entity:

```bash
node scripts/dist/get-states.js
```

**Options:**
- `--entity <id>` or `-e` - Get state of specific entity

**Examples:**

Get all entity states:
```bash
node scripts/dist/get-states.js
```

Get specific entity state:
```bash
node scripts/dist/get-states.js --entity light.living_room
node scripts/dist/get-states.js -e sensor.temperature
```

Filter entities by type:
```bash
node scripts/dist/get-states.js | jq '.[] | select(.entity_id | startswith("light."))'
```

**Entity State includes:**
- `entity_id` - Unique identifier (e.g., "light.living_room")
- `state` - Current state ("on", "off", "23.5", etc.)
- `attributes` - Entity-specific attributes (brightness, temperature, etc.)
- `last_changed` - When state last changed
- `last_updated` - When entity was last updated

### Calling Services

To control devices and trigger automations:

```bash
node scripts/dist/call-service.js --domain <domain> --service <service> [options]
```

**Required:**
- `--domain <domain>` or `-d` - Service domain (light, switch, climate, etc.)
- `--service <name>` or `-s` - Service name (turn_on, turn_off, toggle, etc.)

**Optional:**
- `--entity <id>` or `-e` - Target entity ID
- `--data <json>` - Additional service data as JSON

**Common Domains and Services:**

| Domain | Services |
|--------|----------|
| light | turn_on, turn_off, toggle |
| switch | turn_on, turn_off, toggle |
| scene | turn_on |
| script | turn_on, turn_off, toggle |
| cover | open_cover, close_cover, stop_cover |
| climate | set_temperature, set_hvac_mode |
| media_player | play_media, pause, stop, volume_set |
| automation | trigger, turn_on, turn_off |

**Examples:**

Turn on a light:
```bash
node scripts/dist/call-service.js -d light -s turn_on -e light.living_room
```

Turn off a switch:
```bash
node scripts/dist/call-service.js -d switch -s turn_off -e switch.bedroom_fan
```

Activate a scene:
```bash
node scripts/dist/call-service.js -d scene -s turn_on -e scene.movie_time
```

Turn on light with brightness:
```bash
node scripts/dist/call-service.js -d light -s turn_on -e light.bedroom --data '{"brightness": 128}'
```

Set thermostat temperature:
```bash
node scripts/dist/call-service.js -d climate -s set_temperature -e climate.thermostat --data '{"temperature": 72}'
```

Toggle multiple lights:
```bash
node scripts/dist/call-service.js -d light -s toggle --data '{"entity_id": ["light.kitchen", "light.dining"]}'
```

Set light color:
```bash
node scripts/dist/call-service.js -d light -s turn_on -e light.rgb_strip --data '{"rgb_color": [255, 0, 0]}'
```

### Getting Configuration

To retrieve Home Assistant system configuration:

```bash
node scripts/dist/get-config.js
```

**Options:**
- `--components` or `-c` - List only loaded components/integrations

**Configuration includes:**
- `location_name` - Instance name
- `version` - Home Assistant version
- `time_zone` - Configured timezone
- `latitude`/`longitude` - Location coordinates
- `unit_system` - Measurement units
- `components` - Loaded integrations

**Examples:**

Get full configuration:
```bash
node scripts/dist/get-config.js
```

Get only components:
```bash
node scripts/dist/get-config.js --components
```

Extract specific info:
```bash
node scripts/dist/get-config.js | jq '{name: .location_name, version: .version}'
```

### Firing Events

To fire custom events for automations:

```bash
node scripts/dist/fire-event.js --event <event_type> [options]
```

**Required:**
- `--event <type>` or `-e` - Event type to fire

**Optional:**
- `--data <json>` or `-d` - Event data as JSON

**Examples:**

Fire simple event:
```bash
node scripts/dist/fire-event.js --event my_custom_event
```

Fire event with data:
```bash
node scripts/dist/fire-event.js -e motion_detected -d '{"location": "backyard", "camera": "cam_01"}'
```

Trigger button automation:
```bash
node scripts/dist/fire-event.js -e button_pressed -d '{"button_id": "front_door"}'
```

### Querying History

To retrieve historical state changes:

```bash
node scripts/dist/get-history.js [options]
```

**Options:**
- `--entity <id>` or `-e` - Filter by entity ID (recommended)
- `--start <time>` or `-s` - Start time in ISO format (default: 24 hours ago)
- `--end <time>` - End time in ISO format (default: now)

**Time Formats:**
- ISO 8601: `2024-01-15T10:00:00Z`
- With timezone: `2024-01-15T10:00:00-05:00`
- Date only: `2024-01-15` (starts at midnight)

**Examples:**

Get history for entity (last 24 hours):
```bash
node scripts/dist/get-history.js --entity sensor.temperature
```

Get history from specific time:
```bash
node scripts/dist/get-history.js -e light.living_room -s "2024-01-15T00:00:00Z"
```

Get history for time range:
```bash
node scripts/dist/get-history.js -e switch.heater -s "2024-01-14T00:00:00Z" --end "2024-01-15T00:00:00Z"
```

## Common Workflows

### Check and Control Lights

```bash
# List all lights
node scripts/dist/get-states.js | jq '.[] | select(.entity_id | startswith("light."))'

# Check specific light
node scripts/dist/get-states.js -e light.living_room | jq '{state, brightness: .attributes.brightness}'

# Turn on with specific brightness
node scripts/dist/call-service.js -d light -s turn_on -e light.living_room --data '{"brightness": 200}'
```

### Monitor Temperature Sensors

```bash
# Get all temperature sensors
node scripts/dist/get-states.js | jq '.[] | select(.entity_id | contains("temperature"))'

# Get temperature history
node scripts/dist/get-history.js -e sensor.living_room_temperature -s "2024-01-15T00:00:00Z"
```

### Control Thermostat

```bash
# Check current state
node scripts/dist/get-states.js -e climate.thermostat

# Set temperature
node scripts/dist/call-service.js -d climate -s set_temperature -e climate.thermostat --data '{"temperature": 72}'

# Set HVAC mode
node scripts/dist/call-service.js -d climate -s set_hvac_mode -e climate.thermostat --data '{"hvac_mode": "heat"}'
```

### Trigger Automation via Event

```bash
# Fire event that automation listens for
node scripts/dist/fire-event.js -e presence_detected -d '{"person": "john", "location": "home"}'
```

## Entity ID Patterns

Home Assistant uses consistent entity ID patterns:

| Domain | Pattern | Example |
|--------|---------|---------|
| Lights | light.{name} | light.living_room |
| Switches | switch.{name} | switch.bedroom_fan |
| Sensors | sensor.{name} | sensor.temperature |
| Binary Sensors | binary_sensor.{name} | binary_sensor.motion |
| Climate | climate.{name} | climate.thermostat |
| Covers | cover.{name} | cover.garage_door |
| Media Players | media_player.{name} | media_player.tv |
| Scenes | scene.{name} | scene.movie_time |
| Scripts | script.{name} | script.good_night |
| Automations | automation.{name} | automation.lights_at_sunset |

## Troubleshooting

**"Missing required environment variable" error:**
- Set both `HOME_ASSISTANT_URL` and `HOME_ASSISTANT_TOKEN`
- Verify: `echo $HOME_ASSISTANT_URL`
- Reload shell after setting variables

**"API request failed: 401 Unauthorized":**
- Token is invalid or expired
- Generate new Long-Lived Access Token from your profile
- Verify token doesn't have extra whitespace

**"API request failed: 404 Not Found":**
- Entity ID is incorrect
- Use `get-states.js` to list valid entity IDs
- Check entity exists in Home Assistant

**"ECONNREFUSED" or connection errors:**
- Home Assistant is not accessible at the configured URL
- Check URL is correct (include port 8123 if needed)
- Verify Home Assistant is running
- Check network/firewall settings

**Service call has no effect:**
- Entity may not support the service
- Check entity attributes for supported features
- Verify entity_id is correct

## Security Notes

- Long-Lived Access Tokens have full access to your Home Assistant instance
- Store tokens securely as environment variables
- Never commit tokens to version control
- Regenerate tokens periodically
- Use HTTPS in production for encrypted communication
- Consider using a read-only token if only monitoring is needed

## Additional Resources

For detailed setup instructions, see [references/SETUP.md](references/SETUP.md).

For complete API documentation, see [references/API.md](references/API.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mezzle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
