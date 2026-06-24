---
name: dubai-guide
description: Manage the Dubai Lifestyle Database. Log places (with Google data), recommend spots based on weather/vibe, and track visits. Use when this capability is needed.
metadata:
  author: hashmil
---

# Dubai Guide Skill

> **⚠️ API Key Required:** This skill requires the `goplaces` skill and a Google Places API key.
> Set up `goplaces` first and configure your API key via `goplaces auth` or environment variable.

This skill manages `memory/dubai-tracker.json`. Use it to log new visits, update entries, or ask for recommendations.

## Data Source
- **File:** `memory/dubai-tracker.json`
- **Helper:** `goplaces` CLI (for fetching address, rating, location data).

## First-Time Setup

If `memory/dubai-tracker.json` doesn't exist, the skill will create it with this structure:

```json
{
  "city": "Dubai",
  "places": []
}
```

## Actions

### 1. Log a New Place
**Trigger:** "Log [Place Name]", "I went to [Place]", "Add [Place]"
**Procedure:**
1.  **Check Database:** Ensure `memory/dubai-tracker.json` exists. If not, create it with the structure above.
2.  **Search:** Run `goplaces search "[Place Name] Dubai" --limit 1 --json`.
3.  **Verify:** Check if the result matches the user's intent.
4.  **Read DB:** Read `memory/dubai-tracker.json`.
5.  **Append:** Add a new entry to the `places` array using the **Schema** below.
    *   *Auto-fill:* Name, Google Place ID, Details (rating, address), Location.
    *   *Infer:* Area (from address), Type (from `types`), Environment (Indoor/Outdoor based on type/photos if available, or ask user).
    *   *Ask User:* specific notes, "Summer Safe?" (if unclear), "Vibe".
5.  **Save:** Write back to `memory/dubai-tracker.json`.

### 2. Log a Repeat Visit
**Trigger:** "We went back to [Place]", "Visited [Place] again"
**Procedure:**
1.  **Find:** Locate the place in `memory/dubai-tracker.json`.
2.  **Update:** Add a new object to the `visits` array with `date` (YYYY-MM-DD) and `notes`.

### 3. Recommend
**Trigger:** "Where should I go?", "Suggest a place for [activity]", "Dinner spot?"
**Procedure:**
1.  **Analyze Context:**
    *   **Weather:** Is it "burning hot" (Summer) or "nice" (Winter)?
    *   **Vibe:** Chill, fancy, work, kids?
2.  **Filter DB:** Query `memory/dubai-tracker.json`.
    *   *If Summer:* Filter for `environment: "Indoor"` or `seasonality: "Summer Safe"`.
    *   *If Winter:* Prefer `environment: "Outdoor"` or `seasonality: "Winter Only"`.
3.  **Vibe Check (Consensus Search):**
    *   For the top candidate, run multiple targeted searches across different platforms:
        *   **Reddit:** "[Place] Dubai reddit review tips"
        *   **Twitter/X:** "[Place] Dubai twitter review experience"
        *   **TripAdvisor:** "[Place] Dubai tripadvisor review"
        *   **Google Reviews:** "[Place] Dubai google reviews recent"
        *   **Blogs:** "[Place] Dubai blog review 2026"
    *   Look for: Parking chaos, best arrival times, hidden gems, "tourist trap" warnings, crowd levels, and recent experiences.
4.  **Output:** Present 3 top options with their status (Visited/Wishlist) and a **"Pro Tip"** derived from the search (e.g., "Go at 5 PM for sunset, park at Mall").

## JSON Schema (Enforced)

```json
{
  "name": "string (Official Name)",
  "area": "string (e.g. Jumeirah, DIFC)",
  "type": "string (Cafe, Beach, Mall, Dinner, Activity)",
  "environment": "Indoor ❄️ | Outdoor ☀️ | Hybrid 🌓",
  "seasonality": "Summer Safe 🛡️ | Winter Only ❄️ | All Year 🗓️",
  "vibe": "comma-separated tags (e.g. work, date-night, loud, quiet)",
  "status": "Visited ✅ | Wishlist 📌",
  "google_place_id": "string",
  "details": {
    "rating": number,
    "address": "string",
    "location": { "lat": number, "lng": number }
  },
  "visits": [
    {
      "date": "YYYY-MM-DD",
      "notes": "User comments (parking, food, service)",
      "companions": ["names"]
    }
  ]
}
```

## Nuance Guide
- **Summer Safe:** Malls, heavily AC'd cafes, indoor play areas.
- **Winter Only:** Beaches, parks, outdoor terraces, Global Village, Miracle Garden.
- **Parking:** Always note if parking is "Valet only" or "Easy".

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hashmil) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
