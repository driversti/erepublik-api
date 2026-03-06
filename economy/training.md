# Training

#erepublik #api #economy #training

[< Back to Economy Overview](README.md)

Training grounds status and training actions for increasing citizen strength and earning Super Soldier medals.

**Related:** [Work](work.md) | [Inventory](inventory.md)

---

## Get Training Grounds Status

**Method:** GET
**URL:** `/en/main/training-grounds-json`
**Auth Required:** Yes

### Description

Retrieves the current status of all training grounds owned by the citizen, including which facilities have been trained in today, their quality levels, costs, and the citizen's available resources (energy/gold) for training. Training grounds are used to increase strength, with medals awarded every 250 strength gained.

### Parameters

No parameters required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/training-grounds-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "message": "",
  "upgradeBanner": "",
  "bfUpgradeBanner": "",
  "contracts": false,
  "progress": 75,
  "can_train": false,
  "grounds": [
    {
      "id": 85434,
      "trained": true,
      "training": 62951,
      "anniversary": false,
      "name": "Weights Room - Quality 4",
      "icon": "//www.erepublik.net/images/modules/myland/buildings/training_grounds_q4.png",
      "quality": 4,
      "cost": 0,
      "effectiveCost": 0,
      "strength": 20,
      "default": false
    },
    {
      "id": 363943,
      "trained": true,
      "training": 62951,
      "anniversary": false,
      "name": "Climbing Center - Quality 4",
      "icon": "//www.erepublik.net/images/modules/myland/buildings/train_1_q4.png",
      "quality": 4,
      "cost": 0.19,
      "effectiveCost": 0,
      "strength": 10,
      "default": false
    },
    {
      "id": 1979243,
      "trained": true,
      "training": 62951,
      "anniversary": false,
      "name": "Shooting Range - Quality 4",
      "icon": "//www.erepublik.net/images/modules/myland/buildings/train_2_q4.png",
      "quality": 4,
      "cost": 0.89,
      "effectiveCost": 0,
      "strength": 20,
      "default": false
    },
    {
      "id": 364274,
      "trained": true,
      "training": 62951,
      "anniversary": false,
      "name": "Special Forces Center - Quality 4",
      "icon": "//www.erepublik.net/images/modules/myland/buildings/train_3_q4.png",
      "quality": 4,
      "cost": 1.79,
      "effectiveCost": 0,
      "strength": 40,
      "default": false
    }
  ],
  "page_details": {
    "health_tooltip": "Energy required",
    "gold_tooltip": "Gold cost",
    "health": 5886,
    "recoverable_health": {
      "value": 0
    },
    "health_warn": "Food will be consumed, make sure you have enough food in your Storage",
    "health_warn_limit": "Energy limit exceeded, you will be able to train only if you have Energy Bars",
    "gold": 72916.5823,
    "gold_warn": "Not enough Gold to train",
    "recoverable_health_in_food": 14588942,
    "energy": 7935200
  },
  "canTrain": false,
  "hasFreeTrain": true,
  "hasTrainingContract": false,
  "contractExpiresAt": 0,
  "status": true,
  "error": false
}
```

</details>

### Notes

- **Training Status:**
  - `can_train`: `true` if the citizen hasn't trained today, `false` if already trained
  - `progress`: Strength gained towards the next medal (medals awarded every 250 strength)
  - `trained`: Indicates if training was completed in this specific facility today

- **Training Grounds:**
  - Four types of training facilities: Weights Room, Climbing Center, Shooting Range, Special Forces Center
  - Each facility has a quality level (Q1-Q4) affecting strength gains
  - `strength`: Amount of strength gained from training in this facility
  - `cost`: Gold cost to train in this facility
  - `effectiveCost`: Actual cost after applying any discounts or contracts
  - `training`: eDay when the facility was last used (eRepublik day number)

- **Resources:**
  - `page_details.health`: Current available energy
  - `page_details.gold`: Current gold balance
  - `page_details.recoverable_health_in_food`: Total food value in storage (energy equivalent)
  - `page_details.energy`: Maximum energy capacity

- **Training Contracts:**
  - `hasFreeTrain`: Indicates if free training is available (typically one free training per day)
  - `hasTrainingContract`: Indicates if an active training contract exists (reduces/removes costs)
  - `contractExpiresAt`: Unix timestamp when the training contract expires (0 if no contract)

- **Warnings:**
  - `health_warn`: Displayed when food will be consumed from storage
  - `health_warn_limit`: Displayed when energy limit is exceeded
  - `gold_warn`: Displayed when insufficient gold to train

- Training resets daily at eRepublik day change (00:00 eRepublik time)
- Higher quality facilities provide more strength but cost more gold
- Training is a key mechanic for increasing combat effectiveness

---

## Train in Training Grounds

**Method:** POST
**URL:** `/en/economy/train`
**Auth Required:** Yes

### Description

Executes training in selected training grounds, gaining strength points. Each facility can be trained once per day. Training consumes no energy for the default free training but may cost gold for premium facilities. Returns updated strength totals, XP gained, and progress toward the Super Soldier Medal.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| grounds[N][id] | number | Yes | Training ground ID (from `/en/main/training-grounds-json`) |
| grounds[N][train] | number | Yes | Training flag: `1` to train, `0` to skip |
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
curl -X POST 'https://www.erepublik.com/en/economy/train' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'grounds%5B0%5D%5Bid%5D=85434&grounds%5B0%5D%5Btrain%5D=1&grounds%5B1%5D%5Bid%5D=363943&grounds%5B1%5D%5Btrain%5D=1&grounds%5B2%5D%5Bid%5D=1979243&grounds%5B2%5D%5Btrain%5D=1&grounds%5B3%5D%5Bid%5D=364274&grounds%5B3%5D%5Btrain%5D=1&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "status": true,
  "message": true,
  "result": {
    "strength_bonus": 90,
    "xp": 8,
    "health": 40,
    "gold": 0,
    "strength": 412917,
    "consumed_summary": [],
    "to_achievement": 83,
    "to_achievement_text": "You need an additional <span id=\"train_to_achievement\">83 Strength points</span> to get the Super Soldier Medal.",
    "daily_tasks_done": true
  },
  "error": false
}
```

