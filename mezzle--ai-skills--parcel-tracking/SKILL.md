---
name: parcel-tracking
description: Track package deliveries using the Parcel app API. View active and recent deliveries, add new tracking numbers, get delivery status updates and events. Use when the user asks about packages, parcels, deliveries, tracking numbers, shipments, or delivery status. Use when this capability is needed.
metadata:
  author: mezzle
---

# Parcel Tracking

Track package deliveries using the Parcel app API. View delivery status, tracking events, and manage tracking numbers.

## Prerequisites

Before using this Skill, you must:

1. Install Node.js dependencies:
```bash
cd scripts && npm install && npm run build
```

2. Obtain a Parcel app API key from [web.parcelapp.net](https://web.parcelapp.net) (requires premium subscription)

3. Set the API key environment variable:
```bash
export PARCEL_API_KEY="your-api-key-here"
```

## Instructions

### Viewing Deliveries

To retrieve delivery information:

```bash
node scripts/dist/view-deliveries.js --filter recent
```

**Filter modes:**
- `active` - Only show deliveries currently in transit
- `recent` - Show recent deliveries including completed ones (default)

**Examples:**

Get active deliveries:
```bash
node scripts/dist/view-deliveries.js --filter active
```

Get all recent deliveries:
```bash
node scripts/dist/view-deliveries.js
```

### Delivery Data Structure

Each delivery object includes:
- `tracking_number` - Package tracking identifier
- `carrier_code` - Carrier identifier (e.g., "ups", "fedex", "usps")
- `description` - User-provided delivery description
- `status_code` - Numeric status indicator (0-8)
- `events` - Array of tracking events with timestamps and locations
- `date_expected` - Expected delivery date/time (if available)
- `date_expected_end` - Delivery window end time (if available)
- `extra_information` - Additional carrier-specific data

**Status codes:**
- 0: Unknown
- 1: Pre-transit (label created)
- 2: In transit
- 3: Out for delivery
- 4: Delivered
- 5: Available for pickup
- 6: Exception/Issue
- 7: Expired
- 8: Pending

**Event object structure:**
- `event` - Event description
- `date` - Event timestamp
- `location` - Event location (optional)
- `additional` - Extra information (optional)

### Adding a Delivery

To add a new tracking number:

```bash
node scripts/dist/add-delivery.js --tracking <number> --carrier <code> --description <text>
```

**Required parameters:**
- `--tracking` or `-t` - Tracking number
- `--carrier` or `-c` - Carrier code (see "Getting Carrier Codes" below)
- `--description` or `-d` - Delivery description

**Optional parameters:**
- `--language` or `-l` - Two-letter language code (default: "en")
- `--push` or `-p` - Send push notification when delivery is added

**Examples:**

Add UPS delivery:
```bash
node scripts/dist/add-delivery.js -t 1Z999AA10123456784 -c ups -d "Office supplies"
```

Add placeholder delivery:
```bash
node scripts/dist/add-delivery.js --tracking 12345 --carrier pholder --description "Unknown carrier" --push
```

Add delivery with language preference:
```bash
node scripts/dist/add-delivery.js -t ABC123 -c dhl -d "Documents" --language de
```

### Getting Carrier Codes

To retrieve the list of supported carriers:

```bash
node scripts/dist/get-carriers.js
```

This returns a JSON array with carrier codes and names. Common carriers include:
- `ups` - UPS
- `usps` - USPS
- `fedex` - FedEx
- `dhl` - DHL
- `amazon` - Amazon Logistics
- `pholder` - Placeholder (for unknown carriers)

The carrier list is updated daily and does not require authentication.

**Filter carriers:**
```bash
node scripts/dist/get-carriers.js | jq '.[] | select(.code=="ups")'
```

## Rate Limits

**View Deliveries:**
- 20 requests per hour
- Responses are cached, not live updates

**Add Delivery:**
- 20 requests per day (including failed attempts)
- Failed attempts count toward the limit

**Get Carriers:**
- No authentication required
- No documented rate limit

## Best Practices

1. **Check active deliveries first**: Use `--filter active` to see only in-transit packages
2. **Verify carrier codes**: Run `get-carriers.js` before adding deliveries to ensure correct carrier code
3. **Use descriptive names**: Provide clear descriptions when adding deliveries for easier identification
4. **Handle rate limits**: Cache results when possible; the API responses are already cached by the service
5. **Placeholder deliveries**: Use carrier code `pholder` when the carrier is unknown

## Common Workflows

### Check for new delivery updates

```bash
# Get all active deliveries
node scripts/dist/view-deliveries.js --filter active

# Check specific status (filter in your code or with jq)
node scripts/dist/view-deliveries.js --filter active | jq '.[] | select(.status_code==3)'
```

### Add and track a new package

```bash
# Get carrier code
node scripts/dist/get-carriers.js | jq '.[] | select(.name | contains("UPS"))'

# Add delivery
node scripts/dist/add-delivery.js -t 1Z999AA10123456784 -c ups -d "Holiday gift" --push

# View to confirm
node scripts/dist/view-deliveries.js --filter active
```

### Monitor delivery status

```bash
# Get delivery with expected date
node scripts/dist/view-deliveries.js --filter active | jq '.[] | {description, date_expected, status_code}'
```

## Troubleshooting

**"Missing required environment variable: PARCEL_API_KEY"**
- Set the API key: `export PARCEL_API_KEY="your-key"`
- Verify it's set: `echo $PARCEL_API_KEY`
- Ensure you've reloaded your shell after setting

**"API request failed: 401"**
- API key is invalid or expired
- Generate a new key at web.parcelapp.net
- Verify you have an active premium subscription

**"API request failed: 429"**
- Rate limit exceeded
- View deliveries: Wait an hour before retrying
- Add delivery: Wait until the next day
- The service enforces strict rate limits

**Empty deliveries array**
- No tracked deliveries in your account
- Add a delivery first using `add-delivery.js`
- Check filter mode (active vs recent)

**"No data available" after adding delivery**
- Normal behavior for newly added deliveries
- Server updates tracking data periodically
- Check again later for tracking events

**Invalid carrier code**
- Run `get-carriers.js` to see supported carriers
- Use exact code from the carriers list
- Try `pholder` if carrier is not supported

## Security Notes

- API keys should be treated as passwords - never commit them to version control
- Premium subscription required for API access
- API keys can be regenerated at web.parcelapp.net if compromised
- Rate limits prevent abuse; respect them to maintain API access

## Additional Resources

For detailed API documentation, see [references/API.md](references/API.md).

For setup instructions, see [references/SETUP.md](references/SETUP.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mezzle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
