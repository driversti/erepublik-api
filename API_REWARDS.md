# eRepublik API - Rewards

#erepublik #api #rewards #objectives #missions

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Rewards endpoints cover daily objectives, missions, achievement rewards, and bonus systems. These endpoints track player progress toward daily goals and provide information about claimable rewards.

---

## Endpoints

## Get Daily Objective Status

**Method:** POST
**URL:** `/en/main/objective-status`
**Auth Required:** Yes

### Description

Retrieves the current status of daily objectives, including progress, completed objectives, available rewards, and time remaining. Daily objectives reset every 24 hours and reward players for achieving specific action point thresholds (20, 40, 60, 80, 100 points).

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request (cURL)

```bash
curl -X POST 'https://www.erepublik.com/en/main/objective-status' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

### Example Response

```json
{
  "status": {
    "progress": 70,
    "claimedObjectives": {
      "20": {
        "objective_cost": 20,
        "claimed": true,
        "claimed_at": "2026-01-25 00:02:40"
      },
      "40": {
        "objective_cost": 40,
        "claimed": true,
        "claimed_at": "2026-01-25 00:02:41"
      },
      "60": {
        "objective_cost": 60,
        "claimed": true,
        "claimed_at": "2026-01-25 01:17:18"
      }
    },
    "endDate": "Sun, 25 Jan 2026 23:59:59 -0800",
    "timeLeft": 62607
  },
  "data": {
    "20": {
      "objective_cost": 20,
      "rewards": [
        {
          "type": "strength",
          "name": "Strength",
          "amount": 2
        },
        {
          "type": "overtime_points",
          "name": "Overtime Points",
          "amount": 1
        },
        {
          "type": "food_raw",
          "name": "Food Raw Materials",
          "amount": 10,
          "new_player_ramp_up_factor": 0.1
        },
        {
          "type": "collection_part_3",
          "name": "Barrel",
          "amount": 1
        },
        {
          "type": "currency",
          "name": "Currency",
          "amount": 100,
          "new_player_ramp_up_factor": 0.1
        }
      ]
    },
    "40": {
      "objective_cost": 40,
      "rewards": [
        {
          "type": "speed_booster",
          "name": "x2 Damage Accelerator",
          "amount": 1,
          "minutes": 3
        },
        {
          "type": "overtime_points",
          "name": "Overtime Points",
          "amount": 1
        },
        {
          "type": "food_raw",
          "name": "Food Raw Materials",
          "amount": 15,
          "new_player_ramp_up_factor": 0.1
        },
        {
          "type": "collection_part_5",
          "name": "Projectile",
          "amount": 1
        },
        {
          "type": "currency",
          "name": "Currency",
          "amount": 200,
          "new_player_ramp_up_factor": 0.1
        }
      ]
    },
    "60": {
      "objective_cost": 60,
      "rewards": [
        {
          "type": "ghost_booster",
          "name": "Ghost Booster",
          "amount": 1,
          "minutes": 1
        },
        {
          "type": "overtime_points",
          "name": "Overtime Points",
          "amount": 1
        },
        {
          "type": "weapon_raw",
          "name": "Weapon Raw Materials",
          "amount": 25,
          "new_player_ramp_up_factor": 0.1
        },
        {
          "type": "collection_part_4",
          "name": "Scope",
          "amount": 1
        },
        {
          "type": "currency",
          "name": "Currency",
          "amount": 300,
          "new_player_ramp_up_factor": 0.1
        }
      ]
    },
    "80": {
      "objective_cost": 80,
      "rewards": [
        {
          "type": "fuel",
          "name": "Fuel",
          "amount": 1
        },
        {
          "type": "overtime_points",
          "name": "Overtime Points",
          "amount": 1
        },
        {
          "type": "house_raw",
          "name": "House Raw Materials",
          "amount": 1
        },
        {
          "type": "collection_part_6",
          "name": "Trigger Kit",
          "amount": 1
        },
        {
          "type": "currency",
          "name": "Currency",
          "amount": 400,
          "new_player_ramp_up_factor": 0.1
        }
      ]
    },
    "100": {
      "objective_cost": 100,
      "rewards": [
        {
          "type": "energy_bars",
          "name": "Energy Bar",
          "amount": 1
        },
        {
          "type": "overtime_points",
          "name": "Overtime Points",
          "amount": 1
        },
        {
          "type": "aircraft_raw",
          "name": "Aircraft Weapons Raw Materials",
          "amount": 1
        },
        {
          "type": "collection_part_2",
          "name": "Stock",
          "amount": 1
        },
        {
          "type": "currency",
          "name": "Currency",
          "amount": 500,
          "new_player_ramp_up_factor": 0.1
        }
      ]
    }
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| status.progress | number | Current daily objective progress (action points earned) |
| status.claimedObjectives | object | Map of claimed objectives with claim timestamps |
| status.endDate | string | When the current daily objective period ends |
| status.timeLeft | number | Seconds remaining until reset |
| data.{threshold} | object | Reward data for each threshold (20, 40, 60, 80, 100) |
| data.{threshold}.objective_cost | number | Action points required to unlock this tier |
| data.{threshold}.rewards | array | List of rewards for completing this objective |

### Reward Types

| Type | Description | Example Items |
|------|-------------|---------------|
| strength | Permanent strength increase | +2 Strength |
| overtime_points | Points for working overtime | +1 Overtime Point |
| food_raw | Raw materials for food production | 10-15 units |
| weapon_raw | Raw materials for weapon production | 25 units |
| house_raw | Raw materials for house production | 1 unit |
| aircraft_raw | Raw materials for aircraft weapons | 1 unit |
| currency | In-game currency | 100-500 units |
| collection_part_* | Weapon collection parts | Barrel, Stock, Scope, Projectile, Trigger Kit |
| speed_booster | Temporary damage multiplier | x2 Damage for 3 minutes |
| ghost_booster | Temporary ghost mode | 1 minute duration |
| fuel | Energy recovery item | 1 Fuel |
| energy_bars | Energy recovery item | 1 Energy Bar |

### Notes

- CSRF token (`_token`) must be obtained from the page where this action is triggered
- Daily objectives reset at midnight server time (PST/PDT)
- The `progress` value increases based on actions performed (working, training, fighting, etc.)
- Each objective tier can only be claimed once per day
- `new_player_ramp_up_factor` appears to scale rewards for newer players (lower multiplier for new accounts)
- Collection parts are used to complete weapon collections for permanent bonuses
- Boosters have a `minutes` field indicating duration
- The `timeLeft` countdown is in seconds and updates dynamically

---

## Get Daily Missions Data

**Method:** POST
**URL:** `/en/main/daily-missions-data`
**Auth Required:** Yes

### Description

Retrieves the complete list of daily missions available to the player, including mission details, requirements, progress, rewards, and completion status. Daily missions reward players with Prestige Points and Activity Points (which contribute to daily objectives).

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request (cURL)

```bash
curl -X POST 'https://www.erepublik.com/en/main/daily-missions-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

### Example Response

```json
{
  "missions": [
    {
      "id": 100016,
      "title": "Manufacture Weapons Raw Materials",
      "description": "Produce 10 Weapons Raw Materials",
      "type": "daily",
      "hints": {
        "WeaponsRawProduced": {
          "requirement": "WeaponsRawProduced",
          "text": "",
          "link": "company_my_companies"
        }
      },
      "priority": 100016,
      "rewards": {
        "prestige_points": {
          "name": "Prestige Points",
          "iconUrl": "/images/modules/_icons/reward_prestige_points.png",
          "type": "prestige_points",
          "amount": 9,
          "displayValue": 9
        },
        "daily_objective_progress": {
          "name": "Activity Points",
          "type": "daily_objective_progress",
          "amount": 3,
          "displayValue": 3
        }
      },
      "requirements": {
        "WeaponsRawProduced": {
          "type": "WeaponsRawProduced",
          "threshold": 10,
          "text": "Produce 10 Weapons Raw Materials",
          "displayThreshold": 10
        }
      },
      "start_conditions": [],
      "citizen_data": {
        "mission_id": 100016,
        "started_at": 1769328092,
        "finished_at": null
      },
      "is_active": 1,
      "starts_at": "2022-04-07 00:00:00",
      "ends_at": null,
      "created_at": 1649240438,
      "updated_at": "2022-04-06 03:20:38",
      "gold_unlock": [],
      "hint": {
        "hints": [],
        "links": {
          "WeaponsRawProduced": {
            "url": "//www.erepublik.com/en/economy/myCompanies",
            "link": "My companies",
            "is_land": true
          }
        }
      },
      "progress": [
        {
          "completed": true,
          "requirement": "WeaponsRawProduced",
          "partial": "10 / 10"
        }
      ]
    },
    {
      "id": 100007,
      "title": "Fight in a Meaningful Battle",
      "description": "Spend 250 Energy fighting in the Campaign of the Day or for the Daily Order",
      "type": "daily",
      "requirements": {
        "DeployEnergyMeaningful": {
          "type": "DeployEnergyMeaningful",
          "threshold": 250,
          "text": "Deploy 250 Energy in a Meaningful Battle",
          "displayThreshold": 250
        }
      },
      "rewards": {
        "prestige_points": {
          "amount": 30
        },
        "daily_objective_progress": {
          "amount": 10
        }
      },
      "progress": [
        {
          "completed": false,
          "requirement": "DeployEnergyMeaningful",
          "partial": "0 / 250"
        }
      ]
    }
  ]
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| missions | array | List of all daily missions |
| missions[].id | number | Unique mission identifier |
| missions[].title | string | Mission title/name |
| missions[].description | string | Detailed mission description |
| missions[].type | string | Mission type (always "daily" for daily missions) |
| missions[].priority | number | Mission sorting priority |
| missions[].rewards | object | Reward breakdown (prestige_points, daily_objective_progress) |
| missions[].requirements | object | Mission completion requirements with thresholds |
| missions[].citizen_data | object | Player-specific mission data (started_at, finished_at timestamps) |
| missions[].is_active | number | Whether mission is currently active (1 = active) |
| missions[].progress | array | Current progress for each requirement |
| missions[].progress[].completed | boolean | Whether the requirement is completed |
| missions[].progress[].partial | string | Progress display (e.g., "10 / 10", "2 / 3") |
| missions[].hint | object | Hints and helpful links for completing the mission |

### Mission Requirement Types

| Type | Description | Example Threshold |
|------|-------------|-------------------|
| WeaponsRawProduced | Produce Weapons Raw Materials | 10 |
| WeaponsProduced | Produce Weapons | 20 |
| FoodRawProduced | Produce Food Raw Materials | 10 |
| FoodProduced | Produce Food | 200 |
| HousesRawProduced | Produce Houses Raw Materials | 1 |
| HousesProduced | Produce Houses | 1 |
| AircraftRawProduced | Produce Aircraft Raw Materials | 1 |
| AircraftWeaponsProduced | Produce Aircraft Weapons | 5 |
| AssignEmployees | Assign employees to companies | 1-5 |
| SpendFuel | Use Fuel in battles | 3 |
| DeployEnergyMeaningful | Fight in Campaign/Daily Order | 250 energy |
| ActivateBooster | Activate boosters | 1 |
| TravelTimes | Travel to different regions | 1 |
| FightSpecialWeapon | Launch bombs or rockets | 1 |
| DeployWithMarketWeapon | Fight with manufactured weapons (Q1-Q7) | 1 deployment |

### Reward Types

| Reward Type | Description | Range |
|-------------|-------------|-------|
| prestige_points | Points for leveling prestige | 6-45 points |
| daily_objective_progress | Activity Points toward daily objectives | 2-15 points |

### Notes

- CSRF token (`_token`) must be obtained from the page
- Missions reset daily at midnight server time (PST/PDT)
- Some missions are mutually exclusive (e.g., different "Assign Employees" tiers)
- The `citizen_data.started_at` timestamp indicates when the player first saw this mission
- The `citizen_data.finished_at` timestamp is `null` for incomplete missions
- Activity Points earned from missions contribute to the daily objective thresholds (20, 40, 60, 80, 100)
- Prestige Points contribute to the player's prestige level
- `hint.links` provide helpful navigation links (e.g., to "My companies" page)
- The `is_land` flag in hint links indicates production-related missions
- The `is_battle` flag indicates combat-related missions
- Some missions have hints with additional explanatory text
- `gold_unlock` array is empty for standard missions (may be used for premium/locked missions)

---

## Claim Mission Reward

**Method:** POST
**URL:** `/en/main/mission-solve`
**Auth Required:** Yes

### Description

Claims rewards for a completed daily mission. When a mission's requirements are met, this endpoint marks it as complete and grants the rewards (Prestige Points and Activity Points) to the player. Returns whether the mission was successfully finished.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| missionId | number | Yes | The unique mission ID to claim rewards for |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request (cURL)

```bash
curl -X POST 'https://www.erepublik.com/en/main/mission-solve' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw '_token=YOUR_CSRF_TOKEN&missionId=100016'
```

### Example Response (Not Completed)

```json
{
  "finished": false
}
```

### Example Response (Successfully Completed)

```json
{
  "finished": true,
  "rewards": {
    "prestige_points": {
      "name": "Prestige Points",
      "type": "prestige_points",
      "amount": 9
    },
    "daily_objective_progress": {
      "name": "Activity Points",
      "type": "daily_objective_progress",
      "amount": 3
    }
  }
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| finished | boolean | Whether the mission was successfully completed and rewards claimed |
| rewards | object | Rewards granted (only present when `finished: true`) |
| rewards.prestige_points | object | Prestige Points awarded |
| rewards.daily_objective_progress | object | Activity Points awarded |

### Notes

- CSRF token (`_token`) must be obtained from the page
- This endpoint only succeeds if the mission requirements are fully met
- Returns `{"finished": false}` if mission is not yet complete
- Mission must be active and not already claimed
- Once claimed, the mission's `citizen_data.finished_at` timestamp is set
- Rewards are automatically added to the player's account
- Activity Points contribute to daily objective progress (20/40/60/80/100 thresholds)
- Each mission can only be claimed once per day
- The mission list (from `/en/main/daily-missions-data`) must be checked first to see available missions and their IDs

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
- [Economy API](API_ECONOMY.md)
