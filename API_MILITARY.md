# eRepublik API - Military

#erepublik #api #military #combat

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Military endpoints cover combat mechanics, battles, wars, weapons, and related functionality.

---

## Endpoints

### List Active Campaigns

**Method:** GET
**URL:** `/en/military/campaignsJson/list`
**Auth Required:** No

#### Description

Returns a JSON object containing all currently active battles/campaigns, participating countries, and timing information. This is a public endpoint that does not require authentication.

#### Parameters

None

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Accept | `application/json` | No |
| User-Agent | Browser user agent | Recommended |

#### Example Request

```bash
curl 'https://www.erepublik.com/en/military/campaignsJson/list' \
  -H 'Accept: application/json'
```

#### Example Response

```json
{
  "battles": {
    "863509": {
      "id": 863509,
      "war_id": 204121,
      "zone_id": 5,
      "is_rw": false,
      "is_as": false,
      "type": "tanks",
      "start": 1769337065,
      "det": 1,
      "region": { "id": 91, "name": "Northern Basarabia" },
      "city": { "id": 271, "name": "Balti" },
      "is_dict": false,
      "is_lib": false,
      "war_type": "direct",
      "inv": { "id": 40, "allies": [], "ally_list": [], "points": 15 },
      "def": { "id": 52, "allies": [], "ally_list": [], "points": 74 },
      "div": {
        "37857731": {
          "id": 37857731,
          "div": 1,
          "end": 1769344144,
          "division_end": true,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 40, "dom": 99.83 },
          "terrain": 0
        },
        "37857732": {
          "id": 37857732,
          "div": 2,
          "end": 1769342524,
          "division_end": true,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 52, "dom": 50 },
          "terrain": 0
        },
        "37857733": {
          "id": 37857733,
          "div": 3,
          "end": 1769342524,
          "division_end": true,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 52, "dom": 71.43 },
          "terrain": 0
        },
        "37857734": {
          "id": 37857734,
          "div": 4,
          "end": 1769342645,
          "division_end": true,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 52, "dom": 68.25 },
          "terrain": 0
        },
        "37857735": {
          "id": 37857735,
          "div": 11,
          "end": 1769342645,
          "division_end": true,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 52, "dom": 62.59 },
          "terrain": 0
        }
      },
      "terrainTypes": [0],
      "effects": null,
      "hasMultipleTerrains": false,
      "isMultiZone": true
    }
  },
  "countries": {
    "167": { "id": 167, "name": "Albania", "allies": [], "is_empire": false, "cotd": 0 },
    "27": { "id": 27, "name": "Argentina", "allies": [], "is_empire": false, "cotd": 863489 }
  },
  "last_updated": 1769344252,
  "time": 1769344632
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `battles` | object | Map of battle ID → battle details |
| `battles.*.is_rw` | boolean | Is resistance war |
| `battles.*.is_as` | boolean | Is airstrike |
| `battles.*.type` | string | Battle type (e.g., "tanks") |
| `battles.*.war_type` | string | War type ("direct", etc.) |
| `battles.*.inv` | object | Invader country info with points |
| `battles.*.def` | object | Defender country info with points |
| `battles.*.div` | object | Division rounds with wall domination |
| `battles.*.div.*.div` | number | Division number (1-4 = ground, 11 = air) |
| `battles.*.div.*.wall.for` | number | Country ID currently holding the wall |
| `battles.*.div.*.wall.dom` | number | Wall domination percentage (0-100) |
| `battles.*.div.*.intensity_scale` | string | Battle intensity ("cold_war", etc.) |
| `battles.*.div.*.co` | object | Combat orders for invader and defender |
| `countries` | object | Map of country ID → country info |
| `countries.*.cotd` | number | Campaign of the day battle ID (0 if none) |
| `last_updated` | number | Unix timestamp of last data update |
| `time` | number | Current server Unix timestamp |

#### Notes

- Returns all active battles across all countries
- Country IDs in `inv.id` and `def.id` reference the `countries` map
- **Divisions:** Each battle has 5 divisions:
  - `div: 1` - Division 1 (ground)
  - `div: 2` - Division 2 (ground)
  - `div: 3` - Division 3 (ground)
  - `div: 4` - Division 4 (ground)
  - `div: 11` - Air division
- `epic` values indicate epic battle status (0 = not epic)
- `division_end: true` indicates the round has finished
- `terrain: 0` is default terrain; other values indicate special terrain modifiers

---

### Get Citizen Campaigns Data

**Method:** GET
**URL:** `/en/military/campaignsJson/citizen`
**Auth Required:** Yes

#### Description

Returns personalized campaign/battle data for the authenticated citizen, including their fighting eligibility (mercenary/freedom fighter status), battle zone selections, and combat stats for battles they've participated in. This is a companion endpoint to `/en/military/campaignsJson/list` - use both together to get complete battle information with citizen-specific context.

#### Parameters

None

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json` | No |

