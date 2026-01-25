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
