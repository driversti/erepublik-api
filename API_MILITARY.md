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

### List Country Campaigns

**Method:** GET
**URL:** `/en/military/campaignsJson/countryId/{countryId}`
**Auth Required:** No

#### Description

Returns a JSON object containing all currently active battles/campaigns filtered for a specific country. This is a variant of `/en/military/campaignsJson/list` that only returns battles where the specified country is either the invader or defender. This is a public endpoint that does not require authentication.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryId | number | Yes | The country ID to filter battles for (URL path parameter) |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Accept | `application/json` | No |
| X-Requested-With | `XMLHttpRequest` | Recommended |
| User-Agent | Browser user agent | Recommended |

#### Example Request

```bash
curl 'https://www.erepublik.com/en/military/campaignsJson/countryId/29' \
  -H 'Accept: application/json' \
  -H 'X-Requested-With: XMLHttpRequest'
```

#### Example Response

```json
{
  "battles": {
    "863688": {
      "id": 863688,
      "war_id": 223123,
      "zone_id": 1,
      "is_rw": false,
      "is_as": false,
      "type": "tanks",
      "start": 1769353943,
      "det": 1,
      "region": { "id": 260, "name": "Aosta Valley" },
      "city": { "id": 368, "name": "Aosta" },
      "is_dict": false,
      "is_lib": false,
      "war_type": "direct",
      "inv": { "id": 10, "allies": [], "ally_list": [], "points": 0 },
      "def": { "id": 29, "allies": [], "ally_list": [], "points": 0 },
      "div": {
        "37861141": {
          "id": 37861141,
          "div": 1,
          "end": null,
          "division_end": false,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 29, "dom": 100 },
          "terrain": 4
        },
        "37861142": {
          "id": 37861142,
          "div": 2,
          "end": null,
          "division_end": false,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 29, "dom": 50 },
          "terrain": 4
        },
        "37861145": {
          "id": 37861145,
          "div": 11,
          "end": null,
          "division_end": false,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 10, "dom": 100 },
          "terrain": 0
        }
      },
      "terrainTypes": [4, 0],
      "effects": null,
      "hasMultipleTerrains": false,
      "isMultiZone": true
    },
    "863663": {
      "id": 863663,
      "war_id": 224176,
      "zone_id": 3,
      "is_rw": false,
      "is_as": false,
      "type": "tanks",
      "start": 1769355665,
      "det": 1,
      "region": { "id": 110, "name": "Central Transdanubia" },
      "city": { "id": 276, "name": "Szekesfehervar" },
      "is_dict": false,
      "is_lib": false,
      "war_type": "direct",
      "inv": { "id": 29, "allies": [], "ally_list": [], "points": 0 },
      "def": { "id": 13, "allies": [], "ally_list": [], "points": 36 },
      "div": {
        "37861411": {
          "id": 37861411,
          "div": 1,
          "end": null,
          "division_end": false,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 13, "dom": 50 },
          "terrain": 0
        },
        "37861415": {
          "id": 37861415,
          "div": 11,
          "end": null,
          "division_end": false,
          "epic": 0,
          "epic_type": 0,
          "intensity_scale": "cold_war",
          "co": { "inv": [], "def": [] },
          "wall": { "for": 13, "dom": 56.45 },
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
    "13": { "id": 13, "name": "Hungary", "allies": [], "is_empire": false, "cotd": 863670 },
    "10": { "id": 10, "name": "Italy", "allies": [], "is_empire": false, "cotd": 0 },
    "29": { "id": 29, "name": "United Kingdom", "allies": [], "is_empire": false, "cotd": 0 }
  },
  "last_updated": 1769358144,
  "time": 1769358146
}
```

#### Response Fields