</details>

### Notes

- **Training Grounds Array:**
  - Submit multiple grounds in a single request (up to 4 facilities)
  - Array indices start at 0: `grounds[0]`, `grounds[1]`, etc.
  - Each ground requires both `id` and `train` parameters
  - **WARNING:** The `train=0` flag is ignored by the server -- ALL grounds included in the request body will be trained regardless of the flag value. To train selectively, only include the desired grounds in the body and omit the rest entirely. (Verified 2026-02-28)

- **Response Fields:**
  - `strength_bonus`: Total strength gained from this training session
  - `xp`: Experience points earned
  - `health`: Health points consumed (or restored, depending on context)
  - `gold`: Gold spent on training (0 if free training or training contract active)
  - `strength`: Total accumulated strength after training
  - `consumed_summary`: Array of items consumed during training (energy, gold)
  - `to_achievement`: Strength points needed for the next Super Soldier Medal
  - `to_achievement_text`: Human-readable progress message (HTML)
  - `daily_tasks_done`: `true` if daily task requirements are met

- **Training Costs:**
  - First facility (Weights Room): Free
  - Additional facilities: May cost gold (0.19, 0.89, 1.79 gold depending on quality)
  - Training contracts eliminate gold costs
  - `effectiveCost` from training grounds status shows actual cost after contracts

- **Daily Limits:**
  - Each training ground can only be used once per day
  - Training resets at eRepublik day change (00:00 eRepublik time)
  - Use `/en/main/training-grounds-json` to check if already trained

- **Super Soldier Medal:**
  - Awarded every 250 strength points gained
  - `to_achievement` tracks remaining points to next medal
  - Medal rewards include Gold and achievement progress

- **Related Endpoints:**
  - Use `/en/main/training-grounds-json` to get available training grounds and current status
  - Training ground IDs are citizen-specific (each citizen has unique facility IDs)
