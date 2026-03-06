# Battle Information

#erepublik #api #military #battle-stats

[< Back to Military Module](README.md)

Endpoints for retrieving battle statistics, console data, and war history.

**Related:** [campaigns.md](campaigns.md) | [battlefield.md](battlefield.md) | [battlefield-ajax.md](battlefield-ajax.md) | [wars.md](wars.md)

---

## Get Battle Statistics

**Method:** GET
**URL:** `/en/military/battle-stats/{battleId}/{division}/{battleZoneId}`
**Auth Required:** Yes

### Description

Retrieves detailed statistics for a specific battle zone, including top damage dealers for each side in all divisions, overall stats, zone status, active battle effects, and fighter information. This endpoint is typically polled during active combat to update the battlefield UI.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| battleId | number | Yes | The ID of the battle |
| division | number | Yes | Division number (1-4 for ground, 11 for air) |
| battleZoneId | number | Yes | The specific battle zone ID |

### Headers

| Header           | Value                                            | Required    |
| ---------------- | ------------------------------------------------ | ----------- |
| Cookie           | `erpk=YOUR_SESSION_TOKEN`                        | Yes         |
| X-Requested-With | `XMLHttpRequest`                                 | Yes         |
| Accept           | `application/json, text/javascript, */*; q=0.01` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/military/battle-stats/863499/11/37859365' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01'
```

</details>

<details>
<summary>Example Response</summary>

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

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `stats.personal` | array | Personal stats for the authenticated citizen |
| `stats.current` | object | Current top damage stats by zone -> division -> country -> battle zone |
| `stats.current.*.*.*.*.top_damage` | array | Top damage dealers with citizen info, damage, kills, level |
| `stats.overall` | array | Overall battle statistics |
| `zone_finished` | boolean | Whether the current zone has finished |
| `division.created_at` | number | Unix timestamp when division round was created |
| `division.bar` | object | Map of battle zone ID -> country ID holding the wall |
| `division.domination` | object | Map of battle zone ID -> domination percentage |
| `division.defence_shield` | object | Map of battle zone ID -> shield value (`null` = no shield, `0` = shield inactive/depleted, positive number = shield active) |
| `division.{countryId}` | object | Per-country stats including domination and wins |
| `division.{countryId}.total` | number | Total points/wins for the country in this division |
| `logs.attackers` | array | Combat logs for attacking side |
| `logs.defenders` | array | Combat logs for defending side |
| `fightersData` | object | Map of citizen ID -> citizen details (name, avatar) |
| `fightersData.*.avatar` | string | CDN URL for citizen avatar (30x30) |
| `opponentsInQueue` | number | Number of opponents in queue (for direct fights) |
| `isInQueue` | boolean | Whether the citizen is currently in combat queue |
| `campaigns` | array | Related campaign data |
| `epicBattle` | number | Epic battle status (0 = not epic) |
| `activeEffects` | array | Currently active battle effects/boosters |
| `battleEffects` | object | Named battle effects keyed by event name (e.g., `valentinesDay`), each with `is_active` flag |
| `maxHit` | number | Maximum hit damage recorded |
| `most_contested` | array | Most contested zones info |
| `battle_zone_situation` | object | Map of battle zone ID -> situation status code |

### Notes

- **Real-time updates**: This endpoint is typically polled every few seconds during active combat to refresh the battlefield UI
- **Top damage structure**: `stats.current` is nested as `{zoneId -> divisionId -> countryId -> battleZoneId -> top_damage[]}`
- **Country IDs**: The numeric keys (e.g., "14", "72") represent country IDs for invaders and defenders
- **Division numbers**: 1-4 represent ground divisions, 11 represents the air division
- **Avatar CDN**: Avatar URLs use Cloudflare CDN with `smart` resizing and size parameters (30x30)
- **Domination values**: Can exceed 100, representing accumulated domination points
- **Defence shield**: `null` = no shield mechanic, `0` = shield inactive/depleted, positive number = shield active with remaining health
- **Battle effects**: `battleEffects` is an object keyed by event name (e.g., `valentinesDay`) with `is_active` flag (0/1) -- tracks seasonal/holiday combat modifiers
- **Zone situation codes**: The `battle_zone_situation` values likely indicate special states (0 = normal)

---

## Get Battle Statistics (Console)

**Method:** POST
**URL:** `/en/military/battle-console`
**Auth Required:** Yes

### Description

Retrieves paginated battle statistics for a specific battle, round, and division using POST parameters. Returns detailed round information across all divisions and top damage dealers (leaderboards) for each country side. This endpoint is used by the battle console UI to display fighter statistics, leaderboards, and round data with pagination support.

This is a **POST variant** of battle statistics that differs from the GET `/battle-stats` endpoint - it uses the `action=battleStatistics` parameter and provides paginated leaderboard data.

### Parameters

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

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/battle-console' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'battleId=863499&zoneId=7&action=battleStatistics&round=7&division=11&battleZoneId=37859365&type=damage&leftPage=1&rightPage=1&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

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

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `rounds` | object | Map of battle zone ID -> round details for all divisions |
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
| `{countryId}.fighterData` | object | Map of rank -> fighter details |
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

### Notes

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

## Get Battle Console Data

**Method:** POST
**URL:** `/en/military/battle-console`
**Auth Required:** Yes

### Description

Retrieves comprehensive battle state data including all divisions' status, wall positions, domination points, campaign points, and overall battle progress. This endpoint uses the `action=battleConsole` parameter and provides a high-level overview of the entire battle across all divisions, unlike the `battleStatistics` action which focuses on fighter leaderboards. This is typically used to render the battle console UI showing the current state of all divisions in a campaign.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| battleId | number | Yes | The ID of the battle |
| zoneId | number | Yes | The zone/round number |
| action | string | Yes | Must be "battleConsole" |
| battleZoneId | number | Yes | The specific battle zone ID |
| _token | string | Yes | CSRF token for security |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/battle-console' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'battleId=864000&zoneId=9&action=battleConsole&battleZoneId=37886839&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

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

</details>

### Response Fields

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
| `battle_time` | number | Seconds since campaign started. Always equals `serverTime - campaign_start_unix` -- does **not** freeze for finished battles |
| `serverTime` | number | Unix timestamp of the server time |

### Notes

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
- **`battle_time` is not frozen**: Live testing confirmed `battle_time` keeps incrementing for finished battles. It always equals `serverTime - campaign_start_unix`. Useful for deriving `battle_start_at` as a Unix timestamp: `start_unix = serverTime - battle_time`. See [Determining Battle Finish Time](wars.md#determining-battle-finish-time)

---

## Get War History (Previous Rounds)

**Method:** POST
**URL:** `/en/military/battle-console`
**Auth Required:** Yes

### Description

Returns campaign progress data used by the "War History" tab in `BattleStatsController`. Despite the name, this action does **not** return per-round historical breakdowns -- it returns the **same data structure and content** as `action=battleConsole` (current round state + cumulative campaign totals). The `zoneId` parameter is ignored.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `zoneId` | number | Yes | The zone (round) number |
| `battleZoneId` | number | Yes | The specific battle zone ID (division) |
| `action` | string | Yes | Must be `"warHistory"` |
| `_token` | string | Yes | CSRF token from `SERVER_DATA.csrfToken` |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/battle-console' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&zoneId=8&battleZoneId=38158390&action=warHistory&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

Response is **identical in structure** to [`action=battleConsole`](#get-battle-console-data). Tested on battle 869713 (round 5, 4 rounds of history):

```json
{
  "division": [
    {
      "id": 38185661,
      "division": 1,
      "countries": {
        "24": {
          "roundDominationPoints": 1830,
          "defenceShield": 0,
          "totalPointsWonByDivision": 1,
          "wall": 100,
          "combatOrders": []
        },
        "42": {
          "roundDominationPoints": 1470,
          "defenceShield": null,
          "totalPointsWonByDivision": 0,
          "wall": 0,
          "combatOrders": []
        },
        "divisionWinner": 24,
        "dominatingCountry": 24
      },
      "type": "tank",
      "divisionWinner": false,
      "isEpic": false,
      "epicState": 0,
      "epicBattleProgress": 0,
      "campaignPoints": 1,
      "winnerInfo": {
        "message": "Division 1 of USA won this battle",
        "countryName": "USA",
        "countryPermalink": "USA",
        "campaignFinished": false,
        "waiting": true
      }
    }
  ],
  "totalPointsByDivision": {
    "1": { "42": 3, "24": 2 },
    "2": { "42": 8, "24": 0 },
    "3": { "42": 15, "24": 0 },
    "4": { "42": 25, "24": 0 },
    "11": { "42": 35, "24": 0 }
  },
  "totalPoints": { "24": 2, "42": 86 },
  "isResistance": false,
  "isRevolution": false,
  "isDictatorship": false,
  "allies": { "42": [], "24": [] },
  "battle_time": 7130,
  "serverTime": 1771042613
}
```

> Note: Only 1 of 5 divisions shown for brevity -- full response contains all 5 divisions identical to `battleConsole`.

</details>

### Notes

- **Functionally identical to `battleConsole`**: Live testing confirmed this returns the **exact same** data structure and content as `action=battleConsole`. The "warHistory" name reflects the frontend's usage context (populating the War History tab), not a different data format
- **`zoneId` parameter is ignored**: Tested with `zoneId=1` and `zoneId=5` on a round-5 battle -- same response each time
- **No per-round history**: Does **not** return round-by-round historical data, timestamps, or breakdown of previous rounds. Only shows current round state + cumulative `totalPointsByDivision`
- **No finish timestamps**: Does not contain any round finish or campaign finish timestamps (see [Determining Battle Finish Time](wars.md#determining-battle-finish-time))
- **`winnerInfo` differences from finished battles**: For active battles with finished divisions, `winnerInfo` has `waiting: true` and no `isConquered`/`timeUntil` fields (compared to finished battles which have `campaignFinished: true`, `isConquered`, `waiting: false`, `timeUntil: 0`)
- **Same endpoint**: Uses the same `POST /en/military/battle-console` endpoint as `battleConsole`, `battleStatistics`, `fighterStatistics`, and `combatOrders` actions
