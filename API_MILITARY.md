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

### Get Battle Statistics

**Method:** GET
**URL:** `/en/military/battle-stats/{battleId}/{division}/{battleZoneId}`
**Auth Required:** Yes

#### Description

Retrieves detailed statistics for a specific battle zone, including top damage dealers for each side in all divisions, overall stats, zone status, active battle effects, and fighter information. This endpoint is typically polled during active combat to update the battlefield UI.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| battleId | number | Yes | The ID of the battle |
| division | number | Yes | Division number (1-4 for ground, 11 for air) |
| battleZoneId | number | Yes | The specific battle zone ID |

#### Headers

| Header           | Value                                            | Required    |
| ---------------- | ------------------------------------------------ | ----------- |
| Cookie           | `erpk=YOUR_SESSION_TOKEN`                        | Yes         |
| X-Requested-With | `XMLHttpRequest`                                 | Yes         |
| Accept           | `application/json, text/javascript, */*; q=0.01` | Recommended |

#### Example Request

```bash
curl 'https://www.erepublik.com/en/military/battle-stats/863499/11/37859365' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01'
```

#### Example Response

```json
{
  "stats": {
    "personal": [],
    "current": {
      "6": {
        "1": {
          "14": {
            "37858081": {
              "top_damage": [
                {
                  "battle_zone_id": 37858081,
                  "battle_id": 863499,
                  "zone_id": 6,
                  "division": 1,
                  "citizen_id": 9741009,
                  "damage": 215,
                  "kills": 1,
                  "side_country_id": 14,
                  "type": "top_damage",
                  "level": 12,
                  "sector": ""
                }
              ]
            }
          },
          "72": {
            "37858081": {
              "top_damage": [
                {
                  "battle_zone_id": 37858081,
                  "battle_id": 863499,
                  "zone_id": 6,
                  "division": 1,
                  "citizen_id": 9741009,
                  "damage": 215,
                  "kills": 1,
                  "side_country_id": 72,
                  "type": "top_damage",
                  "level": 12,
                  "sector": ""
                }
              ]
            }
          }
        },
        "4": {
          "14": {
            "37858084": {
              "top_damage": [
                {
                  "battle_zone_id": 37858084,
                  "battle_id": 863499,
                  "zone_id": 6,
                  "division": 4,
                  "citizen_id": 3257328,
                  "damage": 275002739,
                  "kills": 60,
                  "side_country_id": 14,
                  "type": "top_damage",
                  "level": 638,
                  "sector": ""
                }
              ]
            }
          }
        }
      }
    },
    "overall": []
  },
  "zone_finished": false,
  "division": {
    "created_at": 1769345796,
    "bar": {
      "37859365": 14
    },
    "domination": {
      "37859365": 50
    },
    "defence_shield": {
      "37859365": null
    },
    "72": {
      "37859365": {
        "domination": 0,
        "won": 0
      },
      "total": 17
    },
    "14": {
      "37859365": {
        "domination": 110,
        "won": 0
      },
      "total": 91
    }
  },
  "logs": {
    "attackers": [],
    "defenders": []
  },
  "fightersData": {
    "4009762": {
      "id": 4009762,
      "name": "ogius",
      "avatar": "https://cdnt.erepublik.net/HMWQL5S3qtIsRgP4qKawitAYNHw=/30x30/smart/avatars/Citizens/2010/10/25/c3df46043a674ac97aee9fab5b5cf2a6.jpg"
    },
    "6521752": {
      "id": 6521752,
      "name": "J. Jogreth",
      "avatar": "https://cdnt.erepublik.net/SMd-KXdHetNQyp92hNoepQ93zjM=/30x30/smart/avatars/Citizens/2012/09/17/e0c976b4873af307f9596a917a12a5e5.png?d6f58076e458a4451a74ab35eb81dd52"
    }
  },
  "opponentsInQueue": 0,
  "isInQueue": false,
  "campaigns": [],
  "epicBattle": 0,
  "activeEffects": [],
  "battleEffects": [],
  "maxHit": 0,
  "most_contested": [],
  "battle_zone_situation": {
    "37859361": 0,
    "37859362": 0,
    "37859363": 0,
    "37859364": 0,
    "37859365": 0
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `stats.personal` | array | Personal stats for the authenticated citizen |
| `stats.current` | object | Current top damage stats by zone → division → country → battle zone |
| `stats.current.*.*.*.*.top_damage` | array | Top damage dealers with citizen info, damage, kills, level |
| `stats.overall` | array | Overall battle statistics |
| `zone_finished` | boolean | Whether the current zone has finished |
| `division.created_at` | number | Unix timestamp when division round was created |
| `division.bar` | object | Map of battle zone ID → country ID holding the wall |
| `division.domination` | object | Map of battle zone ID → domination percentage |
| `division.defence_shield` | object | Map of battle zone ID → shield status (null if no shield) |
| `division.{countryId}` | object | Per-country stats including domination and wins |
| `division.{countryId}.total` | number | Total points/wins for the country in this division |
| `logs.attackers` | array | Combat logs for attacking side |
| `logs.defenders` | array | Combat logs for defending side |
| `fightersData` | object | Map of citizen ID → citizen details (name, avatar) |
| `fightersData.*.avatar` | string | CDN URL for citizen avatar (30x30) |
| `opponentsInQueue` | number | Number of opponents in queue (for direct fights) |
| `isInQueue` | boolean | Whether the citizen is currently in combat queue |
| `campaigns` | array | Related campaign data |
| `epicBattle` | number | Epic battle status (0 = not epic) |
| `activeEffects` | array | Currently active battle effects/boosters |
| `battleEffects` | array | Available battle effects |
| `maxHit` | number | Maximum hit damage recorded |
| `most_contested` | array | Most contested zones info |
| `battle_zone_situation` | object | Map of battle zone ID → situation status code |

#### Notes

- **Real-time updates**: This endpoint is typically polled every few seconds during active combat to refresh the battlefield UI
- **Top damage structure**: `stats.current` is nested as `{zoneId → divisionId → countryId → battleZoneId → top_damage[]}`
- **Country IDs**: The numeric keys (e.g., "14", "72") represent country IDs for invaders and defenders
- **Division numbers**: 1-4 represent ground divisions, 11 represents the air division
- **Avatar CDN**: Avatar URLs use Cloudflare CDN with `smart` resizing and size parameters (30x30)
- **Domination values**: Can exceed 100, representing accumulated domination points
- **Defence shield**: `null` indicates no shield active; non-null values indicate shield health/status
- **Zone situation codes**: The `battle_zone_situation` values likely indicate special states (0 = normal)

---

### Get Battle Statistics (Console)

**Method:** POST
**URL:** `/en/military/battle-console`
**Auth Required:** Yes

#### Description

Retrieves paginated battle statistics for a specific battle, round, and division using POST parameters. Returns detailed round information across all divisions and top damage dealers (leaderboards) for each country side. This endpoint is used by the battle console UI to display fighter statistics, leaderboards, and round data with pagination support.

This is a **POST variant** of battle statistics that differs from the GET `/battle-stats` endpoint - it uses the `action=battleStatistics` parameter and provides paginated leaderboard data.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| battleId | number | Yes | The ID of the battle |
| zoneId | number | Yes | The zone/round number |
| round | number | Yes | The round number (same as zoneId) |
| division | number | Yes | Division number (1-4 for ground, 11 for air) |
| battleZoneId | number | Yes | The specific battle zone ID |
| action | string | Yes | Must be "battleStatistics" |
| type | string | Yes | Statistics type (e.g., "damage") |
| leftPage | number | Yes | Page number for left side (invader) leaderboard |
| rightPage | number | Yes | Page number for right side (defender) leaderboard |
| _token | string | Yes | CSRF token for security |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/battle-console' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'battleId=863499&zoneId=7&action=battleStatistics&round=7&division=11&battleZoneId=37859365&type=damage&leftPage=1&rightPage=1&_token=YOUR_CSRF_TOKEN'
```

