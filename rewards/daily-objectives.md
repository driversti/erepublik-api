# Daily Objectives

Endpoints for checking daily objective progress and claiming tier rewards. Daily objectives reset every 24 hours and reward players for achieving specific action point thresholds (20, 40, 60, 80, 100 points).

#erepublik #api #rewards #objectives

[< Back to Rewards](README.md) | [< Back to Table of Contents](../API_TOC.md)

---

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

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/objective-status' \
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

</details>

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

## Claim Daily Objective Tier Reward

**Method:** POST
**URL:** `/en/main/objective-claim-reward`
**Auth Required:** Yes

### Description

Claims a daily objective tier reward. When a player's activity points reach a threshold (20, 40, 60, 80, or 100), they can claim the corresponding reward box. Each tier can only be claimed once per day. Use `/en/main/objective-status` first to check which tiers are claimable.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| objectiveCost | number | Yes | The tier threshold to claim: `20`, `40`, `60`, `80`, or `100` |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Referer | `https://www.erepublik.com/en` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/objective-claim-reward' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Referer: https://www.erepublik.com/en' \
  --data-raw 'objectiveCost=20&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
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
      "amount": 1,
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
      "amount": 10,
      "new_player_ramp_up_factor": 0.1
    }
  ]
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| rewards | array | List of rewards granted for claiming this tier |
| rewards[].type | string | Reward type identifier (e.g., `strength`, `overtime_points`, `food_raw`, `collection_part_3`, `currency`) |
| rewards[].name | string | Human-readable reward name |
| rewards[].amount | number | Quantity of the reward granted |
| rewards[].new_player_ramp_up_factor | number | Optional scaling factor for newer players (lower multiplier) |

### Known Reward Types

| Type | Name | Description |
|------|------|-------------|
| strength | Strength | Strength points (same as training) |
| overtime_points | Overtime Points | Points for overtime work |
| food_raw | Food Raw Materials | Raw materials for food production |
| currency | Currency | Local currency (CC) |
| collection_part_3 | Barrel | Weapon collection part |
| collection_part_4 | Projectile | Weapon collection part |
| collection_part_5 | Scope | Weapon collection part |
| collection_part_6 | Trigger Kit | Weapon collection part |
| collection_part_7 | Stock | Weapon collection part |
| damage_accelerator | x2 Damage Accelerator | Combat booster (has `minutes` field) |
| ghost_booster | Ghost Booster | Combat booster (has `minutes` field) |
| fuel | Fuel | Used in aircraft battles |
| energy_bar | Energy Bar | Restores 200 energy |
| weapon_raw | Weapon Raw Materials | Raw materials for weapon production |
| house_raw | House Raw Materials | Raw materials for house production |
| aircraft_raw | Aircraft Weapons Raw Materials | Raw materials for aircraft weapon production |

### Notes

- The `objectiveCost` must match one of the five tier thresholds: 20, 40, 60, 80, or 100
- The player's activity points (from `/en/main/objective-status` -> `status.progress`) must be >= the requested tier
- Each tier can only be claimed once per day; claiming an already-claimed tier will fail
- Rewards vary by tier -- higher tiers give better rewards
- The `Referer` header is required for this endpoint
- Items with `new_player_ramp_up_factor` may scale differently for newer accounts
- Tiers should be claimed in order (lowest first), matching how the game UI works
- Daily objectives reset at midnight PST (eRepublik day change)
- Discovered via ebot source code (`collectObjectiveRewards` in `src/actions/collectRewards.js`). Verified 2026-02-28.

---

## Related

- [Daily Missions](daily-missions.md)
- [Weekly Challenges](weekly-challenges.md)
- [VIP Points](vip.md)