#### Example Request

```bash
curl 'https://www.erepublik.com/en/military/campaignsJson/citizen' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json'
```

#### Example Response

```json
{
  "battles": {
    "863498": {
      "citizenStats": {
        "72": {
          "zones": 1,
          "current": 0,
          "damage": 24536,
          "kills": 51,
          "activeZones": []
        }
      },
      "selectedBattleZoneId": 37858205,
      "aircraftZoneId": 37858205,
      "groundZoneId": 37858204,
      "isMercenary": false,
      "isFreedomFighter": false
    },
    "863565": {
      "citizenStats": null,
      "selectedBattleZoneId": 37858095,
      "aircraftZoneId": 37858095,
      "groundZoneId": 37858094,
      "isMercenary": true,
      "isFreedomFighter": false
    },
    "863465": {
      "citizenStats": null,
      "selectedBattleZoneId": 37858350,
      "aircraftZoneId": 37858350,
      "groundZoneId": 37858349,
      "isMercenary": false,
      "isFreedomFighter": true
    }
  },
  "contributions": null,
  "deployment": null,
  "time": 1769345424
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `battles` | object | Map of battle ID → citizen-specific battle info |
| `battles.*.citizenStats` | object/null | Citizen's stats in this battle (null if hasn't fought) |
| `battles.*.citizenStats.{countryId}` | object | Stats keyed by country ID the citizen fought for |
| `battles.*.citizenStats.*.zones` | number | Number of zones/rounds participated in |
| `battles.*.citizenStats.*.current` | number | Current round participation (0 = not fighting) |
| `battles.*.citizenStats.*.damage` | number | Total damage dealt in this battle |
| `battles.*.citizenStats.*.kills` | number | Total kills in this battle |
| `battles.*.citizenStats.*.activeZones` | array | Currently active zones |
| `battles.*.selectedBattleZoneId` | number | Currently selected battle zone ID |
| `battles.*.aircraftZoneId` | number | Air division zone ID |
| `battles.*.groundZoneId` | number | Ground division zone ID |
| `battles.*.isMercenary` | boolean | Whether citizen can fight as mercenary in this battle |
| `battles.*.isFreedomFighter` | boolean | Whether citizen can fight as freedom fighter in this battle |
| `contributions` | object/null | Citizen's contributions data (if applicable) |
| `deployment` | object/null | Citizen's military unit deployment info (if applicable) |
| `time` | number | Current server Unix timestamp |

#### Notes

- **Authentication required**: This endpoint requires a valid session cookie
- **Complements list endpoint**: Use with `/en/military/campaignsJson/list` to get complete battle info with citizen context
- **Mercenary eligibility**: `isMercenary: true` indicates the citizen can fight for either side (not a citizen of involved countries)
- **Freedom Fighter eligibility**: `isFreedomFighter: true` indicates the citizen can support a resistance war
- **citizenStats country key**: The key inside `citizenStats` (e.g., "72") is the country ID the citizen fought for in that battle
- **Zone IDs**: The `groundZoneId` and `aircraftZoneId` reference division zones in the battle, corresponding to entries in the `/campaignsJson/list` response's `div` section

---

## Template

Use this template when documenting new endpoints:

```markdown
## Endpoint Name

**Method:** GET/POST
**URL:** `/en/path/to/endpoint`
**Auth Required:** Yes/No

### Description

Brief explanation of what this endpoint does.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| param1 | string | Yes | Description |

### Headers

| Header | Value | Description |
|--------|-------|-------------|
| Content-Type | application/json | ... |

### Example Request (cURL)

\`\`\`bash
curl -X GET "https://www.erepublik.com/en/..." \
  -H "Cookie: erpk=..."
\`\`\`

### Example Response

\`\`\`json
{
  "success": true,
  "data": {}
}
\`\`\`

### Notes

Any additional information, gotchas, or observations.
```

---

## Related

- [API Table of Contents](API_TOC.md)
- [Authentication](API_AUTH.md)
- [Social API](API_SOCIAL.md)
- [Economy API](API_ECONOMY.md)