#### Example Response

```json
{
  "rounds": {
    "37859365": {
      "id": 37859365,
      "zone": 7,
      "round": 7,
      "division": 11,
      "created_at": 1769345107,
      "type": "aircraft",
      "terrain_type_id": null,
      "started": 1,
      "finished": 0
    },
    "37858085": {
      "id": 37858085,
      "zone": 6,
      "round": 6,
      "division": 11,
      "created_at": 1769338746,
      "type": "aircraft",
      "terrain_type_id": null,
      "started": 1,
      "finished": 1
    },
    "37859364": {
      "id": 37859364,
      "zone": 7,
      "round": 7,
      "division": 4,
      "created_at": 1769345107,
      "type": "tanks",
      "terrain_type_id": null,
      "started": 1,
      "finished": 0
    }
  },
  "72": {
    "fighterData": {
      "1": {
        "citizenId": 2886612,
        "citizenName": "ghoghnooos",
        "country_name": "Iran",
        "citizenAvatar": "https://cdnt.erepublik.net/YHNhtt2L2mJ1nL43ygz7PtZgfDM=/30x30/smart/avatars/Citizens/2010/03/05/1dd7c446109ffa07dccbbb124b6e69de.jpg?a11fd74342a8cd81a4e5f8f0b06a65e6",
        "country_permalink": "Iran",
        "for_country_id": 72,
        "value": "12,836",
        "raw_value": 12836,
        "reward": false
      }
    },
    "pages": 0
  },
  "14": {
    "fighterData": {
      "1": {
        "citizenId": 1752353,
        "citizenName": "Awang",
        "country_name": "Malaysia",
        "citizenAvatar": "https://cdnt.erepublik.net/aqhbCUPdjxyP_RCbFjbAtocRFBM=/30x30/smart/avatars/Citizens/2009/08/11/5ade5c1f5bccf9d3b3046aa9b41a4959.jpg?42fa4b51f92caead74118e921488ea7f",
        "country_permalink": "Malaysia",
        "for_country_id": 14,
        "value": "32,534",
        "raw_value": 32534,
        "reward": false
      }
    },
    "pages": 0
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `rounds` | object | Map of battle zone ID → round details for all divisions |
| `rounds.*.id` | number | Battle zone ID |
| `rounds.*.zone` | number | Zone/round number |
| `rounds.*.round` | number | Round number (same as zone) |
| `rounds.*.division` | number | Division number (1-4 = ground, 11 = air) |
| `rounds.*.created_at` | number | Unix timestamp when round was created |
| `rounds.*.type` | string | Battle type ("aircraft" for air, "tanks" for ground) |
| `rounds.*.terrain_type_id` | number/null | Terrain modifier ID (null if no terrain) |
| `rounds.*.started` | number | 1 if round has started, 0 otherwise |
| `rounds.*.finished` | number | 1 if round is finished, 0 if ongoing |
| `{countryId}` | object | Country-specific leaderboard data (e.g., "72", "14") |
| `{countryId}.fighterData` | object | Map of rank → fighter details |
| `{countryId}.fighterData.*.citizenId` | number | Citizen ID of the fighter |
| `{countryId}.fighterData.*.citizenName` | string | Display name of the fighter |
| `{countryId}.fighterData.*.country_name` | string | Fighter's home country name |
| `{countryId}.fighterData.*.citizenAvatar` | string | CDN URL for fighter's avatar (30x30) |
| `{countryId}.fighterData.*.country_permalink` | string | Country permalink/slug |
| `{countryId}.fighterData.*.for_country_id` | number | Country ID the fighter fought for |
| `{countryId}.fighterData.*.value` | string | Formatted damage value with comma separators |
| `{countryId}.fighterData.*.raw_value` | number | Raw damage value as number |
| `{countryId}.fighterData.*.reward` | boolean | Whether fighter received reward |
| `{countryId}.pages` | number | Total number of pages available for this side's leaderboard |

#### Notes

- **POST method required**: Unlike the GET `/battle-stats` endpoint, this uses POST with form data
- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Pagination**: Use `leftPage` and `rightPage` to paginate through leaderboards for each side
  - Page numbers start at 1
  - `pages: 0` in response indicates only one page of results
- **Country ID keys**: The numeric keys (e.g., "72", "14") are country IDs representing each side of the battle
  - These correspond to the invader and defender countries
- **Round data scope**: The `rounds` object includes data for **all divisions** in the specified zone/round, not just the requested division
- **Fighter ranking**: The keys in `fighterData` (e.g., "1") represent the rank/position on the leaderboard
- **Battle types**:
  - `type: "aircraft"` for air division (division 11)
  - `type: "tanks"` for ground divisions (divisions 1-4)
- **Terrain modifiers**: `terrain_type_id` indicates special terrain effects; `null` means default terrain
- **Avatar CDN**: Uses Cloudflare CDN with smart resizing at 30x30 pixels
- **Damage formatting**: Both formatted (`value`) and raw (`raw_value`) damage are provided
- **Action parameter**: The `action=battleStatistics` parameter determines which data subset to return

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
