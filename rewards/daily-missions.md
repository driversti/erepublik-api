# Daily Missions

Endpoints for retrieving daily mission data and claiming mission rewards. Daily missions reward players with Prestige Points and Activity Points (which contribute to daily objectives).

#erepublik #api #rewards #missions

[< Back to Rewards](README.md) | [< Back to Table of Contents](../API_TOC.md)

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

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/daily-missions-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

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

</details>

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
- Activity Points earned from missions contribute to the [daily objective](daily-objectives.md) thresholds (20, 40, 60, 80, 100)
- Prestige Points contribute to the player's prestige level and [weekly challenges](weekly-challenges.md)
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

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/mission-solve' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw '_token=YOUR_CSRF_TOKEN&missionId=100016'
```

</details>

<details>
<summary>Example Response (Not Completed)</summary>

```json
{
  "finished": false
}
```

</details>

<details>
<summary>Example Response (Successfully Completed)</summary>

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

</details>

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
- Activity Points contribute to [daily objective](daily-objectives.md) progress (20/40/60/80/100 thresholds)
- Each mission can only be claimed once per day
- The mission list (from `/en/main/daily-missions-data`) must be checked first to see available missions and their IDs

---

## Related

- [Daily Objectives](daily-objectives.md)
- [Weekly Challenges](weekly-challenges.md)
- [VIP Points](vip.md)
