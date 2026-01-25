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