See [List Active Campaigns](#list-active-campaigns) for detailed field descriptions. The response structure is identical.

#### Notes

- Returns only battles where the specified country is invader (`inv.id`) or defender (`def.id`)
- The `countries` object only includes countries relevant to the filtered battles
- Useful for building country-specific battle dashboards or tracking a nation's active wars
- Country IDs can be obtained from the full campaigns list or other API endpoints
- Common country IDs: 29 = United Kingdom, 1 = Romania, 10 = Italy, 13 = Hungary, 72 = Lithuania

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
      "8": {
        "2": {
          "52": {
            "38158387": {
              "top_damage": [
                {
                  "battle_zone_id": 38158387,
                  "battle_id": 869119,
                  "zone_id": 8,
                  "division": 2,
                  "citizen_id": 9617915,
                  "damage": 489819,
                  "kills": 4,
                  "side_country_id": 52,
                  "type": "top_damage",
                  "level": 49,
                  "sector": ""
                }
              ]
            }
          },
          "72": {
            "38158387": {
              "top_damage": [
                {
                  "battle_zone_id": 38158387,
                  "battle_id": 869119,
                  "zone_id": 8,
                  "division": 2,
                  "citizen_id": 9617915,
                  "damage": 4549974,
                  "kills": 28,
                  "side_country_id": 72,
                  "type": "top_damage",
                  "level": 49,
                  "sector": ""
                }
              ]
            }
          }
        },
        "4": {
          "72": {
            "38158389": {
              "top_damage": [
                {
                  "battle_zone_id": 38158389,
                  "battle_id": 869119,
                  "zone_id": 8,
                  "division": 4,
                  "citizen_id": 2100702,
                  "damage": 1938717198,
                  "kills": 198,
                  "side_country_id": 72,
                  "type": "top_damage",
                  "level": 1066,
                  "sector": ""
                }
              ]
            }
          },
          "52": {
            "38158389": {
              "top_damage": [
                {
                  "battle_zone_id": 38158389,
                  "battle_id": 869119,
                  "zone_id": 8,
                  "division": 4,
                  "citizen_id": 7780887,
                  "damage": 243775256,
                  "kills": 26,
                  "side_country_id": 52,
                  "type": "top_damage",
                  "level": 1690,
                  "sector": ""
                }
              ]
            }
          }
        },
        "11": {
          "52": {
            "38158390": {
              "top_damage": [
                {
                  "battle_zone_id": 38158390,
                  "battle_id": 869119,
                  "zone_id": 8,
                  "division": 11,
                  "citizen_id": 7780887,
                  "damage": 28329,
                  "kills": 19,
                  "side_country_id": 52,
                  "type": "top_damage",
                  "level": 1690,
                  "sector": ""
                }
              ]
            }
          },
          "72": {
            "38158390": {
              "top_damage": [
                {
                  "battle_zone_id": 38158390,
                  "battle_id": 869119,
                  "zone_id": 8,
                  "division": 11,
                  "citizen_id": 9637574,
                  "damage": 146160,
                  "kills": 210,
                  "side_country_id": 72,
                  "type": "top_damage",
                  "level": 775,
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
    "created_at": 1770898418,
    "bar": {
      "38158390": 72
    },
    "domination": {
      "38158390": 83.7646
    },
    "defence_shield": {
      "38158390": 0
    },
    "52": {
      "38158390": {
        "domination": 0,
        "won": 0
      },
      "total": 4
    },
    "72": {
      "38158390": {
        "domination": 90,
        "won": 0
      },
      "total": 122
    }
  },
  "logs": {
    "attackers": [],
    "defenders": []
  },
  "fightersData": {
    "2100702": {
      "id": 2100702,
      "name": "Gedoras",
      "avatar": "https://cdnt.erepublik.net/nJR31VeRruwr3428BVYALe_byZM=/30x30/smart/avatars/Citizens/2009/10/30/426a1f6b4acdb80141909b7bdda037be.jpg"
    },
    "7780887": {
      "id": 7780887,
      "name": "NuRupeNik",
      "avatar": "https://cdnt.erepublik.net/Xg1r0JGIeBiskTvAG63olitD7kU=/30x30/smart/avatars/Citizens/2013/07/11/c2ba951a95ece74cf543782fae6e3f7f.jpg?0d12907546b8fcc998389ac5a3ca6bd1"
    },
    "9617915": {
      "id": 9617915,
      "name": "Lazauskas",
      "avatar": "https://cdnt.erepublik.net/kN03gJYxEtDcYYN6C9QiRI6mB4U=/30x30/smart/avatars/Citizens/2021/03/11/29f28f4dc725488d508ce75899b62af3.jpg?90f8a943944e5c76a0fce2a4fdaf0745"
    },
    "9637574": {
      "id": 9637574,
      "name": "K0rsakoff",
      "avatar": "https://cdnt.erepublik.net/QwFvBoPimFffiWJhzLRLL_3Hrho=/30x30/smart/avatars/Citizens/2021/10/23/995edeb7e1b5377f012a2924b40c76b3.jpg?739399a9db0f2cb22474d68c224363ee"
    }
  },
  "opponentsInQueue": 0,
  "isInQueue": false,
  "campaigns": [],
  "epicBattle": 0,
  "activeEffects": [],
  "battleEffects": {
    "valentinesDay": {
      "is_active": 0
    }
  },
  "maxHit": 134,
  "most_contested": [],
  "battle_zone_situation": {
    "38158386": 0,
    "38158387": 0,
    "38158388": 0,
    "38158389": 0,
    "38158390": 0
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
| `division.defence_shield` | object | Map of battle zone ID → shield value (`null` = no shield, `0` = shield inactive/depleted, positive number = shield active) |
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
| `battleEffects` | object | Named battle effects keyed by event name (e.g., `valentinesDay`), each with `is_active` flag |
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
- **Defence shield**: `null` = no shield mechanic, `0` = shield inactive/depleted, positive number = shield active with remaining health
- **Battle effects**: `battleEffects` is an object keyed by event name (e.g., `valentinesDay`) with `is_active` flag (0/1) — tracks seasonal/holiday combat modifiers
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
- **Type parameter values**: The `type` parameter filters the leaderboard type:
  - `"damage"` - Returns top damage dealers
  - `"kills"` - Returns top kill counts
  - `"overall"` - Returns overall/combined statistics

---

### Get Battle Console Data

**Method:** POST
**URL:** `/en/military/battle-console`
**Auth Required:** Yes

#### Description

Retrieves comprehensive battle state data including all divisions' status, wall positions, domination points, campaign points, and overall battle progress. This endpoint uses the `action=battleConsole` parameter and provides a high-level overview of the entire battle across all divisions, unlike the `battleStatistics` action which focuses on fighter leaderboards. This is typically used to render the battle console UI showing the current state of all divisions in a campaign.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| battleId | number | Yes | The ID of the battle |
| zoneId | number | Yes | The zone/round number |
| action | string | Yes | Must be "battleConsole" |
| battleZoneId | number | Yes | The specific battle zone ID |
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
  --data-raw 'battleId=864000&zoneId=9&action=battleConsole&battleZoneId=37886839&_token=YOUR_CSRF_TOKEN'
```

#### Example Response

```json
{
  "division": [
    {
      "id": 37886836,
      "division": 1,
      "countries": {
        "53": {
          "roundDominationPoints": 0,
          "defenceShield": 0,
          "totalPointsWonByDivision": 0,
          "wall": 0,
          "combatOrders": []
        },
        "42": {
          "roundDominationPoints": 1800,
          "defenceShield": null,
          "totalPointsWonByDivision": 1,
          "wall": 100,
          "combatOrders": []
        },
        "divisionWinner": 42,
        "dominatingCountry": 42
      },
      "type": "tank",
      "divisionWinner": false,
      "isEpic": false,
      "epicState": 0,
      "epicBattleProgress": 0,
      "campaignPoints": 1,
      "winnerInfo": {
        "message": "Bulgaria won this campaign",
        "isConquered": false,
        "countryName": "Bulgaria",
        "countryPermalink": "Bulgaria",
        "waiting": false,
        "timeUntil": 0,
        "campaignFinished": true
      }
    },
    {
      "id": 37886837,
      "division": 2,
      "countries": {
        "53": {
          "roundDominationPoints": 0,
          "defenceShield": 0,
          "totalPointsWonByDivision": 0,
          "wall": 50,
          "combatOrders": []
        },
        "42": {
          "roundDominationPoints": 1800,
          "defenceShield": null,
          "totalPointsWonByDivision": 2,
          "wall": 50,
          "combatOrders": []
        },
        "divisionWinner": 42,
        "dominatingCountry": 42
      },
      "type": "tank",
      "divisionWinner": false,
      "isEpic": false,
      "epicState": 0,
      "epicBattleProgress": 0,
      "campaignPoints": 2,
      "winnerInfo": {
        "message": "Bulgaria won this campaign",
        "isConquered": false,
        "countryName": "Bulgaria",
        "countryPermalink": "Bulgaria",
        "waiting": false,
        "timeUntil": 0,
        "campaignFinished": true
      }
    },
    {
      "id": 37886840,
      "division": 11,
      "countries": {
        "53": {
          "roundDominationPoints": 0,
          "defenceShield": 0,
          "totalPointsWonByDivision": 0,
          "wall": 49.25,
          "combatOrders": []
        },
        "42": {
          "roundDominationPoints": 1800,
          "defenceShield": null,
          "totalPointsWonByDivision": 7,
          "wall": 50.75,
          "combatOrders": []
        },
        "divisionWinner": 42,
        "dominatingCountry": 42
      },
      "type": "airforce",
      "divisionWinner": false,
      "isEpic": false,
      "epicState": 0,
      "epicBattleProgress": 1.8,
      "campaignPoints": 7,
      "winnerInfo": {
        "message": "Bulgaria won this campaign",
        "isConquered": false,
        "countryName": "Bulgaria",
        "countryPermalink": "Bulgaria",
        "waiting": false,
        "timeUntil": 0,
        "campaignFinished": true
      }
    }
  ],
  "totalPointsByDivision": {
    "1": { "42": 6, "53": 3 },
    "2": { "42": 18, "53": 0 },
    "3": { "42": 27, "53": 0 },
    "4": { "42": 45, "53": 0 },
    "11": { "42": 56, "53": 7 }
  },
  "totalPoints": {
    "53": 10,
    "42": 152
  },
  "isResistance": false,
  "isRevolution": false,
  "isDictatorship": false,
  "allies": {
    "42": [],
    "53": []
  },
  "battle_time": 87794,
  "serverTime": 1769578638
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `division` | array | Array of all 5 divisions (1-4 ground, 11 air) with detailed status |
| `division[].id` | number | Battle zone ID for this division |
| `division[].division` | number | Division number (1-4 = ground, 11 = air) |
| `division[].countries` | object | Per-country battle status including wall and domination |
| `division[].countries.{countryId}` | object | Country-specific stats within this division |
| `division[].countries.{countryId}.roundDominationPoints` | number | Domination points accumulated this round (max 1800) |
| `division[].countries.{countryId}.defenceShield` | number/null | Defence shield value (null if no shield active) |
| `division[].countries.{countryId}.totalPointsWonByDivision` | number | Total campaign points won in this division |
| `division[].countries.{countryId}.wall` | number | Wall position percentage (0-100) |
| `division[].countries.{countryId}.combatOrders` | array | Active combat orders for this country |
| `division[].countries.divisionWinner` | number | Country ID that won this division round |
| `division[].countries.dominatingCountry` | number | Country ID currently dominating this division |
| `division[].type` | string | Battle type ("tank" for ground, "airforce" for air) |
| `division[].divisionWinner` | boolean | Whether this division round has concluded |
| `division[].isEpic` | boolean | Whether this is an epic battle |
| `division[].epicState` | number | Epic battle state (0 = not epic) |
| `division[].epicBattleProgress` | number | Progress toward epic battle status |
| `division[].campaignPoints` | number | Total campaign points at stake in this division |
| `division[].winnerInfo` | object | Winner information (populated when round finished) |
| `division[].winnerInfo.message` | string | Human-readable winner message |
| `division[].winnerInfo.isConquered` | boolean | Whether the region was conquered |
| `division[].winnerInfo.countryName` | string | Name of the winning country |
| `division[].winnerInfo.countryPermalink` | string | URL-safe country name |
| `division[].winnerInfo.waiting` | boolean | Whether waiting for next round |
| `division[].winnerInfo.timeUntil` | number | Seconds until next event |
| `division[].winnerInfo.campaignFinished` | boolean | Whether the entire campaign has finished |
| `totalPointsByDivision` | object | Campaign points per division, keyed by division number |
| `totalPointsByDivision.{divisionNum}` | object | Points per country for this division |
| `totalPoints` | object | Overall campaign points, keyed by country ID |
| `isResistance` | boolean | Whether this is a resistance war |
| `isRevolution` | boolean | Whether this is a revolution battle |
| `isDictatorship` | boolean | Whether this involves a dictatorship |
| `allies` | object | Allied countries for each side, keyed by country ID |
| `allies.{countryId}` | array | Array of allied country IDs for this side |
| `battle_time` | number | Elapsed battle time in seconds |
| `serverTime` | number | Unix timestamp of the server time |

#### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Difference from battleStatistics**: This endpoint (`action=battleConsole`) returns high-level battle state data for all divisions, while `action=battleStatistics` returns paginated fighter leaderboards for a specific division
- **Division structure**: The `division` array contains all 5 divisions regardless of which `battleZoneId` is specified:
  - Divisions 1-4 are ground divisions (`type: "tank"`)
  - Division 11 is the air division (`type: "airforce"`)
- **Wall mechanics**:
  - Wall values range from 0-100, representing the percentage controlled by each side
  - When a country's wall reaches 100%, they win the round
  - Wall values for both countries should add up to approximately 100
- **Domination points**:
  - `roundDominationPoints` accumulates during combat (max 1800 typically)
  - Higher domination leads to faster wall movement
- **Campaign points**:
  - `campaignPoints` in each division shows the point value for winning that division
  - `totalPointsByDivision` tracks accumulated points per country per division
  - `totalPoints` shows the overall campaign score
- **Winner determination**: When `campaignFinished: true`, the `winnerInfo` object contains final results
- **Battle types**:
  - `isResistance`: True for resistance wars (citizens fighting occupation)
  - `isRevolution`: True for revolution battles (against government)
  - `isDictatorship`: True when a dictatorship is involved
- **Country IDs**: The numeric keys (e.g., "42", "53") represent country IDs for invaders and defenders
- **Use case**: This endpoint is ideal for building battle dashboards or monitoring overall campaign progress

---

## Get Armory Overview

**Method:** POST
**URL:** `/en/military/armory-data/overview`
**Auth Required:** Yes

#### Description

Retrieves the authenticated citizen's complete armory overview, including all owned and available military vehicles (tanks and aircraft) with their enrollment status, country-specific protection levels, damage bonuses, and blueprint progress. This endpoint provides a comprehensive view of the citizen's military vehicle collection and progression system.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
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
curl -X POST 'https://www.erepublik.com/en/military/armory-data/overview' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

#### Example Response

```json
{
  "panelUrl": "/en/military/armory-data/overview",
  "vehicles": [
    {
      "id": 9,
      "type": "tanks",
      "isActive": false,
      "name": "S-07 Sentinel",
      "desc": "<p>The <b>S-07 Sentinel</b> was obtained from the eRepublik <b>12 Years Anniversary Challenge</b>.</p>",
      "status": "enrolled",
      "intStatus": 1,
      "isDefault": false,
      "blueprints": null,
      "countryId": 40,
      "countryData": {
        "id": 40,
        "level": 50,
        "maxLevel": 50,
        "xp": null,
        "requiredXP": null,
        "damageBonus": 200,
        "title": "Protector of Ukraine"
      },
      "title": "Protector of Ukraine",
      "attributes": [
        {
          "type": "damageBonus",
          "value": 200
        }
      ],
      "displayPriority": 1
    },
    {
      "id": 17,
      "type": "tanks",
      "isActive": false,
      "name": "D4 Overlord",
      "desc": "<p>The D4 Overlord brings its own genre of drama to the battlefield.</p>",
      "status": "enrolled",
      "intStatus": 1,
      "isDefault": true,
      "blueprints": null,
      "countryId": 65,
      "countryData": {
        "id": 65,
        "level": 27,
        "maxLevel": 50,
        "xp": 1682228238,
        "requiredXP": 2000000000,
        "damageBonus": 41,
        "title": "Protector of Serbia"
      },
      "title": "Protector of Serbia",
      "attributes": [
        {
          "type": "damageBonus",
          "value": 41
        }
      ],
      "displayPriority": 5
    },
    {
      "id": 30,
      "type": "aircraft",
      "isActive": true,
      "name": "F8U-16 Gunslinger",
      "desc": "<p>The <b>F8U-16 Gunslinger</b> was obtained from the <b>Amazing Journey</b> 16th anniversary event.</p>",
      "status": "enrolled",
      "intStatus": 1,
      "isDefault": false,
      "blueprints": null,
      "countryId": 72,
      "countryData": {
        "id": 72,
        "level": 13,
        "maxLevel": 50,
        "xp": 20309840,
        "requiredXP": 43076000,
        "damageBonus": 13,
        "title": "Protector of Lithuania"
      },
      "title": "Protector of Lithuania",
      "attributes": [
        {
          "type": "damageBonus",
          "value": 13
        }
      ],
      "displayPriority": 12
    },
    {
      "id": 1,
      "type": "tanks",
      "isActive": false,
      "name": "Red Baron",
      "desc": "<p>The <i>Red Baron</i> tank skin was obtained during <b>Winner Takes it All Event</b>.</p>",
      "status": "notEnrolled",
      "intStatus": 2,
      "isDefault": false,
      "blueprints": null,
      "countryId": null,
      "countryData": null,
      "title": null,
      "attributes": [],
      "displayPriority": 18
    },
    {
      "id": 2,
      "type": "tanks",
      "isActive": false,
      "name": "Monster Grinder",
      "desc": "<p>The <i>Monster Grinder</i> tank skin was obtained during <b>Tales of the New World: Headless Horror Event</b>.</p>",
      "status": "blueprints",
      "intStatus": 4,
      "isDefault": false,
      "blueprints": {
        "current": 0,
        "required": 10
      },
      "countryId": null,
      "countryData": null,
      "title": null,
      "attributes": [],
      "displayPriority": 34
    }
  ],
  "statuses": {
    "unavailable": "Unavailable",
    "blueprints": "Locked",
    "notAssembled": "Assemble",
    "notEnrolled": "Enroll",
    "enrolled": "Enrolled"
  },
  "settings": {
    "maxEnrollPerCountry": 3,
    "universalPoints": {
      "tanks": 4081310987,
      "aircraft": 0
    }
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `panelUrl` | string | URL path to the armory overview panel |
| `vehicles` | array | List of all available vehicles (tanks and aircraft) |
| `vehicles[].id` | number | Unique vehicle ID |
| `vehicles[].type` | string | Vehicle type ("tanks" or "aircraft") |
| `vehicles[].isActive` | boolean | Whether this vehicle is currently active in combat |
| `vehicles[].name` | string | Display name of the vehicle |
| `vehicles[].desc` | string | HTML description of the vehicle and how it was obtained |
| `vehicles[].status` | string | Vehicle status (see `statuses` mapping) |
| `vehicles[].intStatus` | number | Integer status code (1=enrolled, 2=notEnrolled, 4=blueprints) |
| `vehicles[].isDefault` | boolean | Whether this is a default vehicle available to all players |
| `vehicles[].blueprints` | object/null | Blueprint progress if status is "blueprints", null otherwise |
| `vehicles[].blueprints.current` | number | Number of blueprints currently owned |
| `vehicles[].blueprints.required` | number | Total blueprints required to unlock |
| `vehicles[].countryId` | number/null | Country ID for which this vehicle is enrolled (null if not enrolled) |
| `vehicles[].countryData` | object/null | Country-specific protection level data (null if not enrolled) |
| `vehicles[].countryData.id` | number | Country ID |
| `vehicles[].countryData.level` | number | Current protection level for this country |
| `vehicles[].countryData.maxLevel` | number | Maximum protection level (typically 50) |
| `vehicles[].countryData.xp` | number/null | Current XP points (null if max level reached) |
| `vehicles[].countryData.requiredXP` | number/null | XP required for next level (null if max level) |
| `vehicles[].countryData.damageBonus` | number | Damage bonus percentage from this protection level |
| `vehicles[].countryData.title` | string | Protection title (e.g., "Protector of Ukraine") |
| `vehicles[].title` | string/null | Current protection title for enrolled vehicles |
| `vehicles[].attributes` | array | List of vehicle attributes (typically damage bonus) |
| `vehicles[].attributes[].type` | string | Attribute type (e.g., "damageBonus") |
| `vehicles[].attributes[].value` | number | Attribute value |
| `vehicles[].displayPriority` | number | Display order priority (lower = higher priority) |
| `statuses` | object | Map of status keys to human-readable labels |
| `statuses.unavailable` | string | Label for unavailable vehicles |
| `statuses.blueprints` | string | Label for locked vehicles requiring blueprints |
| `statuses.notAssembled` | string | Label for vehicles that need assembly |
| `statuses.notEnrolled` | string | Label for owned but not enrolled vehicles |
| `statuses.enrolled` | string | Label for enrolled vehicles |
| `settings` | object | Armory configuration settings |
| `settings.maxEnrollPerCountry` | number | Maximum number of vehicles that can be enrolled per country |
| `settings.universalPoints` | object | Universal points available for vehicle upgrades |
| `settings.universalPoints.tanks` | number | Universal points for tank upgrades |
| `settings.universalPoints.aircraft` | number | Universal points for aircraft upgrades |

#### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Vehicle statuses**:
  - `enrolled` (intStatus=1): Vehicle is enrolled for a specific country and can be used in combat
  - `notEnrolled` (intStatus=2): Vehicle is owned but not enrolled for any country
  - `blueprints` (intStatus=4): Vehicle is locked and requires collecting blueprints to unlock
- **Country enrollment**: Each vehicle can be enrolled for one country at a time, earning "Protector of {Country}" titles
- **Protection levels**: Each country enrollment has its own XP progression system (levels 1-50)
- **Damage bonus**: Higher protection levels provide increased damage bonus percentages
- **Default vehicles**: Some vehicles (`isDefault: true`) are available to all players at certain division thresholds
- **Universal points**: Accumulated points that can be used to upgrade vehicle protection levels across countries
- **Active vehicle**: Only one tank and one aircraft can be `isActive: true` at a time for ground and air combat respectively
- **Display priority**: Vehicles are sorted by `displayPriority`, with enrolled vehicles typically appearing first
- **HTML descriptions**: The `desc` field contains HTML-formatted text with vehicle lore and obtaining requirements
- **Anniversary vehicles**: Many special vehicles were obtained from anniversary events and challenges
- **Blueprint system**: Locked vehicles show current/required blueprint counts (typically 0-10 blueprints needed)

---

## Get Armory Protectors

**Method:** POST
**URL:** `/en/military/armory-data/protectors`
**Auth Required:** Yes

#### Description

Retrieves the authenticated citizen's complete Protector status across all countries, showing country-specific protection levels, XP progression, damage bonuses, and enrolled vehicles for both tanks and aircraft. This endpoint provides a comprehensive overview of the citizen's country-by-country vehicle protection system, including earned titles and universal points available for upgrades.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
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
curl -X POST 'https://www.erepublik.com/en/military/armory-data/protectors' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

#### Example Response

```json
{
  "panelUrl": "/en/military/armory-data/protectors",
  "countries": [
    {
      "id": 72,
      "tanks": {
        "level": 50,
        "maxLevel": 50,
        "xp": null,
        "requiredXP": null,
        "levelProgress": "100%",
        "damageBonus": 200,
        "title": "Protector of Lithuania",
        "vehicles": [20]
      },
      "aircraft": {
        "level": 13,
        "maxLevel": 50,
        "xp": 20309840,
        "requiredXP": 22766160,
        "levelProgress": "47.1489%",
        "damageBonus": 13,
        "title": "Protector of Lithuania",
        "vehicles": [30]
      }
    },
    {
      "id": 40,
      "tanks": {
        "level": 50,
        "maxLevel": 50,
        "xp": null,
        "requiredXP": null,
        "levelProgress": "100%",
        "damageBonus": 200,
        "title": "Protector of Ukraine",
        "vehicles": [9]
      },
      "aircraft": {
        "level": 11,
        "maxLevel": 50,
        "xp": 9785703,
        "requiredXP": 1258297,
        "levelProgress": "88.6065%",
        "damageBonus": 11,
        "title": "Protector of Ukraine",
        "vehicles": [10]
      }
    },
    {
      "id": 1,
      "tanks": {
        "level": 28,
        "maxLevel": 50,
        "xp": 2527825031,
        "requiredXP": 1472174969,
        "levelProgress": "63.1956%",
        "damageBonus": 44,
        "title": "Protector of Romania",
        "vehicles": [6]
      },
      "aircraft": {
        "level": 6,
        "maxLevel": 50,
        "xp": 397571,
        "requiredXP": 31429,
        "levelProgress": "92.6739%",
        "damageBonus": 6,
        "title": "Protector of Romania",
        "vehicles": [26]
      }
    },
    {
      "id": 57,
      "tanks": {
        "level": 0,
        "maxLevel": 50,
        "xp": 822183,
        "requiredXP": 127817,
        "levelProgress": "86.5456%",
        "damageBonus": 0,
        "title": "Protector of Pakistan",
        "vehicles": []
      },
      "aircraft": {
        "level": 0,
        "maxLevel": 50,
        "xp": 1350,
        "requiredXP": 7750,
        "levelProgress": "14.8352%",
        "damageBonus": 0,
        "title": "Protector of Pakistan",
        "vehicles": []
      }
    }
  ],
  "settings": {
    "maxEnrollPerCountry": 3,
    "universalPoints": {
      "tanks": 4081310987,
      "aircraft": 0
    }
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `panelUrl` | string | URL path to the armory protectors panel |
| `countries` | array | List of all countries with protection progress |
| `countries[].id` | number | Country ID |
| `countries[].tanks` | object | Tank protection data for this country |
| `countries[].tanks.level` | number | Current protection level (0-50) |
| `countries[].tanks.maxLevel` | number | Maximum protection level (typically 50) |
| `countries[].tanks.xp` | number/null | Current XP points (null if max level reached) |
| `countries[].tanks.requiredXP` | number/null | XP needed for next level (null if max level) |
| `countries[].tanks.levelProgress` | string | Progress percentage toward next level (e.g., "63.1956%") |
| `countries[].tanks.damageBonus` | number | Damage bonus percentage at current level |
| `countries[].tanks.title` | string | Protection title (e.g., "Protector of Ukraine") |
| `countries[].tanks.vehicles` | array | Array of enrolled tank vehicle IDs for this country |
| `countries[].aircraft` | object | Aircraft protection data for this country |
| `countries[].aircraft.level` | number | Current protection level (0-50) |
| `countries[].aircraft.maxLevel` | number | Maximum protection level (typically 50) |
| `countries[].aircraft.xp` | number/null | Current XP points (null if max level reached) |
| `countries[].aircraft.requiredXP` | number/null | XP needed for next level (null if max level) |
| `countries[].aircraft.levelProgress` | string | Progress percentage toward next level |
| `countries[].aircraft.damageBonus` | number | Damage bonus percentage at current level |
| `countries[].aircraft.title` | string | Protection title |
| `countries[].aircraft.vehicles` | array | Array of enrolled aircraft vehicle IDs for this country |
| `settings` | object | Armory configuration settings |
| `settings.maxEnrollPerCountry` | number | Maximum vehicles that can be enrolled per country (typically 3) |
| `settings.universalPoints` | object | Universal points available for protection upgrades |
| `settings.universalPoints.tanks` | number | Universal points for tank protection upgrades |
| `settings.universalPoints.aircraft` | number | Universal points for aircraft protection upgrades |

#### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Country ordering**: Countries are typically sorted by total protection level/progress (highest first)
- **Protection system**:
  - Each country has independent protection levels for tanks and aircraft (0-50)
  - Fighting with enrolled vehicles earns XP toward the next protection level
  - Higher levels provide increased damage bonuses
  - At level 0, citizens can still earn XP but receive no damage bonus
- **Max level behavior**: When `level: 50` is reached, both `xp` and `requiredXP` become `null`, and `levelProgress` shows "100%"
- **Damage bonuses**:
  - Bonuses scale with protection levels (typically 2% per level at higher tiers)
  - Level 50 provides 200% damage bonus (maximum)
  - Level 0 provides 0% damage bonus
- **Vehicle enrollment**: The `vehicles` arrays contain vehicle IDs that are currently enrolled for that country
  - Empty arrays indicate no vehicles are enrolled, but XP can still be earned with default vehicles
  - Vehicle IDs correspond to the `id` field from the `/armory-data/overview` endpoint
- **Universal points**:
  - Can be spent to increase protection levels without fighting
  - Separate pools for tanks and aircraft
  - Accumulated through gameplay and events
- **Title format**: All titles follow the pattern "Protector of {Country Name}"
- **XP progression**:
  - XP requirements increase significantly at higher levels
  - Fighting for a country with an enrolled vehicle earns XP for that country's protection level
  - Different battles may provide different XP amounts
- **Level progress**: The `levelProgress` percentage shows how far through the current level the citizen is (current XP / required XP for next level)

---

## Get Battle Deployment Inventory

**Method:** POST
**URL:** `/en/military/fightDeploy-getInventory`
**Auth Required:** Yes

#### Description

Retrieves the authenticated citizen's complete combat inventory for deploying to a specific battle, including available weapons (with damage per hit calculations), energy sources (food, energy bars, and pool energy), vehicles with country-specific enrollment status, and recoverable energy. This endpoint is called when preparing to fight in a battle zone to determine available combat resources and deployment options.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for security |
| battleId | number | Yes | The ID of the battle to deploy to |
| sideCountryId | number | Yes | The country ID for the side being joined |
| battleZoneId | number | Yes | The specific battle zone ID to deploy to |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-getInventory' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&battleId=863500&sideCountryId=72&battleZoneId=37861235'
```

#### Example Response

```json
{
  "weapons": [
    {
      "industryId": 23,
      "quality": -1,
      "name": "No weapon",
      "amount": null,
      "preselect": true,
      "attributes": {
        "firepower": null,
        "maxEnergy": null
      },
      "damageperHit": 143,
      "isBest": false
    },
    {
      "industryId": 23,
      "quality": 5,
      "name": "Air-to-Air Missile Q5",
      "amount": 1169,
      "uses": 5844,
      "preselect": false,
      "attributes": {
        "firepower": "+100%",
        "maxEnergy": 58450
      },
      "damageperHit": 286,
      "isBest": true
    }
  ],
  "energySources": [
    {
      "type": "pool",
      "energy": 6654
    },
    {
      "type": "food",
      "tier": 1,
      "quality": 1,
      "amount": 284072,
      "energy": 568144
    },
    {
      "type": "food",
      "tier": 2,
      "quality": 2,
      "amount": 5678,
      "energy": 22712
    },
    {
      "type": "food",
      "tier": 3,
      "quality": 3,
      "amount": 2332887,
      "energy": 13997322
    },
    {
      "type": "energy_bar",
      "tier": 11,
      "quality": 11,
      "amount": 50,
      "energy": 10000
    }
  ],
  "recoverableEnergy": 0,
  "minEnergy": 10,
  "fuelConsumed": 1,
  "vehicles": [
    {
      "id": 30,
      "type": "aircraft",
      "isActive": true,
      "name": "F8U-16 Gunslinger",
      "status": "enrolled",
      "intStatus": 1,
      "isDefault": false,
      "blueprints": null,
      "countryId": 72,
      "countryData": {
        "id": 72,
        "level": 13,
        "maxLevel": 50,
        "xp": 20309840,
        "requiredXP": 43076000,
        "damageBonus": 13,
        "title": "Protector of Lithuania"
      },
      "title": "Protector of Lithuania",
      "attributes": [
        {
          "type": "damageBonus",
          "value": 13
        }
      ],
      "isRecommended": true
    },
    {
      "id": 40,
      "type": "aircraft",
      "isActive": false,
      "name": "Ember Firefly SH-18",
      "status": "notEnrolled",
      "intStatus": 2,
      "isDefault": false,
      "blueprints": null,
      "countryId": null,
      "countryData": null,
      "title": null,
      "attributes": [],
      "isRecommended": false
    }
  ],
  "poolEnergy": 6654,
  "maxEnergy": 16654,
  "recoverableEnergyBuyFood": 0
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `weapons` | array | List of available weapons for this battle type (air or ground) |
| `weapons[].industryId` | number | Industry ID for the weapon type (e.g., 23 = aircraft weapons) |
| `weapons[].quality` | number | Weapon quality (-1 for no weapon, 1-7 for weapons) |
| `weapons[].name` | string | Display name of the weapon |
| `weapons[].amount` | number/null | Number of weapons in inventory (null for "No weapon") |
| `weapons[].uses` | number | Total uses available (amount × uses per weapon) |
| `weapons[].preselect` | boolean | Whether this weapon should be preselected in UI |
| `weapons[].attributes` | object | Weapon attributes (firepower, maxEnergy) |
| `weapons[].attributes.firepower` | string/null | Firepower bonus (e.g., "+100%") |
| `weapons[].attributes.maxEnergy` | number/null | Maximum energy that can be consumed per hit |
| `weapons[].damageperHit` | number | Calculated damage per hit with this weapon |
| `weapons[].isBest` | boolean | Whether this is the highest quality weapon available |
| `energySources` | array | List of available energy sources (food, energy bars, pool) |
| `energySources[].type` | string | Energy source type ("pool", "food", "energy_bar") |
| `energySources[].tier` | number | Tier/quality level (food: 1-7, energy bars: 11) |
| `energySources[].quality` | number | Quality level (matches tier) |
| `energySources[].amount` | number | Number of items in inventory (not present for pool) |
| `energySources[].energy` | number | Total energy available from this source |
| `recoverableEnergy` | number | Energy that can be recovered from previous battles |
| `minEnergy` | number | Minimum energy cost per hit (typically 10) |
| `fuelConsumed` | number | Fuel consumed per hit (for vehicles) |
| `vehicles` | array | List of all available vehicles for this battle type |
| `vehicles[].id` | number | Unique vehicle ID |
| `vehicles[].type` | string | Vehicle type ("aircraft" or "tanks") |
| `vehicles[].isActive` | boolean | Whether this vehicle is currently active |
| `vehicles[].name` | string | Display name of the vehicle |
| `vehicles[].status` | string | Enrollment status ("enrolled", "notEnrolled", etc.) |
| `vehicles[].intStatus` | number | Integer status code (1=enrolled, 2=notEnrolled) |
| `vehicles[].isDefault` | boolean | Whether this is a default vehicle |
| `vehicles[].blueprints` | object/null | Blueprint progress (null if not applicable) |
| `vehicles[].countryId` | number/null | Country ID for enrolled vehicles |
| `vehicles[].countryData` | object/null | Country-specific protection data |
| `vehicles[].countryData.level` | number | Protection level for this country (1-50) |
| `vehicles[].countryData.damageBonus` | number | Damage bonus percentage |
| `vehicles[].countryData.title` | string | Protection title (e.g., "Protector of Lithuania") |
| `vehicles[].title` | string/null | Current protection title |
| `vehicles[].attributes` | array | Vehicle attributes (typically damage bonus) |
| `vehicles[].isRecommended` | boolean | Whether this vehicle is recommended for this battle |
| `poolEnergy` | number | Current pool energy available |
| `maxEnergy` | number | Maximum energy capacity |
| `recoverableEnergyBuyFood` | number | Energy recoverable by purchasing food |

#### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Battle type detection**: The endpoint automatically determines whether the battle zone is air or ground division and returns appropriate weapons and vehicles
  - Air divisions (division 11) return aircraft and air-to-air missiles
  - Ground divisions (1-4) return tanks and ground weapons
- **Weapon selection**:
  - `preselect: true` indicates the default weapon (usually "No weapon")
  - `isBest: true` marks the highest quality weapon available
  - Higher quality weapons provide better `damageperHit` values
- **Damage calculation**: The `damageperHit` value is pre-calculated based on:
  - Base damage
  - Weapon quality and firepower bonuses
  - Vehicle protection level and damage bonuses
  - Citizen level and attributes
- **Energy sources**:
  - `type: "pool"` represents the citizen's energy pool (regenerates over time)
  - `type: "food"` represents consumable food items of various qualities
  - `type: "energy_bar"` represents premium energy bars (typically Q11)
- **Food tiers**:
  - Tier 1 (Q1): 2 energy per unit
  - Tier 2 (Q2): 4 energy per unit
  - Tier 3 (Q3): 6 energy per unit
  - Higher tiers provide proportionally more energy
- **Vehicle recommendations**:
  - `isRecommended: true` indicates the vehicle is enrolled for the battle's side country
  - Vehicles enrolled for the correct country provide damage bonuses
  - Vehicles enrolled for other countries appear but are not recommended
- **Weapon uses**: The `uses` field shows total available uses (e.g., 1169 weapons × 5 uses each = 5844 total uses)
- **Energy limits**: `maxEnergy` represents the citizen's maximum energy capacity including pool and storage
- **Industry IDs**:
  - 23 = Aircraft weapons (air-to-air missiles)
  - 1 = Ground weapons (various weapon types)
- **Quality scale**: Weapons range from Q1 (lowest) to Q7 (highest), with Q5+ providing significant bonuses
- **No weapon option**: Always available with `quality: -1` and basic damage per hit
- **Country matching**: The `sideCountryId` parameter determines which vehicles are recommended based on their enrollment status

---

## Start Battle Deployment

**Method:** POST
**URL:** `/en/military/fightDeploy-startDeploy`
**Auth Required:** Yes

#### Description

Initiates a battle deployment (auto-fight) for the authenticated citizen in a specific battle zone. This endpoint queues the citizen for combat with their specified energy allocation, weapon quality, and vehicle selection. The system will automatically fight using the configured resources until the energy is depleted or the battle round ends. This is the companion endpoint to `/en/military/fightDeploy-getInventory` - first retrieve available resources with getInventory, then start the deployment with this endpoint.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for security |
| battleId | number | Yes | The ID of the battle to deploy to |
| battleZoneId | number | Yes | The specific battle zone ID to deploy to |
| sideCountryId | number | Yes | The country ID for the side being joined |
| weaponQuality | number | Yes | Weapon quality to use (-1 = no weapon, 1-7 for weapons) |
| totalEnergy | number | Yes | Total energy to allocate for this deployment |
| energySources[n][quality] | number | Yes | Quality/tier of the energy source (1-3 for food, 11 for energy bars) |
| energySources[n][amount] | number | Yes | Amount of energy to consume from this source |
| skinId | number | Yes | Vehicle/skin ID to use for combat |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-startDeploy' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&battleId=863657&battleZoneId=37861650&sideCountryId=29&weaponQuality=-1&totalEnergy=340&energySources%5B0%5D%5Bquality%5D=3&energySources%5B0%5D%5Bamount%5D=0&skinId=18'
```

#### Example Response

```json
{
  "error": false,
  "message": "Successfully deployed",
  "deploymentId": 122127169,
  "data": {
    "energyItems": {
      "food": 0,
      "energyBars": []
    },
    "energyTotal": 340,
    "weaponType": "aircraft",
    "chosenWeaponQuality": -1,
    "weaponQuality": -1,
    "battleId": 863657,
    "sideCountryId": 29,
    "battleZoneId": 37861650,
    "skinId": 18,
    "fuelLeft": 36
  }
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | Whether an error occurred (false = success) |
| `message` | string | Human-readable status message |
| `deploymentId` | number | Unique ID for this deployment session |
| `data` | object | Deployment details |
| `data.energyItems` | object | Energy sources consumed |
| `data.energyItems.food` | number | Amount of food energy consumed |
| `data.energyItems.energyBars` | array | Energy bars consumed |
| `data.energyTotal` | number | Total energy allocated for this deployment |
| `data.weaponType` | string | Type of combat ("aircraft" for air, "tanks" for ground) |
| `data.chosenWeaponQuality` | number | The weapon quality selected by the user |
| `data.weaponQuality` | number | The actual weapon quality being used |
| `data.battleId` | number | The battle ID for this deployment |
| `data.sideCountryId` | number | The country ID the citizen is fighting for |
| `data.battleZoneId` | number | The battle zone ID for this deployment |
| `data.skinId` | number | The vehicle/skin ID being used |
| `data.fuelLeft` | number | Remaining fuel for the vehicle after deployment starts |

#### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Workflow**: This endpoint is typically called after `/en/military/fightDeploy-getInventory`:
  1. Call `getInventory` to retrieve available weapons, energy sources, and vehicles
  2. Select desired combat configuration
  3. Call `startDeploy` to begin the auto-fight deployment
- **Energy sources array**: The `energySources` parameter is an array of objects, each specifying a quality tier and amount:
  - Format: `energySources[0][quality]=3&energySources[0][amount]=100`
  - Multiple sources can be specified: `energySources[1][quality]=11&energySources[1][amount]=50`
- **Weapon quality values**:
  - `-1` = No weapon (base damage only)
  - `1-7` = Weapon quality levels (Q1-Q7)
- **Vehicle selection**: The `skinId` must be a vehicle the citizen owns and should be appropriate for the battle type:
  - Aircraft skins for air divisions (division 11)
  - Tank skins for ground divisions (divisions 1-4)
- **Fuel consumption**: The `fuelLeft` value shows remaining vehicle fuel after deployment; fuel is consumed per hit
- **Auto-fight behavior**: Once deployed, the system automatically executes combat until:
  - All allocated energy is consumed
  - The battle round ends
  - The citizen cancels the deployment
- **Energy allocation**: The `totalEnergy` should match the sum of energy from all sources in `energySources`
- **Deployment ID**: Save the `deploymentId` to check deployment status or cancel the deployment later
- **Country matching**: For optimal damage bonuses, use a vehicle enrolled for the `sideCountryId` country

---

## Get Battlefield Page

**Method:** GET
**URL:** `/en/military/battlefield/{battleId}`
**Auth Required:** Yes

### Description

Returns the full battlefield HTML page for a specific battle. This is **not a JSON API** — it returns a 200+ KB HTML document with embedded JavaScript containing rich battle state data.

The useful data is embedded in a `<script id="BATTLE_SERVER_DATA">` tag as:

```javascript
SERVER_DATA = $j.extend(SERVER_DATA, { /* battle state JSON */ });
```

This merges battle-specific fields into the global `SERVER_DATA` object (which already contains session-level data like `csrfToken`, `isLoggedIn`, `serverTime` from an earlier `<script>` block on line ~49).

### Path Parameters

| Parameter  | Type   | Required | Description                          |
|------------|--------|----------|--------------------------------------|
| `battleId` | number | Yes      | The battle (campaign) ID to load     |

### Headers

| Header                  | Value                                                                                      | Required |
|-------------------------|--------------------------------------------------------------------------------------------|----------|
| `Cookie`                | `erpk=SESSION_TOKEN; erpk_mid=...; erpk_plang=en; erpk_auth=1; erpk_rm=...; cf_clearance=...` | Yes      |
| `Accept`                | `text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8`                         | Yes      |
| `User-Agent`            | Standard browser UA                                                                        | Yes      |
| `sec-fetch-dest`        | `document`                                                                                 | No       |
| `sec-fetch-mode`        | `navigate`                                                                                 | No       |
| `upgrade-insecure-requests` | `1`                                                                                    | No       |

### Example Request

```bash
curl 'https://www.erepublik.com/en/military/battlefield/851234' \
  -H 'accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' \
  -H 'accept-language: en-GB,en;q=0.9' \
  -b 'erpk=YOUR_SESSION_TOKEN; erpk_mid=YOUR_MID; erpk_plang=en; erpk_auth=1; erpk_rm=YOUR_RM_TOKEN; cf_clearance=YOUR_CF_TOKEN' \
  -H 'sec-fetch-dest: document' \
  -H 'sec-fetch-mode: navigate' \
  -H 'upgrade-insecure-requests: 1' \
  -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36'
```

### Response

**Status:** `200 OK`
**Content-Type:** `text/html; charset=UTF-8`
**Size:** ~221 KB (full HTML page)

### Embedded `BATTLE_SERVER_DATA` JSON Structure

The `<script id="BATTLE_SERVER_DATA">` block contains 90+ fields. Below is the full structure with example values from a **finished** battle (Latvia vs Sweden, battle 851234).

> **Note:** Date fields use `new Date('...')` in the raw JavaScript. When parsing, convert these to strings first (see [Data Extraction](#data-extraction) below).

#### Battle Metadata

```json
{
  "initialLoad": true,
  "battleId": 851234,
  "battleZoneId": 37214694,
  "countryId": 71,
  "invaderId": 71,
  "defenderId": 38,
  "leftBattleId": 38,
  "rightBattleId": 71,
  "realInvaderId": 71,
  "realDefenderId": 38,
  "zoneId": 12,
  "currentRoundNumber": 12,
  "battleFinished": 1,
  "isResistance": false,
  "isCivilWar": false,
  "isLiberation": false,
  "isDictatorship": false,
  "isMultiZone": true,
  "mustInvert": false,
  "spectatorOnly": true,
  "onAirforceBattlefield": false,
  "battle_close_to_finish": 0
}
```

#### Timestamps

```json
{
  "battle_start_at": "Tue, 16 Dec 2025 17:59:05 -0800",
  "battle_critical_at": "Tue, 16 Dec 2025 19:59:05 -0800",
  "server_time": "Sun, 08 Feb 2026 02:01:41 -0800",
  "zoneElapsedTime": "1280:2:36"
}
```

> Raw JS uses `new Date('...')` for `battle_start_at`, `battle_critical_at`, and `server_time`.

#### Campaign Points & Scores

```json
{
  "campaignPointsThreshold": 150,
  "points": {
    "defender": 210,
    "attacker": 1650
  },
  "campaignHeroId": {
    "38": 45422,
    "71": 1637391
  }
}
```

- `campaignPointsThreshold` — points needed to win the campaign
- `campaignHeroId` — keys are country IDs, values are citizen IDs of campaign heroes

#### Battle Zone Situation

```json
{
  "battleZoneSituation": {
    "37214691": 0,
    "37214692": 0,
    "37214693": 0,
    "37214694": 0,
    "37214695": 0
  },
  "activeBattleZones": [],
  "zoneStarted": false
}
```

- Keys in `battleZoneSituation` are `battleZoneId`s, values are situation codes (`0` = finished/inactive)
- `activeBattleZones` is empty for finished battles

#### Player State

```json
{
  "citizenId": 4690052,
  "militaryUnitId": 893,
  "regimentId": 1390,
  "division": 4,
  "currentDivision": 4,
  "naturalDivision": 4,
  "canSwitchDivisions": true,
  "health": 274,
  "level": 1716,
  "currentWeaponId": -1,
  "hasBazookaAmmo": -1,
  "weaponSkinId": 20,
  "opponentSkinId": 0,
  "mySideCountryId": 71,
  "showTravelPopup": false,
  "fightAgainstBrothers": false,
  "travelRequired": false,
  "has_jid": 1,
  "pvpKillCount": 0,
  "pvpShowLock": false,
  "isDeployOnly": false
}
```

- `currentWeaponId: -1` means no weapon selected
- `hasBazookaAmmo: -1` means no bazooka ammo available

#### Legend Data

Per-country vehicle protection levels with XP progression:

```json
{
  "legendData": {
    "71": {
      "id": 71,
      "tanks": {
        "level": 32,
        "maxLevel": 50,
        "xp": 34730991005,
        "requiredXP": 5269008995,
        "levelProgress": "86.8275%",
        "damageBonus": 60,
        "title": "Protector of Latvia",
        "vehicles": [35]
      },
      "aircraft": {
        "level": 3,
        "maxLevel": 50,
        "xp": 46545,
        "requiredXP": 25855,
        "levelProgress": "64.2887%",
        "damageBonus": 3,
        "title": "Protector of Latvia",
        "vehicles": []
      }
    },
    "38": {
      "id": 38,
      "tanks": {
        "level": 12,
        "maxLevel": 50,
        "xp": 6757808,
        "requiredXP": 242192,
        "levelProgress": "96.5401%",
        "damageBonus": 12,
        "title": "Protector of Sweden",
        "vehicles": []
      },
      "aircraft": {
        "level": 2,
        "maxLevel": 50,
        "xp": 23298,
        "requiredXP": 12202,
        "levelProgress": "65.6282%",
        "damageBonus": 2,
        "title": "Protector of Sweden",
        "vehicles": []
      }
    }
  }
}
```

- Keys are country IDs for the two sides of the battle
- `damageBonus` is the percentage bonus from legend level
- `vehicles` array lists active vehicle skin IDs

#### Special Weapons

Three weapon types — Big Bomb, Small Bomb, and Cruise Missile:

```json
{
  "specialWeapons": {
    "2_22": {
      "name": "Big Bomb",
      "id": "2_22",
      "industryId": 100,
      "quality": 22,
      "amount": 1944,
      "activable": 1,
      "deactivable": 0,
      "activableFromInventory": 0,
      "activableFromBattlefield": 1,
      "activationData": {
        "tooltip": "",
        "url": "/en/military/deploy-bomb",
        "params": { "bombId": 22 }
      },
      "active": 0,
      "icon": "/images/icons/industry/999/22.png",
      "tooltip": "Big Bomb",
      "token": "booster_q22",
      "attributes": {
        "firePower": { "id": "firePower", "name": "Fire power", "type": "use", "value": 5000000 },
        "durability": { "id": "durability", "name": "Durability", "type": "uses", "value": 1 },
        "expirationInfo": { "id": "expirationInfo", "type": "expirationInfo", "value": ["Expires on Day 6,665, 23:54"] }
      },
      "isRaw": 0,
      "isPartial": 0,
      "isBooster": 0,
      "isBomb": 1,
      "isPackBooster": 0,
      "isExpirable": 1,
      "type": "bomb",
      "is_expirable": 1
    },
    "2_215": {
      "name": "Cruise Missile",
      "id": "2_215",
      "industryId": 100,
      "quality": 215,
      "amount": 7,
      "activable": 1,
      "activableFromBattlefield": 1,
      "activationData": {
        "url": "/en/military/deploy-bomb",
        "params": { "bombId": 215 }
      },
      "attributes": {
        "firePower": { "value": 1500000 },
        "durability": { "value": 1 }
      },
      "isBomb": 1,
      "isExpirable": 1,
      "type": "bomb"
    },
    "2_21": {
      "name": "Small Bomb",
      "id": "2_21",
      "industryId": 100,
      "quality": 21,
      "amount": 1645,
      "activable": 1,
      "activableFromBattlefield": 1,
      "activationData": {
        "url": "/en/military/deploy-bomb",
        "params": { "bombId": 21 }
      },
      "attributes": {
        "firePower": { "value": 1500000 },
        "durability": { "value": 1 }
      },
      "isBomb": 1,
      "isExpirable": 1,
      "type": "bomb"
    }
  },
  "specialWeaponsDisabled": false,
  "bigBombsDisabled": false,
  "cruiseMissilesDisabled": false,
  "rocket_damage": 1500000,
  "bomb_damage": 5000000
}
```

- `activationData.url` → POST to `/en/military/deploy-bomb` with `bombId` param
- Big Bomb deals 5,000,000 damage; Small Bomb and Cruise Missile deal 1,500,000
- Special weapon key format: `{industryId}_{quality}` (e.g., `2_22`)

#### Boosters

```json
{
  "boosters": {
    "active": [],
    "inactive": { "...see categories below..." },
    "canUseCatchupBooster": false,
    "catchupBoosterTextDetails": null,
    "events": []
  },
  "canUseCatchupBooster": false,
  "regimentBoosters": [],
  "regimentBooster": false,
  "eventDamageBoosters": [],
  "eventDamageBooster": false
}
```

**Booster categories** (in `boosters.inactive`):

| Category | Description | Example IDs |
|----------|-------------|-------------|
| `catchup` | Ghost boosters — add % of best damage | `100_catchup_30_60` |
| `damage` | Ground damage multiplier | `100_damage_5_86400`, `100_damage_10_600`, `100_damage_15_600` |
| `speed` | Damage accelerators (hit faster) | `100_speed_1_180`, `100_speed_2_600`, `100_speed_3_600` |
| `prestige_points` | Prestige bonus per hit | `100_prestige_points_1_600` |
| `deploy_size` | Deploy size multiplier | `100_deploy_size_100_600`, `100_deploy_size_500_600` |
| `ground_deploy_influence` | Ground influence bonus | `100_ground_deploy_influence_1_600`, `100_ground_deploy_influence_3_600` |
| `ground_deploy_rank_points` | Ground rank points bonus | `100_ground_deploy_rank_points_1_600`, `100_ground_deploy_rank_points_3_600` |
| `air_damage` | Air damage multiplier | `100_air_damage_2_600`, `100_air_damage_5_600`, `100_air_damage_10_600` |
| `air_deploy_influence` | Air influence bonus | `100_air_deploy_influence_1_600`, `100_air_deploy_influence_3_600` |
| `air_deploy_rank_points` | Air rank points bonus | `100_air_deploy_rank_points_1_600`, `100_air_deploy_rank_points_3_600` |

**Booster ID naming convention:** `{industryId}_{type}_{quality}_{duration}` (e.g., `100_damage_10_7200` = +100% damage for 2 hours)

**Full booster item structure** (example):

```json
{
  "name": "+30% Ghost Booster",
  "id": "100_catchup_30_60",
  "industryId": 100,
  "quality": 30,
  "amount": 812,
  "activable": 1,
  "deactivable": 0,
  "activableFromInventory": 0,
  "activableFromBattlefield": 1,
  "activationData": {
    "tooltip": "Add 30% of the Best Damage per Hit in a Ground Battle to your Damage for 1 minute",
    "url": "/en/military/fight-activateBooster",
    "params": {
      "type": "catchup",
      "quality": 30,
      "duration": 60
    }
  },
  "active": 0,
  "icon": "https://www.erepublik.net/images/icons/boosters/52px/catchup.png?1702893011",
  "tooltip": "Add 30% of the Best Damage per Hit in a Ground Battle to your Damage for 1 minute",
  "token": "",
  "attributes": {
    "damageBoost": {
      "id": "damageBoost",
      "name": "Add 30% of the Best Damage per Hit in a Ground Battle to your Damage for 1 minute",
      "type": "use",
      "value": "30%"
    },
    "duration": {
      "id": "duration",
      "name": "Duration",
      "type": "minute",
      "value": 1
    }
  },
  "isRaw": 0,
  "isPartial": 0,
  "isBooster": 1,
  "isBomb": 0,
  "isPackBooster": 0,
  "isExpirable": 0,
  "type": "catchup",
  "duration": 60,
  "canActivateBooster": 1
}
```

- `activationData.url` → POST to `/en/military/fight-activateBooster` with `type`, `quality`, `duration` params
- Speed boosters use `damageAcceleration` attribute (values like `"x2"`, `"x5"`, `"x9"`) instead of `damageBoost`

**Known boosters reference:**

| ID | Name | Effect |
|----|------|--------|
| `100_catchup_30_60` | +30% Ghost Booster | +30% of best damage for 1 min |
| `100_damage_5_86400` | +50% Ground Damage | +50% for 24h |
| `100_damage_5_28800` | +50% Ground Damage | +50% for 8h |
| `100_damage_5_7200` | +50% Ground Damage | +50% for 2h |
| `100_damage_10_86400` | +100% Ground Damage | +100% for 24h |
| `100_damage_10_28800` | +100% Ground Damage | +100% for 8h |
| `100_damage_10_7200` | +100% Ground Damage | +100% for 2h |
| `100_damage_10_3600` | +100% Ground Damage | +100% for 1h |
| `100_damage_10_600` | +100% Ground Damage | +100% for 10 min |
| `100_damage_15_600` | +150% Ground Damage | +150% for 10 min |
| `100_speed_1_180` | x2 Damage Accelerator | x2 speed for 3 min |
| `100_speed_2_600` | x5 Damage Accelerator | x5 speed for 10 min |
| `100_speed_2_300` | x5 Damage Accelerator | x5 speed for 5 min |
| `100_speed_3_600` | x9 Damage Accelerator | x9 speed for 10 min |
| `100_prestige_points_1_600` | +1 Prestige Points | +1 prestige for 10 min |
| `100_deploy_size_100_600` | +100% Deploy Size | +100% for 10 min |
| `100_deploy_size_200_1200` | +200% Deploy Size | +200% for 20 min |
| `100_deploy_size_200_600` | +200% Deploy Size | +200% for 10 min |
| `100_deploy_size_500_600` | +500% Deploy Size | +500% for 10 min |
| `100_ground_deploy_influence_1_600` | +10% Ground Influence | +10% for 10 min |
| `100_ground_deploy_influence_2_1200` | +20% Ground Influence | +20% for 20 min |
| `100_ground_deploy_influence_2_600` | +20% Ground Influence | +20% for 10 min |
| `100_ground_deploy_influence_3_600` | +30% Ground Influence | +30% for 10 min |
| `100_ground_deploy_rank_points_1_600` | +10% Ground Rank Points | +10% for 10 min |
| `100_ground_deploy_rank_points_2_1200` | +20% Ground Rank Points | +20% for 20 min |
| `100_ground_deploy_rank_points_2_600` | +20% Ground Rank Points | +20% for 10 min |
| `100_ground_deploy_rank_points_3_600` | +30% Ground Rank Points | +30% for 10 min |
| `100_air_damage_2_600` | +20% Air Damage | +20% for 10 min |
| `100_air_damage_5_600` | +50% Air Damage | +50% for 10 min |
| `100_air_damage_5_300` | +50% Air Damage | +50% for 5 min |
| `100_air_damage_10_600` | +100% Air Damage | +100% for 10 min |
| `100_air_deploy_influence_1_600` | +10% Air Influence | +10% for 10 min |
| `100_air_deploy_influence_2_1200` | +20% Air Influence | +20% for 20 min |
| `100_air_deploy_influence_2_600` | +20% Air Influence | +20% for 10 min |
| `100_air_deploy_influence_3_600` | +30% Air Influence | +30% for 10 min |
| `100_air_deploy_rank_points_1_600` | +10% Air Rank Points | +10% for 10 min |
| `100_air_deploy_rank_points_2_1200` | +20% Air Rank Points | +20% for 20 min |
| `100_air_deploy_rank_points_2_600` | +20% Air Rank Points | +20% for 10 min |
| `100_air_deploy_rank_points_3_600` | +30% Air Rank Points | +30% for 10 min |

#### Rounds

The `rounds` object contains entries keyed by `battleZoneId`. Each battle has 5 zones per round (Div I–IV + Aircraft), so a 12-round battle has 60 entries:

```json
{
  "rounds": {
    "37214695": {
      "id": 37214695,
      "zone": 12,
      "round": 12,
      "division": 11,
      "created_at": 1765936745,
      "type": "aircraft",
      "terrain_type_id": null,
      "started": 1,
      "finished": 1
    },
    "37214691": {
      "id": 37214691,
      "zone": 12,
      "round": 12,
      "division": 1,
      "created_at": 1765936745,
      "type": "tanks",
      "terrain_type_id": null,
      "started": 1,
      "finished": 1
    }
  }
}
```

**Division mapping:** `1` = Div I, `2` = Div II, `3` = Div III, `4` = Div IV, `11` = Aircraft

**Type values:** `"tanks"` for ground divisions, `"aircraft"` for air division

**Terrain types observed:**
- `terrain_type_id: 3` — ground divisions in round 1
- `terrain_type_id: 0` — aircraft in round 1
- `terrain_type_id: null` — all subsequent rounds

#### Travel Data

```json
{
  "travel": {
    "travelData": {
      "travelRequired": false,
      "isCivilWar": null,
      "isResistance": null,
      "battleFinished": true,
      "travelData": [],
      "allDestinations": {
        "countries": [],
        "regions": []
      }
    },
    "battleZones": [
      {
        "label": "aircraft",
        "name": "Aircraft",
        "division": 11,
        "zoneId": 12,
        "battleZoneId": 37214695,
        "selected": false,
        "disabled": false,
        "terrain": { "id": 0, "codename": "aircraft", "name": "Aircraft" }
      },
      {
        "label": "division_i",
        "name": "Division I",
        "division": 1,
        "zoneId": 12,
        "battleZoneId": 37214691,
        "selected": false,
        "disabled": false,
        "terrain": { "id": 0, "codename": "standard", "name": "Standard" }
      },
      {
        "label": "division_iv",
        "name": "Division IV",
        "division": 4,
        "zoneId": 12,
        "battleZoneId": 37214694,
        "selected": true,
        "disabled": false,
        "terrain": { "id": 0, "codename": "standard", "name": "Standard" }
      }
    ],
    "travelMethod": "preferCurrency"
  }
}
```

- `battleZones` lists all available divisions with their `battleZoneId` and terrain info
- `selected: true` indicates the division the player is currently viewing
- `travelMethod` options: `"preferCurrency"`

#### Battle Effects

```json
{
  "battle_effects": {
    "valentinesDay": { "is_active": 0 },
    "snowFight": { "is_active": 0 },
    "heroRewardEffect": null
  }
}
```

#### Countries Map

A lookup table mapping country IDs to names (74 entries). Truncated:

```json
{
  "countries": {
    "1": "Romania",
    "9": "Brazil",
    "10": "Italy",
    "11": "France",
    "12": "Germany",
    "13": "Hungary",
    "14": "China",
    "15": "Spain",
    "23": "Canada",
    "24": "USA",
    "29": "United-Kingdom",
    "35": "Poland",
    "38": "Sweden",
    "41": "Russia",
    "71": "Latvia",
    "..."
  }
}
```

#### Texts Map

Localized UI strings (56 keys). Used for battlefield UI labels:

```json
{
  "texts": {
    "consume_food": "Consume Food",
    "cosume_energy_bar_text": "Consume Energy Bar",
    "cosume_winter_treat_text": "Consume Treat",
    "buy_health_kit": "Get more energy",
    "won_zone": "won the previous battle",
    "division": "Division",
    "the_airforce": "The Airforce",
    "won_division": "won this battle",
    "won_battle": "won this campaign",
    "yourHealth": "Your Energy:",
    "opponentHealth": "Opponent Energy:",
    "defenderHero": "Our battle hero",
    "attackerHero": "...",
    "conqueredText": "%%1%% CONQUERED %%2%%",
    "defendedText": "...",
    "..."
  }
}
```

- Template placeholders: `%%1%%`, `%%2%%` are used for variable substitution

#### Objectives & Missions

```json
{
  "hasActiveObjective": false,
  "hasDestroyedObjective": false,
  "canDeployObjective": false,
  "tutorial": 0,
  "mission1Completed": true,
  "mission2Completed": true,
  "mission3Completed": true
}
```

#### Other Fields

```json
{
  "deployment": null,
  "aoe_effects": [],
  "aoeMyDivision": true,
  "aoeOtherDivisionData": [],
  "fightersData": [],
  "historyStats": [],
  "culture": "en",
  "csrfToken": "99e749d8bab079654efe0b67c298b9a2",
  "isLoggedIn": true,
  "ERPK_ECONOMY_HOST": "economy.erepublik.com",
  "ERPK_MAIN_HOST": "www.erepublik.com",
  "extras": "winter treats",
  "webDeployEnabled": true,
  "battleDetermination": 1,
  "fightSessionKey": "",
  "fighterCountryId": 72,
  "oldEnemyHealth": 51,
  "remainingHospitals": 0
}
```

- `deployment` — `null` for finished battles; likely populated during active combat with deployment state
- `fightersData`, `historyStats` — empty arrays in finished battles; populated during active rounds
- `aoe_effects` — Area of Effect active effects (empty when battle is over)
- `battleDetermination` — `1` when battle determination is active (affects campaign point calculation), `0` otherwise
- `fightSessionKey` — session key for fight actions; empty string when not actively fighting
- `fighterCountryId` — country ID the player is currently fighting for
- `oldEnemyHealth` — previous opponent's health value in PvP combat
- `remainingHospitals` — number of hospital items remaining for the player

### Response Fields Reference

| Field | Type | Description |
|-------|------|-------------|
| `battleId` | number | Campaign/battle identifier |
| `battleZoneId` | number | Current battle zone (division) being viewed |
| `countryId` | number | Country ID of the viewer's citizenship |
| `invaderId` | number | Attacking country ID |
| `defenderId` | number | Defending country ID |
| `realInvaderId` / `realDefenderId` | number | Actual countries (differs from `invaderId`/`defenderId` in liberation wars) |
| `leftBattleId` / `rightBattleId` | number | Country IDs for left/right sides of the UI |
| `currentRoundNumber` | number | Current round number (1-based) |
| `battleFinished` | number | `1` if battle is over, `0` if active |
| `isResistance` | boolean | True if this is a resistance war |
| `isCivilWar` | boolean | True if this is a civil war |
| `isLiberation` | boolean | True if this is a liberation war |
| `isDictatorship` | boolean | True if dictatorship battle |
| `isMultiZone` | boolean | True if battle has multiple divisions |
| `mustInvert` | boolean | Whether to invert the left/right display |
| `spectatorOnly` | boolean | True if the player can only watch (e.g., finished battle) |
| `onAirforceBattlefield` | boolean | True if viewing the aircraft division |
| `battleZoneSituation` | object | Map of `battleZoneId` → situation code (`0` = finished) |
| `activeBattleZones` | array | Currently active zone IDs (empty when finished) |
| `campaignPointsThreshold` | number | Points needed to win the campaign (typically 83 or 150) |
| `points` | object | `{ defender: number, attacker: number }` — campaign points |
| `campaignHeroId` | object | Country ID → citizen ID of campaign hero |
| `battle_start_at` | Date | When the battle started |
| `battle_critical_at` | Date | When the battle becomes critical |
| `server_time` | Date | Current server time |
| `zoneElapsedTime` | string | Format `"hours:minutes:seconds"` since zone started |
| `citizenId` | number | Logged-in player's citizen ID |
| `division` | number | Player's natural division (1–4) |
| `currentDivision` | number | Division currently being viewed |
| `health` | number | Player's current energy |
| `level` | number | Player's experience level |
| `currentWeaponId` | number | Selected weapon ID (`-1` = none) |
| `hasBazookaAmmo` | number | Bazooka ammo count (`-1` = none) |
| `legendData` | object | Per-country legend progression (tanks/aircraft levels) |
| `specialWeapons` | object | Available bombs/missiles keyed by `{industryId}_{quality}` |
| `boosters` | object | Active and inactive boosters by category |
| `rounds` | object | All rounds keyed by `battleZoneId` with division/type/terrain info |
| `travel` | object | Travel data, battle zones list, and travel method |
| `battle_effects` | object | Active seasonal effects (valentinesDay, snowFight, heroRewardEffect) |
| `countries` | object | Country ID → name lookup map (74 entries) |
| `texts` | object | Localized UI strings (56 keys) with `%%N%%` placeholders |
| `deployment` | object/null | Active deployment state (`null` when no deployment) |
| `webDeployEnabled` | boolean | Whether web-based deployment is enabled |
| `battleDetermination` | number | `1` if battle determination is active, `0` otherwise |
| `fightSessionKey` | string | Session key for fight actions (empty when not fighting) |
| `fighterCountryId` | number | Country ID the player is fighting for |
| `oldEnemyHealth` | number | Previous opponent's health in PvP combat |
| `remainingHospitals` | number | Remaining hospital items for the player |

### Data Extraction

To extract the `BATTLE_SERVER_DATA` JSON from the HTML response:

**Python example:**

```python
import re
import json

# 1. Extract the raw JS object
match = re.search(
    r'id="BATTLE_SERVER_DATA".*?SERVER_DATA = \$j\.extend\(SERVER_DATA, (\{.*?\})\)',
    html_content,
    re.DOTALL
)
raw_js = match.group(1)

# 2. Convert new Date('...') calls to JSON strings
json_str = re.sub(r"new Date\('([^']+)'\)", r'"\1"', raw_js)

# 3. Parse as JSON
data = json.loads(json_str)
```

### Notes

- **HTML page, not JSON API**: This endpoint returns a full HTML document (~221 KB). The battle state is embedded in JavaScript, not returned as JSON. You must parse the HTML to extract the data.
- **Two `SERVER_DATA` blocks**: The page contains two `<script>` blocks that populate `SERVER_DATA`:
  1. Line ~49: Generic session data (`csrfToken`, `isLoggedIn`, `serverTime`, `fbAppId`, etc.)
  2. `<script id="BATTLE_SERVER_DATA">` (line ~1311): Battle-specific state merged via `$j.extend(SERVER_DATA, {...})`
- **Division numbering**: Ground divisions use `1`–`4` (Div I–IV), aircraft uses `11`
- **`spectatorOnly: true`** appears when the battle is finished — the player can only view the results
- **Empty arrays in finished battles**: `activeBattleZones`, `fightersData`, `historyStats`, `aoe_effects`, `aoeOtherDivisionData` are all empty when the battle is over. They are populated during active combat rounds.
- **`deployment` is `null`** in finished battles — this field contains active deployment state during combat
- **Booster activation endpoint**: `POST /en/military/fight-activateBooster` with params `type`, `quality`, `duration` (discovered from `activationData`)
- **Bomb deployment endpoint**: `POST /en/military/deploy-bomb` with param `bombId` (discovered from `specialWeapons.activationData`)
- **Booster ID naming convention**: `{industryId}_{type}_{quality}_{duration}` (e.g., `100_damage_10_7200`)
- **Date fields**: `battle_start_at`, `battle_critical_at`, `server_time` use `new Date('...')` in the raw JavaScript — these must be converted to strings before JSON parsing
- **Text placeholders**: The `texts` map uses `%%1%%`, `%%2%%` for variable substitution (e.g., `"%%1%% CONQUERED %%2%%"`)
- **`terrain_type_id`**: Observed values: `0` (aircraft round 1), `3` (ground round 1 — one battle), `4` (ground round 1 — another battle), `null` (subsequent rounds). Different terrain types may represent different battlefield terrain bonuses.
- **Active vs Finished battle differences**:
  - `activeBattleZones` — empty array when finished; populated with objects `{ battleZoneId, zoneId, division, type, terrainTypeId, sector }` during active battles
  - `battleFinished` — `0` for active, `1` for finished
  - `spectatorOnly` — `false` for active (player can fight), `true` for finished
  - `deployment` — `null` when not deploying; contains `{ myTotalDamage, isMaxLevel, rankText, rankIcon, currentRankPoints, nextRankPoints, rankPercentProgress }` when deployment UI is shown
  - `fightersData`, `historyStats` — populated during active rounds
  - `battleDetermination` — present in both active and finished battles; `1` when determination is active
  - `fightSessionKey` — empty string when not in an active fight session; populated with a session key during combat
  - `fighterCountryId` — the country ID the player chose to fight for; present in both active and finished battles
  - `oldEnemyHealth` — PvP opponent's previous health; may be `0` in finished battles
  - `remainingHospitals` — hospital count available at current location; present in both active and finished battles
- **Player strength**: Available via `erepublik.promos.April1st2017.playerStrength` (e.g., `414481.469`) — the "April 1st 2017" promo is permanently active and carries the player's current strength value
- **Pomelo WebSocket**: Real-time battle updates via `gate.erepublik.com:3050` with auth token = session token (`erpk` cookie value). See `erepublik.settings.pomelo` in the page's global object.

### Additional Embedded Data Objects

Beyond `BATTLE_SERVER_DATA`, the battlefield page embeds several other useful data objects:

#### `erepublik.citizen` (line ~47)

Full citizen state including identity, economy, party, residence, daily orders, terrain skills (43 keys):

```json
{
  "citizenId": 4690052,
  "isOrganization": 0,
  "isAdult": true,
  "isNewCitizen": false,
  "country": 72,
  "citizenshipCountryId": 72,
  "name": "driver sti",
  "avatar": "https://cdnt.erepublik.net/enQ9lXJMVlMaSYRgyEsa10ndQwA=/55x55/smart/avatars/Citizens/2011/04/12/1907aee62f42c11beba21159840cdc16.png?fa70b874e2f337186b979af9404828eb",
  "currency": "LTL",
  "citizenshipCurrencyName": "LTL",
  "isEmpire": false,
  "isDictatorship": 0,
  "countryLocationId": 72,
  "regionLocationId": 663,
  "countryLocationName": "Lithuania",
  "countryLocationPermalink": "Lithuania",
  "residence": {
    "hasResidence": 1,
    "cityId": 710,
    "regionId": 663,
    "countryId": 72,
    "bonuses": {
      "has_residence": 1,
      "is_in_residence": 1,
      "valid_until": 0,
      "time_remaining": 0,
      "num_houses": 1,
      "energy_bonus": 100,
      "recovery_bonus": 4,
      "description": "Residence bonuses available"
    }
  },
  "division": 4,
  "muId": 893,
  "muCountryId": 72,
  "regimentId": 1390,
  "party": {
    "id": 3773,
    "party_id": 3773,
    "is_party_president": 0,
    "name": "Lietuvos Tevynes Sajunga",
    "economical_orientation": "Center",
    "stripped_title": "lietuvos-tevynes-sajunga",
    "has_avatar": 1,
    "created_at": 1315032136,
    "joined_at": 1681043826
  },
  "energy": 900,
  "energyToRecover": 10220,
  "energyFromFoodRemaining": 0,
  "energyPerInterval": 64,
  "hasFoodInInventory": true,
  "gold": 75678,
  "currencyAmount": 7120147,
  "userLevel": 1725,
  "hasCoreLoopAccess": false,
  "canWorkTrainAgainIn": 70214,
  "currentExperiencePoints": 8503402,
  "dailyOrders": [
    {
      "battleId": 869119,
      "sideCountryId": 72,
      "regionId": 372,
      "title": "Fight for Lithuania in Jiangsu",
      "battleLink": "//www.erepublik.com/en/military/battlefield-choose-side/869119/72",
      "setBy": {
        "name": "priorities",
        "id": 0,
        "role": "Military Unit's priorities",
        "description": "Set by Military Unit's priorities"
      },
      "required": 25,
      "progress": 0,
      "completed": false,
      "isCivilWar": false
    }
  ],
  "dailiesToCollect": 0,
  "canChangeMPPPriorities": false,
  "canChangeTerrainPriorities": false,
  "canSwitchDivisions": true,
  "terrainSkills": {
    "1": { "terrain_id": 1, "skill_points": 40000 },
    "2": { "terrain_id": 2, "skill_points": 33333 },
    "3": { "terrain_id": 3, "skill_points": 17728 },
    "4": { "terrain_id": 4, "skill_points": 0 },
    "5": { "terrain_id": 5, "skill_points": 33333 },
    "6": { "terrain_id": 6, "skill_points": 40000 },
    "7": { "terrain_id": 7, "skill_points": 0 },
    "8": { "terrain_id": 8, "skill_points": 0 },
    "9": { "terrain_id": 9, "skill_points": 33333 },
    "10": { "terrain_id": 10, "skill_points": 33333 },
    "11": { "terrain_id": 11, "skill_points": 0 },
    "12": { "terrain_id": 12, "skill_points": 0 },
    "13": { "terrain_id": 13, "skill_points": 0 },
    "14": { "terrain_id": 14, "skill_points": 0 }
  },
  "isTopCustomer": true,
  "loyaltyPoints": 2620,
  "boxKeys": { "cupid_key": 223 }
}
```

**New fields (compared to earlier captures):**

| Field | Type | Description |
|-------|------|-------------|
| `avatar` | string | Citizen avatar URL (CDN, with image proxy path) |
| `isOrganization` | number | `0` for regular citizen, `1` for organization account |
| `isAdult` | boolean | Whether the citizen is an adult account |
| `isNewCitizen` | boolean | Whether the citizen is considered "new" |
| `isEmpire` | boolean | Whether the citizen's country is currently an empire |
| `isDictatorship` | number | `0` or `1` — whether the country is under dictatorship |
| `citizenshipCurrencyName` | string | Currency name for citizenship country (e.g., `"LTL"`) |
| `countryLocationId` | number | Country ID where the citizen is currently located |
| `countryLocationName` | string | Display name of current country location |
| `countryLocationPermalink` | string | URL-friendly country name (e.g., `"Lithuania"`) |
| `regionLocationId` | number | Region ID of current location |
| `muCountryId` | number | Country ID of the citizen's military unit |
| `currentExperiencePoints` | number | Current XP total |
| `canWorkTrainAgainIn` | number | Seconds until next work/train action is available |
| `dailiesToCollect` | number | Number of uncollected daily rewards |
| `hasCoreLoopAccess` | boolean | Whether citizen has access to the core loop feature |
| `canChangeMPPPriorities` | boolean | Can change MPP (Mutual Protection Pact) priorities |
| `canChangeTerrainPriorities` | boolean | Can change terrain priorities |
| `party` | object | Full party membership info (see below) |

**`party` sub-object:**

| Field | Type | Description |
|-------|------|-------------|
| `id` / `party_id` | number | Party ID (duplicated) |
| `is_party_president` | number | `0` or `1` — whether citizen is party president |
| `name` | string | Party display name |
| `economical_orientation` | string | Political orientation (e.g., `"Center"`) |
| `stripped_title` | string | URL-safe slug of the party name |
| `has_avatar` | number | `0` or `1` — whether party has a custom avatar |
| `created_at` | number | Unix timestamp of party creation |
| `joined_at` | number | Unix timestamp of when the citizen joined |

**Updated `dailyOrders[]` fields:**

| Field | Type | Description |
|-------|------|-------------|
| `battleLink` | string | Protocol-relative URL to the battlefield choose-side page |
| `setBy` | object | Who set the order: `{ name, id, role, description }` |
| `isCivilWar` | boolean | Whether the battle is a civil war |

**Updated `residence.bonuses` fields:**

| Field | Type | Description |
|-------|------|-------------|
| `valid_until` | number | Expiry timestamp (`0` = permanent/no expiry) |
| `time_remaining` | number | Seconds remaining until bonuses expire (`0` = permanent) |
| `num_houses` | number | Number of houses the citizen owns |
| `description` | string | Human-readable description (e.g., `"Residence bonuses available"`) |

**`terrainSkills`** — expanded from 2 to 14 terrain entries (IDs 1–14), reflecting the full terrain system. Each entry: `{ terrain_id: number, skill_points: number }`.

#### `energyData` (line ~272)

```json
{
  "energy": 772,
  "energyPoolLimit": 10220,
  "recoverableEnergy": 0,
  "energyPerInterval": 64,
  "energyStatus": "recovering"
}
```

#### `globalNS.userInfo` (line ~1069)

```javascript
globalNS.userInfo.wellness = 772;
globalNS.userInfo.specialFoodAmount = 71353;  // treats count
globalNS.userInfo.specialFoodValue = 100;     // energy per treat
globalNS.userInfo.energyPerInterval = 64;
```

#### Deploy Rank Data (line ~2653)

```json
{
  "deploy": {
    "myTotalDamage": 0,
    "isMaxLevel": false,
    "rankText": "Air Commodore *****",
    "rankIcon": "//www.erepublik.net/images/modules/ranks/aircraft/32px/061_air_commodore.png",
    "currentRankPoints": 59284166,
    "nextRankPoints": 76360000,
    "rankPercentProgress": 3.088
  }
}
```

---

## Battlefield AJAX Endpoints

The following endpoints are called by the battlefield page's JavaScript (AngularJS controllers and jQuery) to perform actions during combat. All require the `erpk` session cookie and most require the CSRF token from `SERVER_DATA.csrfToken`.

### Common Headers for All AJAX Endpoints

| Header | Value | Required |
|--------|-------|----------|
| `Cookie` | `erpk=YOUR_SESSION_TOKEN` | Yes |
| `Content-Type` | `application/x-www-form-urlencoded` | Yes (POST) |
| `X-Requested-With` | `XMLHttpRequest` | Recommended |
| `Referer` | `https://www.erepublik.com/en/military/battlefield/{battleId}` | Recommended |

---

### Battle Console (Get Live Battle State)

**Method:** POST
**URL:** `/en/military/battle-console`
**Auth Required:** Yes

#### Description

Fetches real-time battle zone state including live stats (domination bar), fighter rankings, combat orders, war history, and allied forces. This is the **primary polling endpoint** for battlefield UI updates. Called by `BattleStatsController`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `zoneId` | number | Yes | The zone (round) number |
| `battleZoneId` | number | Yes | The specific battle zone ID (division) |
| `action` | string | Yes | Action type: `"battleStatistics"`, `"fighterStatistics"`, `"warHistory"`, `"combatOrders"` |
| `_token` | string | Yes | CSRF token from `SERVER_DATA.csrfToken` |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/battle-console' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&zoneId=8&battleZoneId=38158390&action=battleStatistics&_token=YOUR_CSRF_TOKEN'
```

#### Notes

- `action=battleStatistics` — returns live domination stats, wall info, allied forces
- `action=fighterStatistics` — returns top fighters on each side with damage rankings
- `action=combatOrders` — returns active combat orders (bounties) for each side
- `action=warHistory` — returns previous rounds' results

---

### Deploy Bomb

**Method:** POST
**URL:** `/en/military/deploy-bomb`
**Auth Required:** Yes

#### Description

Deploys a special weapon (Big Bomb, Small Bomb, or Cruise Missile) in the current battle zone. Called from `hit_bomb()` in `battle_new.js`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `bombId` | number | Yes | The bomb type ID: `21` (Small Bomb), `22` (Big Bomb), `215` (Cruise Missile) |
| `_token` | string | Yes | CSRF token |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/deploy-bomb' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&battleZoneId=38158390&_token=YOUR_CSRF_TOKEN&bombId=22'
```

#### Notes

- Bomb damage: Big Bomb = 5,000,000; Small Bomb = 1,500,000; Cruise Missile = 1,500,000
- Can be disabled server-side: check `SERVER_DATA.bigBombsDisabled` and `SERVER_DATA.cruiseMissilesDisabled`
- Response is JSON — check for `error` field

---

### Activate Booster

**Method:** POST
**URL:** `/en/military/fight-activateBooster`
**Auth Required:** Yes

#### Description

Activates a combat booster (speed accelerator, damage boost, prestige points, deploy size, influence, rank points). Called by `SpecialItemsController`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `type` | string | Yes | Booster type: `speed`, `damage`, `catchup`, `prestige_points`, `deploy_size`, `air_damage`, `ground_deploy_influence`, `air_deploy_influence`, `ground_deploy_rank_points`, `air_deploy_rank_points` |
| `quality` | number | Yes | Booster quality tier (e.g., `1`, `2`, `3`, `5`, `10`, `100`, `200`, `500`) |
| `duration` | number | Yes | Duration in seconds (e.g., `60`, `180`, `300`, `600`, `1200`, `3600`, `7200`, `28800`, `86400`) |
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `sideId` | number | Yes | Country ID of the side you're fighting for (`SERVER_DATA.mySideCountryId`) |
| `_token` | string | Yes | CSRF token |
| `confirmExtendBooster` | string | No | Set to `"yes"` when extending/replacing an active booster of the same type |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/fight-activateBooster' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'type=speed&quality=2&duration=600&battleId=869119&battleZoneId=38158390&sideId=72&_token=YOUR_CSRF_TOKEN'
```

#### Response

```json
{
  "status": "succes",
  "message": "Booster activated"
}
```

#### Notes

- Success status is `"succes"` (note the typo — single 's')
- If a booster of the same type is already active, the UI prompts for confirmation and resends with `confirmExtendBooster=yes`
- Available boosters and their params are listed in `SERVER_DATA.boosters.inactive`
- Each booster's `canActivateBooster` flag indicates if it can currently be activated

---

### Battlefield Travel (Choose Side & Travel)

**Method:** POST
**URL:** `/en/main/battlefieldTravel`
**Auth Required:** Yes

#### Description

Handles choosing a side in the battle and traveling to the region where the fight takes place. Called by `BattleSetupController`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `sideCountryId` | number | Yes | Country ID to fight for |
| `battleZoneId` | number | Yes | Target battle zone ID |
| `_token` | string | Yes | CSRF token |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/battlefieldTravel' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&sideCountryId=72&battleZoneId=38158390&_token=YOUR_CSRF_TOKEN'
```

#### Notes

- Travel costs are visible in `SERVER_DATA.travel.travelData.allDestinations.regions[regionId].cost`
- Can use travel tickets instead of currency — check `ticket` and `ticketAmount` fields
- If `travelRequired` is `false`, the player is already in a valid region
- The `travelMethod` field indicates preference: `"preferCurrency"` or ticket-based

---

### Switch Division

**Method:** POST
**URL:** `/en/main/switch-division`
**Auth Required:** Yes

#### Description

Switches the player to a different division (Div I–IV or Aircraft). Requires `canSwitchDivisions: true` in citizen data. Causes a page reload on success. Called from `battle_new.js`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `countryId` | number | Yes | Player's country ID (`SERVER_DATA.countryId`) |
| `zoneId` | number | Yes | Current zone/round number |
| `division` | number | Yes | Target division: `1`–`4` (ground) or `11` (aircraft) |
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `"activate"` |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/switch-division' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&countryId=72&zoneId=8&division=4&_token=YOUR_CSRF_TOKEN&action=activate'
```

#### Response

```json
{
  "error": false
}
```

#### Notes

- On success (`error: false`), the client reloads the page
- Only available when `SERVER_DATA.canSwitchDivisions` is `true`
- Division `11` = Aircraft

---

### PvP Queue (Join/Leave)

**Method:** POST
**URL:** `/en/main/pvp-queue`
**Auth Required:** Yes

#### Description

Joins or leaves the PvP (duel) queue in a battle zone. Minimum 100 energy required to join. Called from `battle_new.js`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `"join"` or `"leave"` |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/pvp-queue' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&battleZoneId=38158390&_token=YOUR_CSRF_TOKEN&action=join'
```

#### Response

```json
{
  "error": false,
  "message": "SUCCESS"
}
```

#### Notes

- Messages: `"SUCCESS"`, `"JOIN"` for successful queue join
- `pvpShowLock` in `SERVER_DATA` gates whether PvP is available
- Points threshold: PvP disabled when both sides exceed 1400 campaign points

---

### PvP Inventory

**Method:** GET
**URL:** `/en/military/pvp-inventory/`
**Auth Required:** Yes

#### Description

Returns an HTML fragment containing the player's PvP-related inventory. Rendered inline in the battlefield UI.

#### Notes

- Returns **HTML**, not JSON
- Used to display weapons/items available during PvP duels

---

### Get Deployment Inventory

**Method:** POST
**URL:** `/en/military/fightDeploy-getInventory`
**Auth Required:** Yes

#### Description

Fetches the player's inventory for deployment — available weapons, vehicles, energy sources (food, energy bars, treats), fuel, and deployment configuration limits. Called by `DeploymentController` when opening the deploy panel.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `sideCountryId` | number | Yes | Country ID to fight for |
| `_token` | string | Yes | CSRF token |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-getInventory' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&battleZoneId=38158390&sideCountryId=72&_token=YOUR_CSRF_TOKEN'
```

#### Expected Response Structure

```json
{
  "energySources": [
    { "type": "food", "quality": 1, "amount": 500 },
    { "type": "energy_bar", "quality": 1, "amount": 200 },
    { "type": "winter_treat", "quality": 1, "amount": 100 }
  ],
  "weapons": [...],
  "vehicles": [...],
  "minEnergy": 0,
  "maxEnergy": 10000,
  "fuel": { "amount": 500 }
}
```

#### Notes

- Response includes `energySources` array grouped by type
- `minEnergy`/`maxEnergy` define the deployment energy slider range
- Weapons and vehicles include quality, damage bonuses, and selection state

---

### Start Deployment

**Method:** POST
**URL:** `/en/military/fightDeploy-startDeploy`
**Auth Required:** Yes

#### Description

Starts an automated deployment that gradually consumes resources (food, energy bars, fuel) to deal damage over time. While deployed, the player **cannot** travel, eat, fight manually, or use bombs — but **can** activate boosters. Called by `DeploymentController.startDeploy()`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `sideCountryId` | number | Yes | Country ID to fight for |
| `energyUsed` | number | Yes | Total energy to deploy |
| `weaponQuality` | number | Yes | Weapon quality to use (0 = bare hands) |
| `vehicleId` | number | No | Vehicle/protector to use |
| `_token` | string | Yes | CSRF token |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-startDeploy' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&battleZoneId=38158390&sideCountryId=72&energyUsed=5000&weaponQuality=7&_token=YOUR_CSRF_TOKEN'
```

#### Notes

- Only one deployment can be active at a time (check `SERVER_DATA.deployment`)
- Level-up energy is automatically added and consumed during deployment
- Deploy estimates (damage, kills, time) are calculated client-side
- Boosters like `deploy_size` (+100%, +200%, +500%) increase the effective deployment size

---

### Cancel Deployment

**Method:** POST
**URL:** `/en/military/fightDeploy-cancelDeploy`
**Auth Required:** Yes

#### Description

Cancels an in-progress deployment. Called when the player clicks the cancel button on the deploy panel.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `_token` | string | Yes | CSRF token |

#### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-cancelDeploy' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&_token=YOUR_CSRF_TOKEN'
```

---

### Deployment Report Popup

**Method:** GET (popup)
**URL:** `/en/military/fightDeploy-deployReportPopup`
**Auth Required:** Yes

#### Description

Returns an HTML popup fragment with the completed deployment's results (damage dealt, kills, resources consumed). Shown after deployment finishes.

---

### Activate Battle Effect

**Method:** POST
**URL:** `/en/main/fight-activateBattleEffect`
**Auth Required:** Yes

#### Description

Activates a special battle effect (e.g., snow fight, Valentine's Day). Called by `SnowFightFactory`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `citizenId` | number | Yes | Player's citizen ID |
| `battleId` | number | Yes | The battle/campaign ID |
| `type` | string | Yes | Effect type (e.g., `"snowFight"`, `"valentinesDay"`) |
| `_token` | string | Yes | CSRF token |

---

### List Battle Objectives

**Method:** GET
**URL:** `/en/main/fight-listObjectives`
**Auth Required:** Yes

#### Description

Returns the list of battle objectives (special destructible targets). Called by `ObjectivesFactory`.

---

### Battle Stats

**Method:** POST
**URL:** `/en/military/battle-stats/{battleId}`
**Auth Required:** Yes

#### Description

Returns battle statistics data. The `{battleId}` is appended to the URL path.

---

### Energy Refill - Get Inventory

**Method:** POST
**URL:** `/en/economy/energyRefill-getInventory`
**Auth Required:** Yes

#### Description

Fetches the player's food and energy bar inventory for the energy refill popup. Called by `EnergyPopupFactory`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |

---

### Refill Energy (Eat Food / Use Energy Bar)

**Method:** POST
**URL:** `/en/economy/refillEnergy`
**Auth Required:** Yes

#### Description

Consumes food or energy bars to restore energy. Called by `EnergyPopupFactory.recoverEnergy()`.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| *(additional params)* | | | Type and quality of food/energy bar to consume |

#### Notes

- Food consumption has daily limits (`energyFromFoodRemaining`)
- Energy bars bypass the food limit
- Special food (treats) restore 50 energy per use, `specialFoodAmount` tracked separately
- Energy pool limit is `energyPoolLimit` (e.g., 10220)
- `energyPerInterval` is the passive recovery rate

---

### Mercenary Campaign Subscribe/Unsubscribe

**Method:** POST
**URL:** `/en/main/mercenary-campaign/subscribe/`
**URL:** `/en/main/mercenary-campaign/unsubscribe/`
**Auth Required:** Yes

#### Description

Subscribe to or unsubscribe from a mercenary campaign (combat order).

---

### Battle Map Status

**Method:** POST
**URL:** `/en/military/battle-map-status/{battleId}`
**Auth Required:** Yes

#### Description

Returns the campaign map status showing which divisions each side has won. Called by `CampaignMapController`.

---

## Pomelo WebSocket (Real-time Battle Updates)

**Gateway:** `gate.erepublik.com:3050`
**Auth Token:** Same as `erpk` session cookie value

### Description

The battlefield page establishes a WebSocket connection via the Pomelo framework for real-time updates. This provides:

- Live hit notifications (who hit whom, damage dealt)
- Domination bar updates
- Battle hero changes
- Zone won/lost events
- Deployment progress updates

### Configuration (from page source)

```json
{
  "pomelo": {
    "gate": "gate.erepublik.com",
    "port": 3050,
    "authToken": "YOUR_SESSION_TOKEN"
  }
}
```

### JavaScript Bundle

The Pomelo client is loaded from: `//www.erepublik.net/js/pomelo/lib/build/build.{timestamp}.js`

### Notes

- The Pomelo connection is what enables the live battlefield animation (players appearing, shooting, damage numbers)
- Without WebSocket, you'd need to poll `/en/military/battle-console` for updates
- The `authToken` matches the `erpk` cookie value

---

## Battlefield AngularJS Controllers Reference

The battlefield page uses these AngularJS controllers:

| Controller | Element ID | Purpose |
|------------|-----------|---------|
| `BattleStatsController` | `#BattleStatsController` | Live stats, fighter rankings, combat orders, war history |
| `BattlefieldPlayersController` | `#battleConsole` | Player avatars on the battlefield animation |
| `BattleSetupController` | `#battleFooterbattleSetup` | Side selection, travel, division switching |
| `DeploymentController` | `#battleFooterDeploy` | Deploy panel (inventory, energy slider, vehicle selection) |
| `SpecialItemsController` | `#special_weapons_list_area` | Boosters, bombs, special weapons |
| `ErpkBattleFieldChangeSides` | `.switch_region_area` | Division/side switching button |
| `CampaignMapController` | `#CampaignMapController` | Campaign map overlay |
| `ValentinesController` | `#ValentinesController` | Seasonal Valentine's Day events |
| `SideNotificationController` | `#SideNotificationController` | Side notifications (achievements, rewards) |
| `CitizenAchievementsController` | `#CitizenAchievementsController` | Achievement popups |

---

## JavaScript Bundles Reference

Key JS files loaded on the battlefield page:

| File | Size | Purpose |
|------|------|---------|
| `battle_new.{ts}.js` | ~86 KB | Core battle logic: PvP, bomb deployment, division switching, food inventory |
| `erepublik.{ts}.js` | ~411 KB | Main framework: popups, general UI, page routing |
| `erpk_angular_controllers.{ts}.js` | ~391 KB | All AngularJS controllers (battle stats, deploy, boosters, economy) |
| `erpk_angular_services.{ts}.js` | ~24 KB | Angular services and factories |
| `pomelo/lib/build/build.{ts}.js` | — | Pomelo WebSocket client for real-time updates |
| `pvp/inventory.{ts}.js` | — | PvP inventory management |

> `{ts}` = build timestamp (e.g., `1770287129`)

---

## Battlefield Automation Summary

For fighting automation, the key endpoints and data flow are:

1. **Load battlefield page** → `GET /en/military/battlefield/{battleId}` → extract `SERVER_DATA` (CSRF token, battle zones, boosters, travel)
2. **Choose side & travel** (if needed) → `POST /en/main/battlefieldTravel`
3. **Switch division** (if needed) → `POST /en/main/switch-division`
4. **Check energy** → `energyData` from page or poll `globalNS.userInfo.wellness`
5. **Eat food / use energy bars** → `POST /en/economy/refillEnergy`
6. **Activate boosters** → `POST /en/military/fight-activateBooster`
7. **Deploy** → `POST /en/military/fightDeploy-startDeploy` (automated fighting)
8. **Deploy bombs** → `POST /en/military/deploy-bomb`
9. **Monitor progress** → WebSocket (Pomelo) or poll `POST /en/military/battle-console`
10. **Cancel deployment** → `POST /en/military/fightDeploy-cancelDeploy`

**Important values from `SERVER_DATA` for automation:**

| Value | Source | Use |
|-------|--------|-----|
| `csrfToken` | `SERVER_DATA.csrfToken` | Required `_token` param for all POST requests |
| `battleId` | `SERVER_DATA.battleId` | Battle identifier |
| `battleZoneId` | `SERVER_DATA.battleZoneId` | Current zone/division identifier |
| `mySideCountryId` | `SERVER_DATA.mySideCountryId` | Which side you're on |
| `health` | `SERVER_DATA.health` | Current energy |
| `travelRequired` | `SERVER_DATA.travelRequired` | Whether travel is needed |
| `canSwitchDivisions` | `SERVER_DATA.canSwitchDivisions` | Whether division switching is allowed |
| `boosters.inactive` | `SERVER_DATA.boosters.inactive` | Available boosters to activate |
| `specialWeapons` | `SERVER_DATA.specialWeapons` | Available bombs to deploy |
| `playerStrength` | `erepublik.promos.April1st2017.playerStrength` | Current strength value |

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
