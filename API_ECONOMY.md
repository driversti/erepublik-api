# eRepublik API - Economy

#erepublik #api #economy #marketplace

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Economy endpoints cover marketplace, work mechanics, companies, currencies (Gold/CC), and financial transactions.

> For Gold earning methods and cost reference data, see [[Gold]].

---

## Endpoints

### Get Job Data

**Method:** GET
**URL:** `/en/main/job-data`
**Auth Required:** Yes

#### Description

Retrieves the current employment and work status for the authenticated citizen, including employer information, salary details, and overtime availability.

#### Parameters

No parameters required.

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

#### Example Request

```bash
curl 'https://www.erepublik.com/en/main/job-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

#### Example Response

```json
{
  "isEmployee": true,
  "employer": {
    "id": 2580095,
    "name": "Greenday_1989",
    "img": "https://cdnt.erepublik.net/hTXa-zXGxYdBcMT9YtgJ9BDyNUA=/142x142/smart/avatars/Citizens/2010/01/18/ef89175e7f4e51d0e3036e743cee0cb6.jpg?afd07d8d1a944e1c3867f2ea89094394"
  },
  "salary": 7500,
  "tax": 75,
  "currency": "LTL",
  "alreadyWorked": true,
  "playerLevel": 1690,
  "overTime": {
    "points": 10342,
    "nextOverTime": 1769331700,
    "salary": 15000,
    "usableEnergy": 8308
  }
}
```

#### Notes

- Returns `isEmployee: true` if the citizen is currently employed
- `alreadyWorked` indicates if the citizen has already worked today
- `overTime.nextOverTime` is a Unix timestamp indicating when the next overtime work is available
- `overTime.points` shows accumulated overtime points
- `overTime.salary` is the overtime work salary (typically double the regular salary)
- Currency codes include: LTL (Lithuanian Litas), USD, GOLD, etc.
- Tax amount is deducted from the gross salary

---

## Work as Employee

**Method:** POST
**URL:** `/en/economy/work`
**Auth Required:** Yes

### Description

Work at the current employer as an employee. This endpoint performs the daily work action, earning salary and contributing to work streak achievements. Can only be executed once per day per citizen.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| action_type | string | Yes | Must be set to `work` for employee work |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/work' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=work'
```

### Example Response

**Already worked today:**
```json
{
  "status": false,
  "message": "already_worked",
  "result": ""
}
```

### Notes

- **Daily Limit:** Can only be executed once per day (resets at eRepublik day change)
- **Error Messages:**
  - `already_worked`: Citizen has already performed daily work
  - Other errors may include: not employed, employer has no funds, etc.
- **Same Endpoint as WAM:** This uses the same `/en/economy/work` endpoint but with `action_type=work` instead of `action_type=production`
- **Requirements:** Citizen must be employed to use this endpoint
- The CSRF `_token` can be obtained from the page source or previous API responses

---

## Work Overtime

**Method:** POST
**URL:** `/en/economy/workOvertime`
**Auth Required:** Yes

### Description

Performs overtime work at the citizen's current employer. This action grants additional salary, XP, and health in exchange for energy. Overtime work is typically available after regular work has been completed and has a cooldown period.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| action_type | string | Yes | Must be set to `workOvertime` |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/workOvertime' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=workOvertime'
```

### Example Response

```json
{
  "status": true,
  "message": true,
  "result": {
    "netSalary": 7425,
    "grossSalary": 7500,
    "tax": 75,
    "currency": "LTL",
    "days_in_a_row": 3764,
    "to_achievment": 28,
    "to_achievment_text": "You have <span id=\"production_to_achievment\">28 days</span> left to work to get the Hardworker medal.",
    "xp": 2,
    "health": 100,
    "consumedSummary": [],
    "daily_tasks_done": false
  }
}
```

### Notes

- **Energy Consumption:**
  - 10 energy if the overtime cooldown has passed (>1 hour since last overtime)
  - 100 energy if attempting to work again within the 1-hour cooldown period (anti-spam mechanism)
- `netSalary` is the amount received after tax deduction
- `days_in_a_row` tracks consecutive work days for achievement progress
- `to_achievment` shows remaining days needed for the Hardworker medal
- `xp` indicates experience points gained from this overtime work
- `health` shows the health points restored (typically 100)
- `consumedSummary` may contain food/gift items used during work
- Currency codes include: LTL, USD, EUR, RON, etc.
- The CSRF `_token` can be obtained from the page source or previous API responses
- Overtime is typically available after completing regular daily work

---

## Work as Manager (WAM)

**Method:** POST
**URL:** `/en/economy/work`
**Auth Required:** Yes

### Description

Work as Manager (WAM) in own companies. This endpoint allows working in multiple owned companies simultaneously, producing goods based on raw materials and production bonuses. Can only be executed once per day.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| own_work | string | No | URL-encoded JSON array of company IDs to work in as owner |
| employee_works | string | No | URL-encoded JSON object for employee work assignments |
| cntOwnWork | number | No | Count of own companies selected for work |
| cntEmployeeWork | number | No | Count of employee companies selected for work |
| cntSelected | number | Yes | Total count of selected companies |
| action_type | string | Yes | Must be set to `production` |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded; charset=UTF-8` | Yes |
| Accept | `application/json, text/javascript, */*; q=0.01` | Recommended |

### Example Request

```bash
# Work as Manager in 2 companies (IDs: 358462, 3271053)
curl -X POST 'https://www.erepublik.com/en/economy/work' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  --data-raw 'own_work=%5B358462%2C3271053%5D&employee_works=%7B%7D&cntOwnWork=2&cntEmployeeWork=0&cntSelected=2&action_type=production&_token=YOUR_CSRF_TOKEN'
```

### Example Response

```json
{
  "status": true,
  "message": true,
  "result": {
    "production": {
      "foodRaw": 0,
      "weaponRaw": 119.85,
      "houseRaw": 0,
      "airplaneRaw": 0,
      "food": [],
      "weapon": {
        "7": 441.5999999999998
      },
      "house": [],
      "airplane": [],
      "totalFood": 0,
      "totalWeapon": 441,
      "totalHouse": 0,
      "totalAirplane": 0
    },
    "resources": {
      "1": {
        "name": "Grain",
        "bonus": 25
      },
      "2": {
        "name": "Fish",
        "bonus": 10
      },
      "3": {
        "name": "Fruits",
        "bonus": 15
      },
      "4": {
        "name": "Cattle",
        "bonus": 20
      },
      "5": {
        "name": "Deer",
        "bonus": 30
      },
      "6": {
        "name": "Iron",
        "bonus": 10
      },
      "7": {
        "name": "Saltpeter",
        "bonus": 0
      },
      "8": {
        "name": "Aluminum",
        "bonus": 15
      },
      "9": {
        "name": "Oil",
        "bonus": 20
      },
      "10": {
        "name": "Rubber",
        "bonus": 0
      },
      "11": {
        "name": "Sand",
        "bonus": 5
      },
      "12": {
        "name": "Clay",
        "bonus": 0
      },
      "13": {
        "name": "Wood",
        "bonus": 7.5
      },
      "14": {
        "name": "Limestone",
        "bonus": 12.5
      },
      "15": {
        "name": "Granite",
        "bonus": 0
      },
      "16": {
        "name": "Neodymium",
        "bonus": 30
      },
      "17": {
        "name": "Magnesium",
        "bonus": 0
      },
      "18": {
        "name": "Cobalt",
        "bonus": 25
      },
      "19": {
        "name": "Titanium",
        "bonus": 0
      },
      "20": {
        "name": "Wolfram",
        "bonus": 0
      }
    },
    "days_in_a_row": 3764,
    "to_achievment": 28,
    "to_achievment_text": "You have <span id=\"production_to_achievment\">28 days</span> left to work to get the Hardworker medal.",
    "xp": 448,
    "health": 2240,
    "works": 0,
    "consumed_summary": [],
    "tax": 14929.8
  }
}
```

### Notes

- **Work as Manager (WAM):** This endpoint is specifically for working in companies you own as the manager
- **Daily Limit:** Can only be executed **once per day** (resets at eRepublik day change)
- **Company IDs:** Must be URL-encoded JSON array format (e.g., `[358462,3271053]` becomes `%5B358462%2C3271053%5D`)
- **Production Results:**
  - `production.weaponRaw` indicates raw production value before applying bonuses
  - `production.weapon` is a map where keys are quality levels (e.g., `"7"` for Q7 weapons)
  - Final production includes resource bonuses from territories
- **Resource Bonuses:**
  - IDs 1-5: Food raw materials (Grain, Fish, Fruits, Cattle, Deer)
  - IDs 6-10: Weapon raw materials (Iron, Saltpeter, Aluminum, Oil, Rubber)
  - IDs 11-15: House raw materials (Sand, Clay, Wood, Limestone, Granite)
  - IDs 16-20: Airplane raw materials (Neodymium, Magnesium, Cobalt, Titanium, Wolfram)
  - `bonus` represents the percentage production bonus from controlled territories
- **Work Streak:**
  - `days_in_a_row` tracks consecutive days of work for achievements
  - `to_achievment` shows remaining days needed for the Hardworker medal
- **Rewards:**
  - `xp` indicates total experience points gained
  - `health` shows total health points restored
  - `tax` is the total tax paid on production (based on country tax rates)
- The CSRF `_token` can be obtained from the page source or previous API responses
- For WAM operations, `employee_works` should be empty: `employee_works=%7B%7D` (empty JSON object)
- The `own_work` array contains all company IDs where you want to work as manager

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

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/training-grounds-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

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

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/train' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'grounds%5B0%5D%5Bid%5D=85434&grounds%5B0%5D%5Btrain%5D=1&grounds%5B1%5D%5Bid%5D=363943&grounds%5B1%5D%5Btrain%5D=1&grounds%5B2%5D%5Bid%5D=1979243&grounds%5B2%5D%5Btrain%5D=1&grounds%5B3%5D%5Bid%5D=364274&grounds%5B3%5D%5Btrain%5D=1&_token=YOUR_CSRF_TOKEN'
```

### Example Response

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

### Notes

- **Training Grounds Array:**
  - Submit multiple grounds in a single request (up to 4 facilities)
  - Array indices start at 0: `grounds[0]`, `grounds[1]`, etc.
  - Each ground requires both `id` and `train` parameters
  - Set `train=1` to train, `train=0` to skip that facility

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

---

## Get Shop Items

**Method:** GET
**URL:** `/en/main/shopItemsJson`
**Auth Required:** Yes

### Description

Retrieves all available items in the Gold Shop and VIP Shop, including prices, discounts, purchase limits, and item details. This endpoint returns two main sections:
- **Gold Items:** Standard shop items purchasable with Gold or Loyalty Points (energy bars, fuel, boosters, bazooka parts, storage)
- **VIP Shop Items:** Special discounted items available at different VIP levels, purchasable with Gold, Raw Materials, or Currency

### Parameters

No parameters required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/shopItemsJson' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

```json
{
  "error": false,
  "data": {
    "goldItems": {
      "sectionId": "goldItems",
      "title": "Gold Items",
      "items": [
        {
          "id": "fuel",
          "category": "fuel",
          "name": "Fuel",
          "details": {
            "description": "Increases your Deployment limit for the current week. Unused Fuel does not carry over to the next week. Fuel price starts at 1 Gold and increases with every purchase. Bought this week: 5"
          },
          "description": "+1 Fuel",
          "itemPurchaseLimit": 1,
          "initialPurchaseLimit": 1,
          "goldPrice": 1,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "isKey": false,
          "dotNotification": false
        },
        {
          "id": "refill",
          "category": "energyBars",
          "name": "1 Energy Bar",
          "details": {
            "description": "+200 Energy (Energy is vital in the eWorld as it is used in most of the Citizen's actions, such as fighting, training, working etc.)"
          },
          "description": "+200 Energy",
          "goldPrice": 5,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "lpPrice": 30,
          "lpCurrencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/loyalty_points.png",
          "lpCurrencyName": "Loyalty Points",
          "isKey": false,
          "dotNotification": false
        },
        {
          "id": "storage",
          "category": "storage",
          "name": "Storage",
          "details": {
            "description": "Extends the capacity of your Storage with 20000 Units (Storage is a building that stores products such as Energy Bars, Food, Weapons, Raw Materials etc.)"
          },
          "description": "+20000 Storage",
          "goldPrice": 100,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "lpPrice": 175,
          "lpCurrencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/loyalty_points.png",
          "lpCurrencyName": "Loyalty Points",
          "isKey": false,
          "dotNotification": false
        }
      ]
    },
    "vipShopItems": {
      "sectionId": "goldItems",
      "title": "VIP Shop",
      "items": [
        {
          "id": 3054,
          "vipLevel": 0,
          "type": "speed_booster_1_180",
          "quantity": 10,
          "currency": "gold",
          "cost": 0.7,
          "discount": 30,
          "data": {
            "name": "x2 Damage Accelerator",
            "type": "speed_booster",
            "image": "/images/icons/boosters/128px/speed.png",
            "quality": 1,
            "tooltip": "Hit x2 faster for 3 minutes",
            "discount": 30,
            "duration": 180
          },
          "maxQuantity": 10,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "price": 0.7,
          "category": "vipShop",
          "vipShopItem": true,
          "name": "x2 Damage Accelerator",
          "description": "Hit x2 faster for 3 minutes",
          "discountClass": "blue",
          "itemPurchaseLimit": 10,
          "soldOut": false
        },
        {
          "id": 3055,
          "vipLevel": 1,
          "type": "damage_booster_50_7200",
          "quantity": 10,
          "currency": "food_raw",
          "cost": 6,
          "discount": 85,
          "currencyIcon": "https://www.erepublik.net/images/icons/industry/7/default_55x55.png",
          "currencyName": "Food Raw Materials",
          "price": 6,
          "name": "+50% Ground Damage",
          "description": "Hit 50% stronger in Ground Battles for 2 hours",
          "discountClass": "red",
          "itemPurchaseLimit": 10,
          "soldOut": false
        },
        {
          "id": 3074,
          "vipLevel": 11,
          "type": "catchup_booster_40_300_vip11",
          "quantity": 0,
          "currency": "unavailable",
          "cost": 0,
          "discount": 10,
          "maxQuantity": 0,
          "locked": true,
          "currencyIcon": null,
          "currencyName": "Unavailable",
          "name": "+40% Ghost Booster",
          "description": "Add 40% of the Best Damage per Hit in a Ground Battle to your Damage for 5 minutes",
          "discountClass": "green",
          "itemPurchaseLimit": 0,
          "soldOut": false
        }
      ]
    }
  }
}
```

### Notes

- **Gold Items Section:**
  - Standard shop items available to all players
  - Purchasable with Gold or Loyalty Points (some items offer both options)
  - Includes: Fuel, Energy Bars, Speed Boosters, Bazooka Parts, Storage upgrades
  - `itemPurchaseLimit`: Remaining quantity available for purchase
  - `initialPurchaseLimit`: Original purchase limit before any purchases
  - Some items have dynamic pricing (e.g., Fuel price increases with each purchase)

- **VIP Shop Section:**
  - Special discounted items unlocked progressively by VIP level (0-13)
  - `vipLevel`: Minimum VIP level required to purchase the item
  - `locked`: `true` for items above the citizen's current VIP level
  - `soldOut`: `true` when purchase limit is exhausted
  - `discount`: Percentage discount applied (affects `discountClass` color)

- **Currencies:**
  - `gold`: Standard Gold currency
  - `food_raw`, `weapon_raw`, `aircraft_raw`, `house_raw`: Raw materials
  - `currency`: Country currency (CC)
  - `loyalty_points`: Loyalty Points (alternative to Gold for some items)
  - `unavailable`: Item is locked (insufficient VIP level)

- **Discount Classes:**
  - `green`: 5-20% discount
  - `blue`: 25-40% discount
  - `purple`: 45-60% discount
  - `orange`: 65-80% discount
  - `red`: 85%+ discount

- **Item Types:**
  - `speed_booster_X`: Damage accelerators (x2, x5, x9 faster hitting)
  - `damage_booster_X`: Damage increase boosters (+50%, +100%, +150%)
  - `energy_bars`: Energy restoration items (+100, +200 energy)
  - `storage`: Storage capacity expansion
  - `fuel`: Weekly deployment limit increase
  - `catchup_booster`: Ghost boosters (% of best damage)
  - `deploy_size_booster`: Deployment size increase
  - `prestige_points`: Prestige increase
  - Various other specialized items (reset tokens, discharge documents, etc.)

- VIP levels range from 0 (non-VIP) to 13 (maximum VIP tier)
- Items often have limited daily/weekly purchase quantities
- Raw material prices make high-tier items accessible without spending Gold

- **Pack Promotions:**
  - Special pack offers (Tycoon Pack, Hero Packs, Combat Packs, Seasonal Packs) appear in `ErpkShop` variable on the homepage
  - `ErpkShop = {"promotions":[],"popupPromotions":[],"battlePromotions":[],"numberOfPurchasablePacks":0,"specialSalesEvent":false}`
  - Packs are real-money purchases (EUR/USD/Game Tokens), not available via this JSON endpoint
  - See [[Packs]] for pack mechanics (Tycoon Pack: 20g + 150 tickets + 20% productivity for 7 days)
  - Promotions shown as banners on homepage, purchased via external payment providers

---

## Marketplace Page

**Method:** GET
**URL:** `/en/economy/marketplace`
**Auth Required:** Yes

### Description

The main marketplace HTML page where citizens browse and purchase products. Built with AngularJS (`MarketplaceController`), the page renders a product browser with country/industry/quality filters and an offer table that updates dynamically via the `marketplaceAjax` AJAX endpoint. The page also embeds critical JavaScript variables (`filters`, `industryJSON`, `conditionedIndustries`) that define marketplace configuration.

### Parameters

No URL parameters. The page uses **URL hash routing** to set the initial market view:

| Pattern | Example | Description |
|---------|---------|-------------|
| `#{countryId}/{quality}` | `marketplace#72/1` | Opens market for country 72 (Lithuania) at quality 1 |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |

### Example Request

```bash
curl 'https://www.erepublik.com/en/economy/marketplace' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Page Structure

```
body#marketplace_zone
└── div#marketplace (ng-controller="MarketplaceController")
    ├── h1 "Marketplace"
    ├── div.country-select
    │   └── select#countryId (74 countries, current country pre-selected)
    ├── div#filters_expanded
    │   ├── h4 "Select product"
    │   ├── div.product_selector
    │   │   └── ul (9 industry filter links + "Companies" link)
    │   │       ├── li > a.industrySelect#1  ng-click="setIndustry(1)"  — Food
    │   │       ├── li > a.industrySelect#2  ng-click="setIndustry(2)"  — Weapons
    │   │       ├── li > a.industrySelect#23 ng-click="setIndustry(23)" — Aircraft Weapons
    │   │       ├── li > a.industrySelect#3  ng-click="setIndustry(3)"  — Moving Tickets
    │   │       ├── li > a.industrySelect#4  ng-click="setIndustry(4)"  — Houses
    │   │       ├── li > a.industrySelect#7  ng-click="setIndustry(7)"  — Food Raw Materials
    │   │       ├── li > a.industrySelect#12 ng-click="setIndustry(12)" — Weapon Raw Materials
    │   │       ├── li > a.industrySelect#24 ng-click="setIndustry(24)" — Aircraft Weapons Raw Materials
    │   │       ├── li > a.industrySelect#17 ng-click="setIndustry(17)" — House Raw Materials
    │   │       └── li.last > a (link to /en/economy/buy-company/{countryId}) — Companies
    │   └── div#minReq (initially hidden, shown when industry is selected)
    │       └── div.product_quality_selector.q7
    │           ├── a[quality=1] ng-click="getOffers(1, 1)" — Q1 (always visible)
    │           ├── a[quality=2] ng-click="getOffers(2, 1)" — Q2 (hidden by default)
    │           ├── a[quality=3..7] — Q3–Q7 (hidden by default)
    │           └── div#product_tip (quality tooltip with effect/durability info)
    ├── div.listing_container
    │   ├── div.default_loading_style (loading spinner)
    │   ├── div.list_products
    │   │   └── table
    │   │       ├── thead > tr.m_mainh (Product, Seller, Supply, Price, Quantity, Buy, Share)
    │   │       └── tbody > tr (ng-repeat="item in marketplace track by $index")
    │   │           ├── td.m_product (product icon + import badge)
    │   │           ├── td.m_provider (seller name with hovercard link)
    │   │           ├── td.m_stock (available quantity)
    │   │           ├── td.m_price (price with currency, bestValue tooltip for food/weapons)
    │   │           ├── td.m_quantity (quantity input with +/−/max buttons)
    │   │           ├── td.m_buy (Buy button → ng-click="buyOffer(...)")
    │   │           └── td.m_share (copy link button)
    │   └── ul.pager (pagination, shown when totalPages > 1)
    │       ├── li > a.first (first page)
    │       ├── li > a.prev (previous page)
    │       ├── li (ng-repeat="page in settings.listPagination | limitTo: 5")
    │       ├── li > a.next (next page)
    │       └── li > a.last (last page)
    └── [inline scripts with filters, industryJSON, attributeIconPath]
```

### Embedded JavaScript Variables

#### `filters` — Initial Marketplace State

```json
{
  "countryId": 72,
  "industryId": 1,
  "quality": 1,
  "entityType": "citizen",
  "companyId": 0,
  "orderBy": "price_asc",
  "page": 1
}
```

Used by `MarketplaceController` to initialize the AJAX request to `marketplaceAjax`.

#### `industryJSON` — Complete Industry Definitions (29 entries)

Defines all 29 industry types with their quality levels, effects, durability, and tokens. This is the master reference for product attributes. Summarized:

| ID | Name | Raw? | Token | Type | Quality Levels |
|----|------|------|-------|------|----------------|
| 1 | Food | No | `FOOD` | grass | Q0–Q7, Q10 (Energy Bar), Q11 (Double Energy Bar), Q12 (Treat), Q13–Q18 (event foods) |
| 2 | Weapons | No | `WEAPON` | stone | Q0–Q7, Q8, Q10 (Bazooka), Q11, Q21–Q22 (Bombs), Q51–Q52, Q100, Q211–Q216 |
| 3 | Moving Tickets | No | `TICKET` | — | Q1–Q5 |
| 4 | House | No | `HOUSE` | wood | Q1–Q5 (Q6–Q7 defined but effect=0) |
| 5 | Hospital | No | `HOSPITAL` | — | Q1–Q5 (legacy, not on marketplace) |
| 6 | Defense System | No | `DEFENSE` | — | Q1–Q5 (legacy, not on marketplace) |
| 7 | Grain | Yes | `GRAIN` | grass | — |
| 8 | Fruits | Yes | `FRUITS` | grass | — |
| 9 | Fish | Yes | `FISH` | grass | — |
| 10 | Cattle | Yes | `CATTLE` | grass | — |
| 11 | Deer | Yes | `DEER` | grass | — |
| 12 | Iron | Yes | `IRON` | stone | — |
| 13 | Oil | Yes | `OIL` | stone | — |
| 14 | Aluminum | Yes | `ALUMINUM` | stone | — |
| 15 | Saltpeter | Yes | `SALTPETER` | stone | — |
| 16 | Rubber | Yes | `RUBBER` | stone | — |
| 17 | Sand | Yes | `SAND` | wood | — |
| 18 | Clay | Yes | `CLAY` | wood | — |
| 19 | Wood | Yes | `WOOD` | wood | — |
| 20 | Gift | No | `GIFT` | — | Q1, Q11, Q12 |
| 21 | Limestone | Yes | `LIMESTONE` | wood | — |
| 22 | Granite | Yes | `GRANITE` | wood | — |
| 23 | Aircraft Weapons | No | `AIRCRAFT` | composites | Q1–Q7, Q10 (AIM-92 Stinger), Q51 (AIM-7 Grid-Lock) |
| 24 | Magnesium | Yes | `MAGNESIUM` | composites | — |
| 25 | Titanium | Yes | `TITANIUM` | composites | — |
| 26 | Wolfram | Yes | `WOLFRAM` | composites | — |
| 27 | Cobalt | Yes | `COBALT` | composites | — |
| 28 | Neodymium | Yes | `NEODYMIUM` | composites | — |
| 100 | Booster | No | `BOOSTER` | — | Q1 |

> **Important:** `industryJSON` defines theoretical quality levels (e.g., Aircraft Weapons Q1–Q7), but the **marketplace only sells** up to Q5 for Aircraft Weapons, Moving Tickets, and Houses. Higher qualities (Q6+) are special items obtained through other means.

#### `conditionedIndustries` — Minimum Quality Requirements

```json
{"1": 2, "2": 5}
```

Maps industry ID → minimum quality the citizen should buy. Used by the UI to show warnings/recommendations:
- Food (1): minimum Q2 recommended
- Weapons (2): minimum Q5 recommended

#### `empires` — Empire Countries

```json
{"35": 1}
```

Maps country ID → empire status. Empire countries (e.g., Poland=35) may have special marketplace rules or tax structures.

#### Other Variables

| Variable | Type | Description |
|----------|------|-------------|
| `csrfToken` | string | CSRF token for POST requests (same as `_token` parameter) |
| `linkForBuy` | string | Default buy link with hash routing: `/en/economy/marketplace#72/1` |
| `textForBuy` | string | "Buy food" — CTA label |
| `food_remaining` | number | Remaining food consumption limit for the day |
| `smallestFood` | object | `{"q":1,"use":2}` — lowest quality food and its energy restore |
| `isOrganization` | number | 0 = citizen, 1 = organization account |
| `foodIndustryId` | number | 1 |
| `weaponIndustryId` | number | 2 |
| `houseIndustryId` | number | 4 |
| `citizenshipCountry` | number | Citizen's citizenship country ID |
| `sharedOffer` | boolean | `false` — whether page was loaded via a shared offer link |
| `attributeIconPath` | string | `//www.erepublik.net/images/icons/industry` — base path for product icons |

### Raw Material Resource Groups

The `type` field in `industryJSON` attributes groups raw materials by their parent finished product:

| Type | Finished Product | Raw Material IDs |
|------|-----------------|------------------|
| `grass` | Food (1) | 7 (Grain), 8 (Fruits), 9 (Fish), 10 (Cattle), 11 (Deer) |
| `stone` | Weapons (2) | 12 (Iron), 13 (Oil), 14 (Aluminum), 15 (Saltpeter), 16 (Rubber) |
| `wood` | Houses (4) | 17 (Sand), 18 (Clay), 19 (Wood), 21 (Limestone), 22 (Granite) |
| `composites` | Aircraft (23) | 24 (Magnesium), 25 (Titanium), 26 (Wolfram), 27 (Cobalt), 28 (Neodymium) |

On the marketplace, each group is sold under a **single aggregate ID** (the first resource in each group: 7, 12, 17, 24).

### Product Quality Effects

Key product attribute data from `industryJSON`:

**Food (energy restored per use):**
| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 |
|----|----|----|----|----|----|----|
| 2 | 4 | 6 | 8 | 10 | 12 | 20 |

**Weapons (fire power / durability in uses):**
| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 |
|----|----|----|----|----|----|----|
| 20/1 | 40/2 | 60/3 | 80/4 | 100/5 | 120/6 | 200/10 |

**Houses (energy pool / durability in hours / recovery per 6 min):**
| Q1 | Q2 | Q3 | Q4 | Q5 |
|----|----|----|----|----|
| 100/168h/+2 | 160/168h/+2 | 200/168h/+2 | 300/168h/+2 | 400/168h/+2 |

**Aircraft Weapons (air fire power / durability in uses):**
| Q1 | Q2 | Q3 | Q4 | Q5 |
|----|----|----|----|----|
| 20/1 | 40/2 | 60/3 | 80/4 | 100/5 |

**Moving Tickets (energy cost / moving distance in zones):**
| Q1 | Q2 | Q3 | Q4 | Q5 |
|----|----|----|----|----|
| −4/1000 | −3/2000 | −2/3000 | −1/4000 | 0/5000 |

### Stylesheets & Scripts

| Resource | Purpose |
|----------|---------|
| `marketplace.{buildId}.css` | Marketplace-specific styles |
| `marketplace.{buildId}.js` | MarketplaceController and marketplace logic |
| `selector.{buildId}.css` | Country/quality selector styles |
| `select2.{buildId}.js` | Enhanced dropdown (country select) |

### Notes

- **AngularJS Framework:** The page uses AngularJS 1.8.2 (`ng-app="ErpkApp"`). The marketplace functionality is in the `MarketplaceController` which manages state, AJAX calls, and DOM updates.

- **Country Dropdown:** The `<select#countryId>` contains all 74 countries with flag image paths as `title` attributes. The citizen's current country is pre-selected with `selected="selected"`. Changing the country triggers `erepublik.marketplace.redirectToMarket()`.

- **Industry Icons:** Each industry filter displays an icon from `https://www.erepublik.net/images/icons/industry/{id}/default.png` with a tooltip describing the product's purpose.

- **Quality Selector:** Initially hidden (`div#minReq style="display:none"`). Shown when an industry is selected. Quality links Q2–Q7 are hidden by default and revealed based on the selected industry's max quality. The quality tooltip (`div#product_tip`) shows effect, durability, and rest points.

- **Offer Table Columns:**
  - **Product:** Icon with quality indicator; imported products show origin country flag
  - **Seller:** Citizen name (hovercard-enabled link to profile)
  - **Supply:** Available quantity
  - **Price:** Price with taxes, with `bestValue` tooltip for Food (cost per energy) and Weapons (cost per use)
  - **Quantity:** Input field with decrease (−), increase (+), and maximum (⇑) buttons
  - **Buy:** Blue button calling `buyOffer(item.id, item.valueToBuy, item.amount)`
  - **Share:** Copy offer link button

- **Price Display Logic:** Food (industry 1) shows a `bestValue` tooltip in "X currency / energy" format. Weapons (industry 2) show "X currency / use" format. Other industries show price without best-value calculation.

- **Pagination:** Shown only when `settings.totalPages > 1`. Displays up to 5 page numbers with first/prev/next/last navigation.

- **Data Flow:**
  1. Page loads with `filters` variable defining initial state
  2. `MarketplaceController` reads `filters` and calls `POST /en/economy/marketplaceAjax`
  3. Response populates `marketplace` array, rendered by `ng-repeat`
  4. User interactions (industry change, quality change, pagination) trigger new AJAX calls
  5. Buy actions call `POST /en/economy/marketplaceActions`

- **CSRF Token:** Available as `csrfToken` global variable and also in `SERVER_DATA.csrfToken`. Required for all POST requests.

- **Shared Offers:** The page supports deep-linking to specific offers via URL hash (`marketplace#{countryId}/{quality}`). The `sharedOffer` variable indicates if the page was loaded from a shared link.

---

## Get Marketplace Offers

**Method:** POST
**URL:** `/en/economy/marketplaceAjax`
**Auth Required:** Yes

### Description

Retrieves filtered and sorted marketplace offers for a specific product type. Supports country filtering, product quality selection, pagination, and various sorting options. Returns active sell offers with seller details, pricing, and availability.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryId | number | Yes | Country ID where the market is located (e.g., 72 for Lithuania) |
| industryId | number | Yes | Product industry type (see Industry IDs table in Notes) |
| quality | number | Yes | Product quality level (see Quality Ranges table in Notes) |
| orderBy | string | Yes | Sort order: `price_asc`, `price_desc`, `amount_asc`, `amount_desc` |
| currentPage | number | Yes | Page number for pagination (starts at 1) |
| ajaxMarket | number | Yes | AJAX flag, must be set to `1` |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/marketplaceAjax' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'countryId=72&industryId=1&quality=1&orderBy=price_asc&currentPage=1&ajaxMarket=1&_token=YOUR_CSRF_TOKEN'
```

### Example Response

```json
{
  "offers": [
    {
      "id": 77924504,
      "country_id": 72,
      "industry_id": 1,
      "citizen_id": 4641359,
      "amount": 119998,
      "price": 0.861386,
      "customization_level": 1,
      "name": "Z1mo",
      "is_for_export": 0,
      "priceWithTaxes": 0.87
    },
    {
      "id": 77838777,
      "country_id": 72,
      "industry_id": 1,
      "citizen_id": 2475653,
      "amount": 4025,
      "price": 0.915094,
      "customization_level": 1,
      "name": "chudziaczek",
      "is_for_export": 35,
      "priceWithTaxes": 0.97
    },
    {
      "id": 77904984,
      "country_id": 72,
      "industry_id": 1,
      "citizen_id": 5498697,
      "amount": 135552,
      "price": 0.960396,
      "customization_level": 1,
      "name": "Tepliotojas",
      "is_for_export": 0,
      "priceWithTaxes": 0.97
    }
  ],
  "currency": "LTL",
  "can_buy": true,
  "pages": 1
}
```

### Notes

- **Industry IDs (9 tradeable industries):**

  | ID | Industry | Type | Quality Range | Token |
  |----|----------|------|---------------|-------|
  | 1 | Food | Finished product | Q1–Q7 | `FOOD` |
  | 2 | Weapons (Ammunition) | Finished product | Q1–Q7 | `WEAPON` |
  | 3 | Moving Tickets | Finished product | Q1–Q5 | `TICKET` |
  | 4 | Houses | Finished product | Q1–Q5 | `HOUSE` |
  | 23 | Aircraft Weapons | Finished product | Q1–Q5 | `AIRCRAFT` |
  | 7 | Food Raw Materials | Raw material | Q1 only | `GRAIN` |
  | 12 | Weapon Raw Materials | Raw material | Q1 only | `IRON` |
  | 17 | House Raw Materials | Raw material | Q1 only | `SAND` |
  | 24 | Aircraft Raw Materials | Raw material | Q1 only | `MAGNESIUM` |

  > IDs 5–6, 8–11, 13–16, 18–22, 25+ are **not valid** for the marketplace. The `industryJSON` variable on the marketplace page defines 29 industry entries total (including individual resource types like Grain, Fish, Iron, etc.), but only these 9 appear as marketplace filters.
  >
  > Raw material IDs use the **first resource** of each category as the aggregate marketplace ID (7=Grain→Food RM, 12=Iron→Weapon RM, 17=Sand→House RM, 24=Magnesium→Aircraft RM).

- **Quality Ranges:**
  - Food, Weapons: Q1–Q7
  - Moving Tickets, Houses, Aircraft Weapons: Q1–Q5
  - All raw materials: Q1 only (no quality tiers)
  - Special items exist at higher quality numbers (e.g., Q10=Energy Bar, Q10=Bazooka) but are **not sold** via this marketplace endpoint — they appear in inventory as separate item types

- **Country Filtering:**
  - `countryId` determines which country's market to search
  - Each country has its own marketplace with local currency
  - Prices are displayed in the country's currency (e.g., LTL, USD, EUR, RON)

- **Sorting Options:**
  - `price_asc`: Lowest price first (most common for buyers)
  - `price_desc`: Highest price first
  - `amount_asc`: Lowest stock first
  - `amount_desc`: Highest stock first

- **Offer Fields:**
  - `id`: Unique marketplace offer ID
  - `citizen_id`: Seller's citizen ID
  - `name`: Seller's display name
  - `amount`: Available quantity in stock
  - `price`: Base price per unit (before taxes)
  - `priceWithTaxes`: Final price including import/VAT taxes
  - `is_for_export`: Export tax percentage (0 if no export tax, >0 indicates export offer)
  - `customization_level`: Product quality level (1-7)

- **Pagination:**
  - `pages`: Total number of pages available
  - `currentPage`: Current page being viewed
  - Typically 5-10 offers per page

- **Purchase Eligibility:**
  - `can_buy`: `true` if the citizen is eligible to make purchases
  - May be `false` if banned, restricted, or lacking required citizenship

- **Tax Calculations:**
  - Import tax applies when buying from foreign countries
  - VAT applies based on country settings
  - `priceWithTaxes` includes all applicable taxes
  - Export tax (`is_for_export`) is paid by the seller but affects buyer price

- The CSRF `_token` can be obtained from the main marketplace page source
- This endpoint is used by the marketplace UI for dynamic filtering without page reloads
- Response times are typically fast (<100ms) due to database indexing

---

## Buy from Marketplace

**Method:** POST
**URL:** `/en/economy/marketplaceActions`
**Auth Required:** Yes

### Description

Executes a purchase transaction on the marketplace. Buys a specified quantity of products from a specific offer, deducts currency from the citizen's account, adds products to storage, and returns updated marketplace offers and account balances.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| offerId | number | Yes | Unique ID of the marketplace offer to purchase from |
| amount | number | Yes | Quantity of products to buy (must not exceed offer's available amount) |
| orderBy | string | Yes | Sort order for refreshed offers: `price_asc`, `price_desc`, `amount_asc`, `amount_desc` |
| currentPage | number | Yes | Current page number for refreshed offer list |
| buyAction | number | Yes | Action flag, must be set to `1` for buy action |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/marketplaceActions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'offerId=77924504&amount=1&orderBy=price_asc&currentPage=1&buyAction=1&_token=YOUR_CSRF_TOKEN'
```

### Example Response

```json
{
  "error": false,
  "message": "You have successfully bought 1 product for 0.87 LTL.",
  "offerUpdate": {
    "offers": [
      {
        "id": 77924504,
        "country_id": 72,
        "industry_id": 1,
        "citizen_id": 4641359,
        "amount": 119996,
        "price": 0.861386,
        "customization_level": 1,
        "name": "Z1mo",
        "is_for_export": 0,
        "priceWithTaxes": 0.87
      },
      {
        "id": 77838777,
        "country_id": 72,
        "industry_id": 1,
        "citizen_id": 2475653,
        "amount": 4025,
        "price": 0.915094,
        "customization_level": 1,
        "name": "chudziaczek",
        "is_for_export": 35,
        "priceWithTaxes": 0.97
      }
    ],
    "currency": "LTL",
    "can_buy": true,
    "pages": 1
  },
  "currency": 6274006,
  "gold": 72916
}
```

### Notes

- **Transaction Processing:**
  - Deducts `amount × priceWithTaxes` from citizen's currency balance
  - Adds purchased products to citizen's storage
  - Updates the offer's available amount (decreased by purchase quantity)
  - If offer amount reaches 0, it's removed from marketplace

- **Response Fields:**
  - `error`: `false` on success, `true` on failure
  - `message`: Success message with purchase details (quantity, total cost, currency)
  - `offerUpdate`: Refreshed marketplace offers list (same format as `marketplaceAjax` endpoint)
  - `currency`: Updated citizen's local currency balance (in smallest units, divide by 100)
  - `gold`: Updated citizen's Gold balance (in smallest units, divide by 100)

- **Currency Handling:**
  - Purchases are made in the market country's local currency
  - If citizen lacks sufficient currency, the transaction fails with an error
  - Currency balance is returned in hundredths (e.g., `6274006` = 62,740.06 LTL)
  - Gold balance is also returned in hundredths (e.g., `72916` = 729.16 Gold)

- **Offer Updates:**
  - The `offerUpdate.offers` array reflects the updated marketplace after purchase
  - The purchased offer's `amount` is decreased by the purchase quantity
  - Offers are re-sorted according to the `orderBy` parameter
  - This allows the UI to refresh without making a separate `marketplaceAjax` call

- **Error Cases:**
  - Insufficient currency: `"error": true, "message": "You don't have enough money"`
  - Insufficient stock: `"error": true, "message": "Insufficient stock available"`
  - Invalid offer ID: `"error": true, "message": "Offer not found"`
  - Storage full: `"error": true, "message": "Your storage is full"`
  - Invalid CSRF token: Returns HTTP 403 or token validation error

- **Storage Management:**
  - Purchased items are automatically added to citizen's storage
  - Storage has a capacity limit (default 5000 units, expandable via Shop)
  - Each product type (Food Q1, Weapons Q5, etc.) counts toward the limit
  - If storage is full, the purchase will fail

- **Rate Limiting:**
  - No explicit rate limit, but rapid purchases may trigger anti-bot checks
  - Recommended to add small delays between automated purchases

- The CSRF `_token` can be obtained from the marketplace page source
- This endpoint is typically called after selecting an offer from the `marketplaceAjax` results
- The `orderBy` and `currentPage` parameters ensure UI state is maintained after purchase

---

## Get My Market Offers

**Method:** GET
**URL:** `/en/economy/myMarketOffers`
**Auth Required:** Yes

### Description

Retrieves all active marketplace offers (sell listings) created by the authenticated citizen. Returns an array of offers with product details, pricing, stock levels, and sales statistics. If the citizen has no active offers, returns an empty array.

### Parameters

No parameters required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/economy/myMarketOffers' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

**Empty offers (no active listings):**
```json
[]
```

**With active offers:**
```json
[
  {
    "id": 77924504,
    "country_id": 72,
    "industry_id": 1,
    "quality": 1,
    "amount": 119998,
    "price": 0.861386,
    "stock": 120000,
    "sold": 2,
    "created_at": "2026-01-24 10:30:00",
    "updated_at": "2026-01-25 08:15:00",
    "currency": "LTL",
    "product_name": "Food Q1"
  },
  {
    "id": 77838777,
    "country_id": 72,
    "industry_id": 2,
    "quality": 7,
    "amount": 4025,
    "price": 15.50,
    "stock": 5000,
    "sold": 975,
    "created_at": "2026-01-23 14:20:00",
    "updated_at": "2026-01-25 06:45:00",
    "currency": "LTL",
    "product_name": "Ammunition Q7"
  }
]
```

### Notes

- **Response Format:**
  - Returns an empty array `[]` if the citizen has no active marketplace offers
  - Returns an array of offer objects if the citizen has active listings

- **Offer Fields:**
  - `id`: Unique marketplace offer ID
  - `country_id`: Country where the offer is listed (marketplace location)
  - `industry_id`: Product industry type (1=Food, 2=Weapons, 23=Aircraft, 4=Houses)
  - `quality`: Product quality level (1-7 for most products, null for raw materials)
  - `amount`: Current available stock (decreases as items are purchased)
  - `price`: Price per unit (in country's local currency)
  - `stock`: Original stock when offer was created
  - `sold`: Number of items sold from this offer
  - `created_at`: Timestamp when offer was created
  - `updated_at`: Timestamp of last modification (price change, stock update)
  - `currency`: Currency code (LTL, USD, EUR, RON, etc.)
  - `product_name`: Human-readable product name

- **Use Cases:**
  - Check status of your active marketplace listings
  - Monitor sales and remaining stock
  - Identify which offers need restocking
  - Track pricing and sales performance

- **Marketplace Management:**
  - Offers remain active until stock is depleted or manually removed
  - Price can be updated without recreating the offer
  - Stock can be added to existing offers
  - Multiple offers for the same product type can exist simultaneously

- **Industry IDs:**
  - 1: Food (Q1–Q7)
  - 2: Weapons/Ammunition (Q1–Q7)
  - 3: Moving Tickets (Q1–Q5)
  - 4: Houses (Q1–Q5)
  - 23: Aircraft Weapons (Q1–Q5)
  - 7, 12, 17, 24: Raw Materials (Q1 only)

- **Related Endpoints:**
  - Use `marketplaceAjax` to browse offers from all sellers
  - Use `marketplaceActions` to create new offers or update existing ones
  - This endpoint is read-only (GET) - modifications require POST actions

- This endpoint is used by the "My Offers" section on the marketplace/inventory pages
- Response is typically very fast (<50ms) since it queries a single citizen's offers
- No pagination - all active offers are returned in a single response

---

## Get Inventory

**Method:** GET
**URL:** `/en/economy/inventory-json`
**Auth Required:** Yes

### Description

Retrieves the complete inventory of the authenticated citizen, including all currencies, items (food, weapons, raw materials), boosters, active enhancements, items in production, and assembly components. This is a comprehensive endpoint that provides a full snapshot of the player's resources and possessions.

### Parameters

No parameters required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/economy/inventory-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

```json
[
  {
    "id": "mainStorage",
    "title": "Storage",
    "items": [
      {
        "id": "gold",
        "industryId": 1000,
        "quality": 1,
        "name": "Gold",
        "type": "currency",
        "rarity": "epic",
        "icon": "https://www.erepublik.net/images/icons/rewards/128px/gold.png",
        "attributes": {
          "canDonate": true,
          "currencyId": 62,
          "currency": true
        },
        "tooltip": {
          "attributes": [
            {
              "type": "quantity",
              "text": "Quantity",
              "value": "72,916"
            }
          ]
        },
        "description": "eRepublik's universal currency",
        "amount": 72916,
        "amountDisplay": "⁠72.9k⁠"
      },
      {
        "id": "currency",
        "industryId": 1000,
        "quality": null,
        "name": "Currency",
        "type": "currency",
        "rarity": "rare",
        "attributes": {
          "canDonate": true,
          "currencyId": 1,
          "currency": true
        },
        "description": "Your country's currency",
        "amount": 6274006,
        "amountDisplay": "⁠6.27M⁠"
      },
      {
        "id": "1_3",
        "industryId": 1,
        "quality": 3,
        "name": "Food Q3",
        "type": "food",
        "rarity": "common",
        "attributes": {
          "canBeSold": true,
          "food": true,
          "manufactured": true,
          "storage": 2332968,
          "energy": "⁠+6⁠",
          "refillValue": "⁠+6⁠",
          "highStorage": true
        },
        "description": "Consuming food recovers your Energy",
        "amount": 2332968,
        "amountDisplay": "⁠2.33M⁠",
        "amountForSale": 2332968
      },
      {
        "id": "2_7",
        "industryId": 2,
        "quality": 7,
        "name": "Ammunition Q7",
        "type": "groundWeapon",
        "rarity": "epic",
        "attributes": {
          "canBeSold": true,
          "groundWeapon": true,
          "manufactured": true,
          "storage": 47619,
          "durability": "10 uses",
          "battleDamageGnd": "⁠+200%⁠"
        },
        "description": "Using Ammunition improves your damage in battles",
        "uses": 476183,
        "amount": 47619,
        "amountDisplay": "⁠47.6k⁠",
        "amountForSale": 47618
      }
    ],
    "status": {
      "usedStorage": 4148795,
      "totalStorage": 5622000,
      "availableStorage": 1473205,
      "storagePercentage": "73.8%",
      "color": "yellow"
    }
  },
  {
    "id": "boosters",
    "title": "Boosters",
    "items": [
      {
        "id": "100_damage_10_7200",
        "industryId": 100,
        "quality": 10,
        "name": "+100% Ground Damage",
        "type": "booster",
        "rarity": "rare",
        "attributes": {
          "booster": "damage",
          "duration": "2h",
          "durationSeconds": 7200,
          "battleDamageGnd": "+100%",
          "battleDamage": "+100%",
          "damage": 100,
          "actionable": true
        },
        "description": "Hit 100% stronger in Ground Battles for 2 hours",
        "amount": 486,
        "amountDisplay": "⁠486⁠",
        "actionable": {
          "url": "https://www.erepublik.com/en/main/activateBooster",
          "params": {
            "type": "damage",
            "quality": 10,
            "duration": 7200,
            "amount": 1
          },
          "maxAmount": 486,
          "confirmType": "inline"
        }
      }
    ]
  },
  {
    "id": "activeEnhancements",
    "title": "Active Boosters",
    "items": [
      {
        "id": "4_1_active",
        "industryId": 4,
        "quality": 1,
        "name": "House Q1",
        "type": "house",
        "rarity": "common",
        "attributes": {
          "energyPool": "⁠+100⁠",
          "active": {
            "activeTimeLeft": 701048,
            "activeUntil": 1770053964,
            "showProgress": true,
            "progress": 1.29,
            "state": null
          },
          "overtimePoints": "⁠+1/h⁠",
          "residenceBonus": "active"
        },
        "description": null,
        "amount": 701048,
        "amountDisplay": "⁠8d⁠"
      }
    ]
  },
  {
    "id": "inProduction",
    "title": "In Production",
    "items": [
      {
        "id": "1_3_inProduction",
        "industryId": 1,
        "quality": 3,
        "name": "Food Q3",
        "type": "food",
        "rarity": "common",
        "attributes": {
          "food": true,
          "inProduction": true,
          "component": true,
          "energy": "⁠+6⁠",
          "refillValue": "⁠+6⁠"
        },
        "description": "Consuming food recovers your Energy",
        "amount": 0.71,
        "amountDisplay": "⁠71%⁠"
      }
    ]
  },
  {
    "id": "assembly",
    "title": "Assembly",
    "items": [
      {
        "finalProduct": {
          "id": "2_10",
          "industryId": 2,
          "quality": 10,
          "name": "Bazooka",
          "type": "groundWeapon",
          "rarity": "epic",
          "attributes": {
            "canBeSold": true,
            "groundWeapon": true,
            "durability": "3 uses",
            "fixedDamage": true,
            "firepowerGnd": "10k"
          },
          "description": null,
          "uses": 13608,
          "amount": 4536,
          "amountDisplay": "⁠4.53k⁠",
          "amountForSale": 4536
        },
        "components": [
          {
            "id": "collection_part_4",
            "industryId": 1000,
            "quality": null,
            "name": "Bazooka Scope",
            "type": "miscellaneous",
            "rarity": "common",
            "attributes": {
              "miscellaneous": true,
              "token": true,
              "component": true
            },
            "description": "One of the 5 parts required to assemble Bazookas.",
            "amount": 4536,
            "amountDisplay": "⁠4.53k⁠"
          }
        ],
        "buttonText": "Build Bazooka",
        "canAssemble": true,
        "componentsPerItem": 1,
        "actionable": {
          "url": "https://www.erepublik.com/en/main/assemble",
          "params": {
            "itemLevel": 1,
            "isAjax": true
          }
        }
      }
    ]
  }
]
```

### Notes

- **Inventory Structure:**
  - The response is an array of inventory sections (Storage, Boosters, Active Boosters, In Production, Assembly)
  - Each section has an `id`, `title`, and `items` array
  - The mainStorage section also includes a `status` object with storage capacity information

- **Main Storage Section:**
  - Contains all currencies (Gold, Country Currency, Game Tokens, Loyalty Points)
  - Contains all consumable items (Food Q1-Q11, Weapons Q1-Q7, Aircraft Q5, Houses, etc.)
  - Contains raw materials (Food Raw, Weapon Raw, House Raw, Aircraft Raw)
  - Contains special items (Vehicle Blueprints, Tokens, etc.)
  - Storage status shows: `usedStorage`, `totalStorage`, `availableStorage`, `storagePercentage`, `color`
  - Color indicates storage health: `green` (low), `yellow` (medium), `red` (nearly full)

- **Item Types:**
  - `currency`: Gold, Country Currency, Game Tokens, Loyalty Points
  - `food`: Food Q1-Q11, Energy Bars, Double Energy Bars
  - `groundWeapon`: Ammunition Q1-Q7, Bazookas, Bombs, Rockets, Missiles
  - `airWeapon`: Air-to-Air Missiles Q1-Q5
  - `house`: Houses Q1-Q5
  - `ticket`: Moving Tickets Q1-Q5
  - `raw`: Raw materials for production (Food, Weapon, House, Aircraft)
  - `booster`: Temporary combat/production boosters
  - `miscellaneous`: Tokens, Blueprints, special items
  - `groundBomb`: Small Bomb, Big Bomb, Cruise Missile
  - `vehicle_blueprint`: Tank and Aircraft blueprints

- **Rarity Levels:**
  - `common`: Basic items
  - `uncommon`: Enhanced items
  - `rare`: High-value items
  - `epic`: Very rare items
  - `legendary`: Unique/premium items

- **Item Attributes:**
  - `canBeSold`: Item can be sold on marketplace
  - `canDonate`: Item can be donated
  - `manufactured`: Item is produced in companies
  - `storage`: Storage space occupied by this item stack
  - `highStorage`: Warning flag for items using excessive storage
  - `energy`: Energy restored when consumed (food)
  - `durability`: Number of uses (weapons)
  - `battleDamageGnd/Air`: Combat damage boost percentage
  - `fixedDamage`: Weapon deals fixed damage (not affected by boosters)
  - `firepowerGnd/Air`: Fixed damage value
  - `temporary`: Time-limited item with expiration data
  - `actionable`: Item can be activated/used (boosters, houses)
  - `component`: Used in assembly/production

- **Boosters Section:**
  - Contains all available (inactive) boosters
  - Types: Damage boosters, Speed boosters, Deploy Size boosters, Influence boosters, Rank boosters, Prestige boosters, Ghost boosters
  - Each booster has `actionable` object with activation URL and parameters
  - Duration ranges from 1 minute to 24 hours
  - Quality levels indicate boost strength (e.g., Q10 = +100%, Q15 = +150%)

- **Active Enhancements Section:**
  - Shows currently active temporary effects (Houses, Pack Boosters)
  - `active.activeTimeLeft`: Seconds remaining until expiration
  - `active.activeUntil`: Unix timestamp of expiration
  - `active.showProgress`: Whether to show progress bar
  - `active.progress`: Progress percentage (can exceed 100%)
  - Houses provide energy pool bonuses and overtime points

- **In Production Section:**
  - Shows items currently being produced in companies
  - `amount` represents production progress as a decimal (0.0 to 1.0)
  - `amountDisplay` shows percentage (e.g., "71%" for 0.71)
  - Production completes when `amount` reaches 1.0

- **Assembly Section:**
  - Contains items that can be assembled from components (Bazookas, Rockets)
  - `finalProduct`: The assembled item
  - `components`: Array of required parts with current quantities
  - `canAssemble`: `true` if all components are available in sufficient quantity
  - `componentsPerItem`: Number of components needed per assembled item
  - Bazookas require 5 parts (Stock, Barrel, Scope, Projectile, Trigger Kit)
  - Rockets require 20 ammunition items (Q1-Q6)

- **Amount Display:**
  - `amount`: Raw numeric value
  - `amountDisplay`: Formatted display string (e.g., "⁠72.9k⁠", "⁠2.33M⁠")
  - Display uses K (thousands), M (millions) abbreviations

- **Currency IDs:**
  - 1: Country Currency
  - 62: Gold

- **Industry IDs:**
  - 1: Food
  - 2: Weapons (Ammunition)
  - 4: Houses
  - 7: Food Raw Materials
  - 12: Weapon Raw Materials
  - 17: House Raw Materials
  - 23: Aircraft Weapons (Air-to-Air Missiles)
  - 24: Aircraft Raw Materials
  - 100: Boosters and special items
  - 1000: Currencies and miscellaneous items

- This endpoint is used by the Inventory page to display all player possessions
- Very comprehensive response (can be 50KB+ for players with large inventories)
- No pagination - returns entire inventory in a single response
- Response time varies based on inventory size (typically 200-500ms)

---

## Get Job Market Listings

**Method:** GET
**URL:** `/en/economy/job-market-json/{countryId}/{page}/{sortOrder}`
**Auth Required:** Yes

### Description

Retrieves paginated job market listings for a specific country, showing available job offers sorted by salary. Returns information about the citizen's current employment status, available jobs from employers, salary details (gross/net), and whether the citizen is eligible to apply.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryId | number | Yes | Country ID where jobs are located (e.g., 72 for Lithuania) |
| page | number | Yes | Page number for pagination (starts at 1) |
| sortOrder | string | Yes | Sort order: `asc` (lowest salary first) or `desc` (highest salary first) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/economy/job-market-json/72/1/desc' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

```json
{
  "isEmployed": true,
  "isOrganization": false,
  "isFromThisCountry": true,
  "citizenId": 4690052,
  "currencyName": "LTL",
  "employer": {
    "id": 2580095,
    "name": "Greenday_1989",
    "salary": 7500,
    "netSalary": 7425,
    "message": "You already work for <a href='//www.erepublik.com/en/citizen/profile/2580095'>Greenday_1989</a>. To apply for this job you have to quit your current job."
  },
  "jobs": [
    {
      "citizen": {
        "id": 1460339,
        "name": "pogonici",
        "img": "https://cdnt.erepublik.net/.../3f672edbae92cd310dd474294414e0cf.jpg",
        "countryId": 24
      },
      "companyName": "Love is in the air 33",
      "salary": 7111,
      "netSalary": 7039.89,
      "salaryLimit": 0,
      "currency": "LTL"
    },
    {
      "citizen": {
        "id": 2629153,
        "name": "Bob_Marley_BG",
        "img": "https://cdnt.erepublik.net/.../d7d99181731a49635cadd3a94abb9aa1.jpg",
        "countryId": 42
      },
      "companyName": "Riding High",
      "salary": 7040,
      "netSalary": 6969.6,
      "salaryLimit": 0,
      "currency": "LTL"
    }
  ],
  "pages": 20
}
```

### Notes

- **Employment Status:**
  - `isEmployed`: `true` if citizen is currently employed
  - `isOrganization`: `true` if the citizen account is an organization (not a regular citizen)
  - `isFromThisCountry`: `true` if citizen has citizenship in the job market's country
  - `citizenId`: Authenticated citizen's ID
  - `currencyName`: Country's currency code (LTL, USD, EUR, RON, etc.)

- **Current Employer:**
  - Only present if `isEmployed: true`
  - `employer.id`: Current employer's citizen ID
  - `employer.name`: Current employer's name
  - `employer.salary`: Gross salary at current job
  - `employer.netSalary`: Net salary after tax deduction
  - `employer.message`: HTML message indicating employment status (citizen must quit current job to apply)

- **Job Listings:**
  - Array of available job offers from employers
  - `citizen`: Employer's profile information (ID, name, avatar, country)
  - `companyName`: Name of the company offering the job
  - `salary`: Gross salary offered (before tax)
  - `netSalary`: Net salary after tax deduction (actual amount received)
  - `salaryLimit`: Maximum salary budget restriction (0 = no limit)
  - `currency`: Currency code for salary payment

- **Pagination:**
  - `pages`: Total number of pages available
  - Typically 10 jobs per page
  - Use `page` parameter to navigate through results

- **Sorting:**
  - `desc`: Highest salary first (most common, shows best-paying jobs)
  - `asc`: Lowest salary first (rarely used)

- **Tax Calculations:**
  - Tax rate varies by country (typically 1% in most countries)
  - `netSalary = salary - (salary × taxRate)`
  - Example: 7111 salary with 1% tax = 7039.89 net salary

- **Country IDs (Common):**
  - 24: Argentina
  - 42: Bulgaria
  - 72: Lithuania
  - 79: Romania
  - (See other endpoints for more country IDs)

- **Application Flow:**
  - If employed, citizen must quit current job first
  - Quitting typically requires navigating to profile and clicking "Quit Job"
  - After quitting, citizen can apply for any job in the market
  - Application is instant (no approval process)

- **Related Endpoints:**
  - Use `/en/main/job-data` to get current employment details
  - Use `/en/economy/work` to work at current job
  - This endpoint is read-only (GET) - job applications require separate POST action

- Response time is typically fast (<100ms) since data is cached and indexed
- Job listings update in real-time as employers create or remove offers
- Only shows jobs in companies that have available positions and sufficient funds

---

## Game Tokens Market Retrieve

**Method:** POST
**URL:** `/en/economy/gameTokensMarketAjax`
**Auth Required:** Yes

### Description

Retrieves game tokens market data for a specific country, showing top market offers, citizen's token balance, active sell offers, daily purchase limits, and market statistics. Game Tokens are a special currency used for premium features and can be bought/sold on the market using country currency.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| action | string | Yes | Must be set to `retrieve` to fetch market data |
| countryId | number | Yes | Country ID where the market is located (e.g., 72 for Lithuania) |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/gameTokensMarketAjax' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'action=retrieve&countryId=72&_token=YOUR_CSRF_TOKEN'
```

### Example Response

```json
{
  "error": false,
  "topOffers": [
    {
      "sellers": "1 Seller(s)",
      "amount": 179,
      "price": 30999,
      "id": "offer_1",
      "canBuy": true,
      "sellerCountries": [34]
    },
    {
      "sellers": "2 Seller(s)",
      "amount": 1302,
      "price": 31000,
      "id": "offer_2",
      "canBuy": false,
      "sellerCountries": [38, 65]
    },
    {
      "sellers": "1 Seller(s)",
      "amount": 9,
      "price": 31148,
      "id": "offer_3",
      "canBuy": false,
      "sellerCountries": [13]
    },
    {
      "sellers": "1 Seller(s)",
      "amount": 5,
      "price": 31149,
      "id": "offer_4",
      "canBuy": false,
      "sellerCountries": [53]
    },
    {
      "sellers": "1 Seller(s)",
      "amount": 938,
      "price": 31150,
      "id": "offer_5",
      "canBuy": false,
      "sellerCountries": [29]
    }
  ],
  "citizenData": {
    "availableTokens": 249,
    "myOffers": [],
    "offersLimit": 3
  },
  "histogram": [],
  "stats": "There are 4591 items for sale, available from 31 sellers. Only the Top 5 offers are shown.",
  "dailyLimit": "You acquire a maximum of 148 Game Tokens per day.",
  "dailyLimitRemaining": 148
}
```

### Notes

- **Top Offers:**
  - Only top 5 offers are displayed (sorted by price, lowest first)
  - `sellers`: Count of sellers offering at this price point
  - `amount`: Total quantity available at this price
  - `price`: Price per game token in country currency (displayed in hundredths, divide by 100)
  - `canBuy`: `true` if citizen can purchase from this offer (depends on citizenship, restrictions)
  - `sellerCountries`: Array of country IDs where sellers are from
  - `id`: Offer identifier (e.g., "offer_1", "offer_2")

- **Citizen Data:**
  - `availableTokens`: Current game tokens owned by the citizen
  - `myOffers`: Array of citizen's active sell offers (empty if none)
  - `offersLimit`: Maximum number of sell offers allowed (typically 3)

- **Market Statistics:**
  - `stats`: Human-readable summary of total market supply and sellers
  - `histogram`: Price distribution data (typically empty in basic retrieve action)
  - Total market size shown in stats (e.g., "4591 items for sale from 31 sellers")

- **Daily Purchase Limits:**
  - `dailyLimit`: Maximum game tokens that can be purchased per day (displayed as text)
  - `dailyLimitRemaining`: Remaining tokens available for purchase today (resets at eRepublik day change)
  - Prevents market manipulation by limiting daily purchases
  - Limit varies by citizen level/VIP status

- **Country IDs (Common):**
  - 13: Spain
  - 29: Bosnia and Herzegovina
  - 34: China
  - 38: Croatia
  - 53: Indonesia
  - 65: Poland
  - 72: Lithuania
  - (See other endpoints for more country IDs)

- **Game Tokens Usage:**
  - Premium currency used for VIP subscription, special items, and services
  - Tradeable between citizens via the market
  - Cannot be converted back to Gold
  - Prices fluctuate based on supply/demand

- **Related Actions:**
  - `action=buy`: Purchase game tokens from market offers
  - `action=sell`: Create a sell offer for game tokens
  - `action=cancel`: Cancel an existing sell offer

- The CSRF `_token` can be obtained from the game tokens market page source
- Response time is typically fast (<100ms) due to caching
- Market data updates in real-time as transactions occur
- Only citizens with citizenship in the specified country can buy/sell in that market

---

## Manage Employees Data

**Method:** GET
**URL:** `/en/economy/manage-employees-json`
**Auth Required:** Yes

### Description

Retrieves comprehensive employee management data for the authenticated citizen's companies, including individual employee work histories, salary details, job offer information, and daily statistics. This endpoint provides a complete overview of employee performance, attendance, and company salary budget management.

### Parameters

No parameters required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/economy/manage-employees-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

```json
{
  "settings": {
    "currency": "LTL",
    "dayNumbers": [6635, 6636, 6637, 6638, 6639, 6640, 6641],
    "daysToShow": 7
  },
  "employees": [
    {
      "citizenId": 5385502,
      "name": "SP rider",
      "avatar": "https://cdnt.erepublik.net/.../de8c10b2e136a296fbe88a5f4c8671ff.jpg",
      "salary": 2100,
      "hiredOn": 5372,
      "workHistory": [1, 1, 2, 1, 1, 1, 1],
      "salaryHistory": [2100, 2100, 4200, 2100, 2100, 2100, 2100],
      "totalWorks": 8,
      "totalSalary": 16800
    }
  ],
  "overview": {
    "jobOffer": {
      "hasJobOffer": true,
      "currentOffer": {
        "citizenId": 4690052,
        "countryId": 72,
        "numberOfJobs": 100,
        "salary": 2100,
        "createdAt": 1745836141
      },
      "minimumSalary": 1,
      "maxNumberOfJobs": 1335
    },
    "salaryLimit": 210000,
    "totalSalaries": {
      "amount": 2100,
      "isCritical": false
    },
    "numEmployees": 1,
    "maxEmployees": 1336,
    "employeePresence": 100,
    "employeeStats": {
      "dailyStats": {
        "6635": {
          "employeePresence": 100,
          "numEmployees": 1,
          "presentEmployees": 1,
          "worksReceived": 1,
          "payedSalaries": 2100,
          "averageSalaryPerWork": 2100,
          "averageWorksPerEmployee": 1
        },
        "6636": {
          "employeePresence": 100,
          "numEmployees": 1,
          "presentEmployees": 1,
          "worksReceived": 1,
          "payedSalaries": 2100,
          "averageSalaryPerWork": 2100,
          "averageWorksPerEmployee": 1
        }
      },
      "overall": {
        "employeePresence": 100,
        "worksReceived": 8,
        "payedSalaries": 16800,
        "averageSalaryPerWork": 2100,
        "averageWorksPerEmployee": 1.14
      }
    }
  }
}
```

### Notes

- **Settings:**
  - `currency`: Currency code used for salaries (LTL, USD, EUR, etc.)
  - `dayNumbers`: Array of eRepublik day numbers (eDay) for the displayed period
  - `daysToShow`: Number of days shown in history (typically 7 days)

- **Employee Data:**
  - `citizenId`: Unique identifier of the employee
  - `name`: Employee's display name
  - `avatar`: Profile picture URL
  - `salary`: Current daily salary offered
  - `hiredOn`: eDay number when employee was hired
  - `workHistory`: Array showing number of works per day (corresponds to `dayNumbers` array)
  - `salaryHistory`: Array showing total salary paid per day (corresponds to `dayNumbers` array)
  - `totalWorks`: Total works received from this employee during the displayed period
  - `totalSalary`: Total salary paid to this employee during the displayed period

- **Job Offer Information:**
  - `hasJobOffer`: `true` if an active job offer exists
  - `currentOffer.citizenId`: Company owner's citizen ID
  - `currentOffer.countryId`: Country where the job offer is posted
  - `currentOffer.numberOfJobs`: Number of available positions
  - `currentOffer.salary`: Offered salary per work
  - `currentOffer.createdAt`: Unix timestamp when offer was created
  - `minimumSalary`: Minimum allowed salary (typically 1)
  - `maxNumberOfJobs`: Maximum employees allowed (based on company level/holding capacity)

- **Salary Budget:**
  - `salaryLimit`: Maximum daily salary budget before warnings appear
  - `totalSalaries.amount`: Total daily salary expenses
  - `totalSalaries.isCritical`: `true` if salary expenses exceed safe thresholds

- **Employee Capacity:**
  - `numEmployees`: Current number of employees
  - `maxEmployees`: Maximum employee capacity (based on company buildings)
  - `employeePresence`: Overall attendance percentage (0-100)

- **Daily Statistics:**
  - `dailyStats`: Object keyed by eDay number with per-day metrics
  - `employeePresence`: Percentage of employees who worked that day
  - `presentEmployees`: Count of employees who worked
  - `worksReceived`: Total works completed that day (can exceed employee count due to overtime)
  - `payedSalaries`: Total salaries paid that day
  - `averageSalaryPerWork`: Average salary paid per work completed
  - `averageWorksPerEmployee`: Average works per employee (includes overtime)

- **Overall Statistics:**
  - `overall`: Aggregated stats across the entire displayed period
  - Includes total works, total salaries, and averages

- **Work History Arrays:**
  - Arrays are ordered chronologically matching the `dayNumbers` array
  - Example: `dayNumbers[0]=6635` corresponds to `workHistory[0]=1` (1 work on day 6635)
  - Works can exceed 1 per day if employee worked overtime
  - Salary history shows gross salary paid (includes overtime payments)

- **Use Cases:**
  - Monitor employee attendance and productivity
  - Track salary expenses across time
  - Identify inactive or low-performing employees
  - Manage job offer settings and capacity
  - Calculate average costs and efficiency metrics

- This endpoint is used by the "Manage Employees" page in the Economy section
- Response time is typically fast (<100ms)
- Data covers the last 7 days by default
- Employee data is only visible to the company owner

---

## Activate Booster

**Method:** POST
**URL:** `/en/main/activateBooster`
**Auth Required:** Yes

### Description

Activates a booster from the inventory. Boosters provide temporary combat enhancements such as increased damage, faster hitting speed, or other bonuses. If another booster of the same type is already active, the endpoint returns a confirmation prompt asking whether to replace the existing booster.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| type | string | Yes | Booster type: `speed` (Damage Accelerator), `damage` (Damage Booster), etc. |
| quality | number | Yes | Booster quality level (determines strength: 1=x2, 2=x5, etc. for speed; 10=+100%, 15=+150%, etc. for damage) |
| duration | number | Yes | Duration in seconds (e.g., 180 = 3 minutes, 300 = 5 minutes, 7200 = 2 hours) |
| amount | number | Yes | Quantity to activate (typically 1) |
| unit | string | No | Time unit for display: `m` (minutes), `h` (hours) |
| value | number | No | Display value for duration (e.g., 5 for "5 minutes") |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/activateBooster' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'type=speed&quality=2&duration=300&amount=1&unit=m&value=5&_token=YOUR_CSRF_TOKEN'
```

### Example Response

**Confirmation Required (conflict with existing booster):**
```json
{
  "error": true,
  "status": "error",
  "message": "Please confirm activating this booster",
  "confirmationRequired": true,
  "confirmationText": "You already have another active item of this type.<br><br>\n Are you sure you want to activate this item?"
}
```

**Successful Activation:**
```json
{
  "status": "success",
  "message": "Booster activated"
}
```

### Notes

- **Booster Types:**
  - `speed`: Damage Accelerator (x2, x5, x9 faster hitting)
  - `damage`: Damage Booster (+50%, +100%, +150% damage)
  - `catchup`: Ghost Booster (% of best damage in battle)
  - `deploy_size`: Deployment Size Booster
  - `prestige`: Prestige Points Booster
  - Additional types may exist for other booster categories

- **Quality Levels:**
  - Speed Boosters: 1=x2, 2=x5, 3=x9
  - Damage Boosters: 10=+100%, 15=+150%, 5=+50%
  - Quality determines the strength of the boost

- **Duration:**
  - Short boosters: 60s (1 min), 180s (3 min), 300s (5 min)
  - Medium boosters: 900s (15 min), 1800s (30 min), 3600s (1 hour)
  - Long boosters: 7200s (2 hours), 14400s (4 hours), 86400s (24 hours)

- **Confirmation Logic:**
  - If a booster of the same type is already active, `confirmationRequired: true` is returned
  - Client must prompt user to confirm replacement
  - To proceed after confirmation, resubmit the request (implementation may require additional parameter)
  - Activating replaces the existing booster (remaining time is lost)

- **Booster Stacking:**
  - Boosters of different types can be active simultaneously (e.g., speed + damage)
  - Boosters of the same type cannot stack (activating replaces the active one)
  - Multiple boosters in inventory can be activated sequentially after previous ones expire

- **Inventory Management:**
  - Activated boosters are consumed from inventory (`amount` is deducted)
  - Remaining quantity is updated in the inventory
  - Boosters can be obtained from the Shop, VIP Shop, events, or rewards

- **Expiration:**
  - `expiresAt`: Unix timestamp when the booster effect ends
  - After expiration, the booster effect is removed automatically
  - Citizens can activate a new booster immediately after expiration

- **Related Endpoints:**
  - Use `/en/economy/inventory-json` to retrieve available boosters
  - Use `/en/main/shopItemsJson` to purchase new boosters from the Shop
  - Booster activation is typically done before entering combat or during battles

- The CSRF `_token` can be obtained from the inventory or main page source
- Response time is typically very fast (<50ms)
- This endpoint is used by the Inventory and Active Enhancements UI sections

---

## Activate House

**Method:** POST
**URL:** `/en/economy/activateHouse`
**Auth Required:** Yes

### Description

Activates a house from the inventory to gain its benefits. Houses provide energy pool bonuses and overtime points accumulation. When activated, a house consumes one unit from inventory and charges an activation tax. Only one house can be active at a time, and activating a new house replaces the currently active one (remaining time is lost).

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| type | string | Yes | Must be set to `house` |
| quality | number | Yes | House quality level (1-5): Q1 (+100 energy pool), Q2 (+200), Q3 (+300), Q4 (+400), Q5 (+500) |
| amount | number | Yes | Quantity to activate (typically 1) |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/activateHouse' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'type=house&quality=1&amount=1&_token=YOUR_CSRF_TOKEN'
```

### Example Response

```json
{
  "status": true,
  "message": "Successfully activated house",
  "activationTax": 628.9,
  "currency": 6273068,
  "error": false
}
```

### Notes

- **House Benefits:**
  - **Energy Pool Bonus:** Increases maximum energy capacity
    - Q1: +100 energy pool
    - Q2: +200 energy pool
    - Q3: +300 energy pool
    - Q4: +400 energy pool
    - Q5: +500 energy pool
  - **Overtime Points:** +1 overtime point per hour while active
  - Houses remain active for a duration based on quality (typically days or weeks)

- **Activation Tax:**
  - `activationTax`: Tax amount charged for activating the house (in country currency)
  - Tax is deducted from the citizen's currency balance
  - Tax varies by country and house quality level
  - Example: 628.9 LTL for Q1 house activation

- **Currency Balance:**
  - `currency`: Updated currency balance after activation tax is deducted (in hundredths)
  - Example: `6273068` = 62,730.68 LTL
  - Insufficient currency will result in an error

- **Activation Rules:**
  - Only one house can be active at a time
  - Activating a new house replaces the currently active one
  - Remaining time on the previous house is lost (no refund)
  - The house is consumed from inventory upon activation
  - Duration varies by quality: Q1 (~7 days), Q5 (~30 days)

- **Inventory Management:**
  - Houses must be in inventory before activation
  - Can be purchased from the marketplace or Shop
  - Each activation consumes one house unit
  - Recommended to activate houses before they expire in inventory

- **Active House Status:**
  - Use `/en/economy/inventory-json` to check currently active house
  - Active houses appear in the "Active Enhancements" section
  - Shows remaining time until expiration
  - After expiration, energy pool bonus and overtime points are removed

- **Strategic Use:**
  - Activate houses before extended play sessions
  - Higher quality houses provide better energy pool and longer duration
  - Energy pool increases effective combat capacity
  - Overtime points accumulate hourly for additional work opportunities

- **Error Cases:**
  - Insufficient currency for activation tax
  - No house of specified quality in inventory
  - Invalid CSRF token

- The CSRF `_token` can be obtained from the inventory page source
- Response time is typically very fast (<50ms)
- This endpoint is used by the Inventory UI when clicking "Activate" on a house

---

## Exchange Market Page (Monetary Market)

**Method:** GET
**URL:** `/en/economy/exchange-market/`
**Auth Required:** Yes

### Description

The Monetary Market page where citizens can buy and sell Gold ↔ Country Currency (CC). Returns an HTML page containing embedded JavaScript variables with exchange rate data, current offers table, and form elements for trading. This is a **page endpoint** (returns HTML, not JSON) — structured data is embedded in `<script>` tags.

### Parameters

No URL parameters. The page defaults to showing offers to buy Gold for the citizen's country currency (CC).

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Accept | `text/html,application/xhtml+xml` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/economy/exchange-market/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Page Structure

The HTML page contains these key sections:

1. **Exchange Bar** — Currency switcher (Buy GOLD / Sell CC or vice versa)
2. **Create Form** — Hidden form to create a new exchange offer (amount + exchange rate)
3. **Offers Table** — `<table class="exchange_offers">` with 10 offers per page
4. **Pagination** — `<ul class="pager">` with page links

### Embedded JavaScript Variables

#### `EXCHANGE` — Exchange Configuration
```javascript
var EXCHANGE = {
    culture: 'en',
    update_offer_confirmation_text: "Are you sure you want to update this offer?",
    remove_offer_confirmation_text: "Are you sure you want to remove this offer ?",
    invalid_decimal_value_text: "Please enter a valid amount value with at most 2 decimals.",
    invalid_decimal_value3_text: "Please enter a valid exchange rate value with at most 3 decimals.",
    invalid_amount_value_text: "Invalid amount value.",
    invalid_exchange_rate_text: "Invalid exchange rate.",
    lowestBuyGoldExchangeRate: 1082.000,
    lowestBuyCurrencyExchangeRate: 0.001
}
```

| Field | Type | Description |
|-------|------|-------------|
| `culture` | string | Language code (e.g., `en`) |
| `lowestBuyGoldExchangeRate` | number | Best rate to buy Gold (cheapest Gold price in CC) |
| `lowestBuyCurrencyExchangeRate` | number | Best rate to buy CC (cheapest CC price in Gold) |

#### `sell_currency_data` — The Currency Being Sold
```javascript
var sell_currency_data = {
    "id": 1,
    "name": "LTL",
    "icon": "//www.erepublik.net/images/flags_png/M/Lithuania.png",
    "small_icon": "//www.erepublik.net/images/flags_png/S/Lithuania.png",
    "large_icon": "//www.erepublik.net/images/flags_png/L/Lithuania.png"
};
```

#### `buy_currency_data` — The Currency Being Bought
```javascript
var buy_currency_data = {
    "id": 62,
    "name": "GOLD",
    "icon": "//www.erepublik.net/images/modules/_icons/gold_24.png",
    "small_icon": "//www.erepublik.net/images/modules/_icons/gold_icon.png",
    "large_icon": "//www.erepublik.net/images/modules/_icons/gold_24.png"
};
```

### Hidden Form Fields

| Field | ID | Example Value | Description |
|-------|----|---------------|-------------|
| Buy Currency | `buy_currency` | `62` | Currency ID being bought (62 = GOLD) |
| Sell Currency | `sell_currency` | `1` | Currency ID being sold (1 = LTL) |
| CSRF Token | `_token` | `99e749d8...` | Required for all POST actions |

### Offers Table Structure

Each row in `<table class="exchange_offers">` contains:

| Column | CSS Class | Data | Example |
|--------|-----------|------|---------|
| Citizen | `ex_citizen` | Seller profile link + avatar | `<a href='//...citizen/profile/9736962'>andyherts</a>` |
| Amount | `ex_amount` | Amount of currency for sale | `1.00 GOLD` |
| Rate | `ex_rate` | Exchange rate with currency icons | `1 GOLD = 1082.000 LTL` |
| Buy | `ex_buy` | Input field + buy button | `<button id='purchase_6806828' data-price='1082.000' data-max='1.00'>` |

The buy button's `data-` attributes expose key offer data:
- `id="purchase_{offerId}"` — Offer ID (e.g., `6806828`)
- `data-price` — Exchange rate
- `data-max` — Maximum amount available to buy
- `data-currency` — Currency name

### Stylesheets & Scripts

| Script | Purpose |
|--------|---------|
| `exchange.{timestamp}.js` | Exchange market logic (offers, pagination, CRUD operations) |

### Notes

- **Default View:** Shows "Buy GOLD, Sell CC" for the citizen's country currency
- **Currency Swap:** The "Swap" button switches between buying GOLD for CC vs buying CC for GOLD
- **Pagination:** 10 offers per page, paginated via AJAX (see Exchange Retrieve Offers endpoint)
- **Currency IDs:** `62` = GOLD; country currency IDs vary (e.g., `1` = LTL, see other endpoints for mapping)
- **CSRF Token:** The `_token` hidden field is required for all exchange POST operations (purchase, create, update, delete)
- **All exchange AJAX endpoints** are documented below — they are called by `exchange.js` for all trading operations

---

## Exchange Retrieve Offers

**Method:** POST
**URL:** `/{lang}/economy/exchange/retrieve/`
**Auth Required:** Yes

### Description

Retrieves a page of exchange offers for a given currency. Used for pagination (navigating pages) and for switching between "buy Gold" and "buy CC" views. Returns HTML fragments that replace the offers table on the page.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| currencyId | number | Yes | Currency ID to show offers for (e.g., `62` for GOLD, `1` for LTL) |
| page | number | Yes | Page number (0-indexed) |
| personalOffers | number | No | `1` to show only the citizen's own offers, `0` or omit for all offers |
| show_create_form | number | No | `1` to include the create offer form in the response |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/retrieve/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&currencyId=62&page=0&personalOffers=0'
```

### Example Response

```json
{
  "error": false,
  "buy_mode": "<table>...HTML offers table...</table><ul class='pager'>...</ul>",
  "essentials": "<input id='buy_currency' ... /><input id='sell_currency' ... />",
  "currencyId": "62",
  "page": "0"
}
```

### Notes

- **Response contains HTML fragments** (`buy_mode`, `essentials`) that replace DOM elements on the page
- **Client-side caching:** The `exchange.js` caches responses in a JS object keyed by `currency_{currencyId}_{page}`, so repeated requests for the same page are served from memory
- **Pages are 0-indexed** in the API but displayed as 1-indexed in the UI
- `personalOffers: 1` switches to "My offers" view (showing only the authenticated citizen's offers)
- On error, returns `{ "error": true, "message": "..." }`

---

## Exchange Purchase (Buy from Offer)

**Method:** POST
**URL:** `/{lang}/economy/exchange/purchase/`
**Auth Required:** Yes

### Description

Purchases currency from an existing exchange offer. The citizen buys from another citizen's offer at the listed exchange rate. Requires the offer ID, amount to buy, and the current page/currency context.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| offerId | number | Yes | The offer ID to buy from (from `purchase_{offerId}` button) |
| amount | number | Yes | Amount to buy (max 2 decimal places, cannot exceed `data-max`) |
| page | number | Yes | Current page number (0-indexed) |
| currencyId | number | Yes | Current currency view ID (e.g., `62` for GOLD) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/purchase/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&offerId=6806828&amount=1.00&page=0&currencyId=62'
```

### Example Response (Success)

```json
{
  "error": false,
  "message": "Exchange successful!",
  "gold": { "value": "123.45" },
  "ecash": { "value": "67890.12" },
  "buy_mode": "<table>...updated offers table...</table>",
  "essentials": "<input .../>",
  "hideOffer": false
}
```

### Example Response (Error)

```json
{
  "error": true,
  "message": "Insufficient funds."
}
```

### Notes

- **Amount validation:** Max 2 decimal places, must be positive, cannot exceed offer's `data-max`
- **Balance update:** Response includes updated `gold.value` and `ecash.value` for the citizen's account
- **`hideOffer`:** `true` if the purchased offer is now fully consumed and should be removed from the table
- **Exchange rate** is determined by the offer, not passed as a parameter — the citizen pays at the listed rate
- The confirmation dialog format: `"Buy {amount} {currency} for {total} {otherCurrency}?"`

---

## Exchange Create Offer

**Method:** POST
**URL:** `/{lang}/economy/exchange/create/`
**Auth Required:** Yes

### Description

Creates a new exchange offer on the monetary market. The citizen lists an amount of currency to sell at a specified exchange rate.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| sellCurrencyId | number | Yes | Currency ID being sold (e.g., `1` for LTL) |
| buyCurrencyId | number | Yes | Currency ID being bought (e.g., `62` for GOLD) |
| amount | number | Yes | Amount of sell currency to offer (max 2 decimal places) |
| at_exchange_rate | number | Yes | Exchange rate (max 3 decimal places) |
| page | number | Yes | Current page (always `0` on create) |
| personalOffers | number | Yes | Always `1` (switches to "My offers" view after creation) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/create/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&sellCurrencyId=1&buyCurrencyId=62&amount=100.00&at_exchange_rate=1082.000&page=0&personalOffers=1'
```

### Example Response (Success)

```json
{
  "error": false,
  "message": "Offer created successfully!",
  "gold": { "value": "123.45" },
  "ecash": { "value": "67790.12" },
  "edit_mode": "<div>...my offers HTML...</div>",
  "buy_mode": "<table>...offers table...</table>",
  "essentials": "<input .../>"
}
```

### Notes

- **Rate format:** "1 {sellCurrency} = {at_exchange_rate} {buyCurrency}" — e.g., selling 100 LTL at rate 1082.000 means 1 GOLD = 1082 LTL
- **Amount validation:** Max 2 decimal places, must be positive
- **Exchange rate validation:** Max 3 decimal places, must be positive
- **After creation:** Response updates the citizen's balance (funds are deducted/held) and switches to "My offers" view
- **Confirmation dialog:** `"Sell {amount} {sellCurrency} for {total} {buyCurrency}?"`
- **Offer limit:** Citizens can have a limited number of offers (counter shown as `my_offers_count`)

---

## Exchange Update Offer

**Method:** POST
**URL:** `/{lang}/economy/exchange/update/`
**Auth Required:** Yes

### Description

Updates an existing exchange offer owned by the authenticated citizen. Allows changing the amount and/or exchange rate of the offer.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| offerId | number | Yes | The offer ID to update |
| amount | number | Yes | New amount (max 2 decimal places) |
| at_exchange_rate | number | Yes | New exchange rate (max 3 decimal places) |
| currencyId | number | Yes | Current currency view ID |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/update/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&offerId=6806828&amount=50.00&at_exchange_rate=1083.000&currencyId=62'
```

### Example Response (Success)

```json
{
  "error": false,
  "message": "Offer updated!",
  "offer_id": 6806828,
  "gold": { "value": "123.45" },
  "ecash": { "value": "67890.12" },
  "edit_mode": "<div>...updated my offers HTML...</div>",
  "buy_mode": "<table>...offers table...</table>",
  "essentials": "<input .../>"
}
```

### Notes

- **Only own offers:** Citizens can only update their own offers
- **Shows confirmation dialog** before sending the request
- **Balance adjusts** if amount changes (difference is refunded or charged)
- `offer_id` in response confirms which offer was updated

---

## Exchange Delete Offer

**Method:** POST
**URL:** `/{lang}/economy/exchange/delete/`
**Auth Required:** Yes

### Description

Removes an existing exchange offer owned by the authenticated citizen from the monetary market. The held funds are returned to the citizen's account.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| offerId | number | Yes | The offer ID to delete |
| page | number | Yes | Current page number (0-indexed) |
| currencyId | number | Yes | Current currency view ID |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/delete/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&offerId=6806828&page=0&currencyId=62'
```

### Example Response (Success)

```json
{
  "error": false,
  "message": "Offer removed!",
  "gold": { "value": "124.45" },
  "ecash": { "value": "67890.12" },
  "buy_mode": "<table>...updated offers table...</table>",
  "edit_mode": "<div>...my offers HTML...</div>",
  "essentials": "<input .../>"
}
```

### Notes

- **Only own offers:** Citizens can only delete their own offers
- **Shows confirmation dialog:** "Are you sure you want to remove this offer?"
- **Funds returned:** The held currency is credited back to the citizen's account (reflected in `gold.value` / `ecash.value`)
- **View switch:** If no more personal offers remain, the response may omit `edit_mode` and the UI switches back to "All offers" view
- **Offer counter:** The "My offers" counter in the UI decreases by 1

---

## My Companies Page

**Method:** GET
**URL:** `/en/economy/myCompanies`
**Auth Required:** Yes

### Description

The company management page where citizens view and manage all their factories and holding companies. Returns an HTML page containing embedded JavaScript variables with full company data, production stats, upgrade trees, raw material stocks, employee/work info, and action URLs. This is a **page endpoint** (returns HTML, not JSON) — structured data is embedded in `<script>` tags.

### Parameters

No URL parameters.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Accept | `text/html,application/xhtml+xml` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/economy/myCompanies' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Embedded JavaScript Variables

#### `companies` — All Owned Companies

A map keyed by company ID containing every company the citizen owns. Each company includes production configuration, upgrade tree, work-as-manager (WAM) status, and holding assignment.

```javascript
var companies = {
  "358462": {
    "id": 358462,
    "holding_company_id": "148744",       // false if unassigned
    "industry_id": 2,
    "industry_name": "Weapons",
    "industry_token": "WEAPON",
    "products_img": "https://www.erepublik.net/images/icons/industry/2/q7_55x55.png?...",
    "quality": 7,
    "building_name": "Weapons Factory",
    "building_img": "//www.erepublik.net/images/modules/myland/buildings/weapons_q7.png",
    "is_raw": false,
    "raw_img": "https://www.erepublik.net/images/icons/industry/12/default.png?...",
    "wam_enabled": true,
    "can_work_as_manager": true,
    "cannot_work_as_manager_reason": "",   // "no_holding" when unassigned
    "can_assign_employees": true,
    "is_assigned_to_holding": true,
    "preset_works": 0,
    "preset_own_work": 1,
    "base_production": 10,
    "resource_bonus": 84,
    "effective_bonus": 184,
    "raw_usage": 36.8,
    "production": 18.4,
    "employee_limit": "10",
    "upgrade_url": "//economy.erepublik.com/en/company/customize/358462",
    "already_worked": false,
    "todays_works": 0,
    "total_works": 10,
    "sell_url": "//www.erepublik.com/en/economy/sell-company/358462",
    "upgrades": {
      "1": { "level": 1, "employees": 1, "cost": 610, "img": "...", "raw_usage": 0.1, "type": -1 },
      "2": { "level": 2, "employees": 2, "cost": 600, "img": "...", "raw_usage": 0.2, "type": -1 },
      "3": { "level": 3, "employees": 3, "cost": 575, "img": "...", "raw_usage": 0.3, "type": -1 },
      "4": { "level": 4, "employees": 5, "cost": 525, "img": "...", "raw_usage": 0.4, "type": -1 },
      "5": { "level": 5, "employees": 10, "cost": 425, "img": "...", "raw_usage": 0.5, "type": -1 },
      "6": { "level": 6, "employees": 10, "cost": 225, "img": "...", "raw_usage": 0.6, "type": -1 },
      "7": { "level": 7, "employees": 10, "cost": 0,   "img": "...", "raw_usage": 2,   "type": 0  }
    },
    "max_quality": 7,
    "own_tax": 65.5057
  }
  // ... more companies keyed by ID
}
```

**Company fields:**

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Unique company ID |
| `holding_company_id` | string\|false | ID of the holding this company is assigned to, or `false` if unassigned |
| `industry_id` | number | Industry type ID (1=Food, 2=Weapons, 4=House, 7=FRM, 12=WRM, 17=HRM, 23=Aircraft, 24=ARM) |
| `industry_name` | string | Human-readable industry name (e.g., "Food", "Weapons", "Rubber", "Deer") |
| `industry_token` | string | Token identifier (e.g., `FOOD`, `WEAPON`, `HOUSE`, `AIRCRAFT`) |
| `quality` | number | Product quality level (1–7) |
| `building_name` | string | Display name (e.g., "Weapons Factory", "Food Factory") |
| `is_raw` | boolean | `true` for raw material companies, `false` for finished goods |
| `wam_enabled` | boolean | Whether Work-as-Manager is enabled for this company |
| `can_work_as_manager` | boolean | Whether the citizen can currently WAM this company |
| `cannot_work_as_manager_reason` | string | Reason WAM is unavailable: `"no_holding"`, or empty string if available |
| `can_assign_employees` | boolean | Whether employees can be assigned |
| `is_assigned_to_holding` | boolean | Whether the company is part of a holding |
| `preset_works` | number | Number of employee work slots pre-configured for today |
| `preset_own_work` | number | Whether owner WAM is pre-configured (0 or 1) |
| `base_production` | number | Base production units per work (e.g., 10 for weapons, 100 for food) |
| `resource_bonus` | number | Region resource bonus percentage (e.g., 84) |
| `effective_bonus` | number | Total effective bonus: `base_production + resource_bonus` (e.g., 184) |
| `raw_usage` | number | Raw materials consumed per production cycle |
| `production` | number | Units produced per production cycle |
| `employee_limit` | string | Maximum employees (as string, e.g., "10") |
| `already_worked` | boolean | Whether the citizen has already WAM'd this company today |
| `todays_works` | number | Number of employee works completed today |
| `total_works` | number | Maximum works available today (includes employee slots) |
| `upgrade_url` | string | URL to customize/upgrade this company (on economy.erepublik.com subdomain) |
| `sell_url` | string | URL to sell this company |
| `upgrades` | object | Map of upgrade levels (see below) |
| `max_quality` | number | Maximum quality this company type can be upgraded to |
| `own_tax` | number | Work-as-manager tax amount in local currency |

**Upgrade level fields:**

| Field | Type | Description |
|-------|------|-------------|
| `level` | number | Upgrade level (1–7) |
| `employees` | number | Employee slots at this level |
| `cost` | number | Gold cost to upgrade to this level (0 = current level) |
| `raw_usage` | number | Raw materials consumed per product at this level |
| `type` | number | `-1` = below current, `0` = current level, `1` = above current (available upgrade) |

#### `holdingCompanies` — Holding Companies

A map keyed by holding ID containing all the citizen's holding companies.

```javascript
var holdingCompanies = {
  "148744": {
    "id": 148744,
    "owner_id": 4690052,
    "owner_entity_type": "citizen",
    "region_id": 663,
    "name": "Hail Lithuania",
    "created_at": 1675322000,
    "num_factories": 224,
    "avatar": "https://www.erepublik.net/images/default_avatars/holding_100x100.png"
  }
  // ... more holdings
}
```

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Unique holding company ID |
| `owner_id` | number | Citizen ID of the owner |
| `owner_entity_type` | string | Entity type (always `"citizen"`) |
| `region_id` | number | Region ID where the holding is located |
| `name` | string | Holding company name (user-defined) |
| `created_at` | number | Unix timestamp of creation |
| `num_factories` | number | Total factories assigned to this holding |
| `avatar` | string | Avatar image URL |

#### `pageDetails` — Page-Level Economy Data

Aggregated economy information including raw material stocks, employee data, energy, overtime stats, and product attribute definitions.

```javascript
var pageDetails = {
  "total_works": "579",
  "food_raw_stock": 8089,
  "weapon_raw_stock": 4605,
  "house_raw_stock": 1353,
  "airplane_raw_stock": 720,
  "recoverable_health": { "value": 0, "reset": "1770900062" },
  "recoverable_health_in_food": 14282854,
  "health": 128,
  "employee": {
    "salary": 7500,
    "total_daily_salary": 15000,
    "currency": "LTL",
    "employer": {
      "id": "2580095",
      "name": "Greenday_1989",
      "created_at": "2010-01-18 03:00:03",
      "has_avatar": "1"
    },
    "alreadyWorked": true,
    "taxes": 75
  },
  "show_first_view": false,
  "tutorial": false,
  "president": [],
  "next_overtime_work": 1770887170,
  "energy": 7135200,
  "overtime_points": 10792,
  "overtime_works": 449,
  "food": { /* product definition — see below */ },
  "weapon": { /* product definition */ },
  "house": { /* product definition */ },
  "airplane": { /* product definition */ }
}
```

**Top-level fields:**

| Field | Type | Description |
|-------|------|-------------|
| `total_works` | string | Total employee work slots available across all companies |
| `food_raw_stock` | number | Current Food Raw Materials (FRM) stock |
| `weapon_raw_stock` | number | Current Weapon Raw Materials (WRM) stock |
| `house_raw_stock` | number | Current House Raw Materials (HRM) stock |
| `airplane_raw_stock` | number | Current Aircraft Raw Materials (ARM) stock |
| `recoverable_health.value` | number | Recoverable energy amount |
| `recoverable_health.reset` | string | Unix timestamp when recoverable health resets |
| `recoverable_health_in_food` | number | Energy value of all food in inventory |
| `health` | number | Current citizen energy |
| `employee` | object | Employment data (salary, employer, taxes — same as `/en/main/job-data`) |
| `next_overtime_work` | number | Unix timestamp for next overtime availability |
| `energy` | number | Total energy cost for all preset WAM work (in energy units) |
| `overtime_points` | number | Accumulated overtime points |
| `overtime_works` | number | Total overtime work count |
| `president` | array | Empty array, or president data if citizen is president |

**Product definition fields** (nested under `food`, `weapon`, `house`, `airplane`):

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Product type ID (1=Food, 2=Weapons, 4=House, 23=Aircraft) |
| `name` | string | Product name |
| `token` | string | Token (e.g., `FOOD`, `WEAPON`) |
| `bp` | number | Base production per work (100 for food, 10 for weapons) |
| `max_quality` | number | Max quality level |
| `all_qualities` | array | All available quality values including special items |
| `wam_enabled` | boolean | Whether WAM is enabled for this industry |
| `buildingName` | string | Factory building display name |
| `raw` | object | Raw material info: `{ id, name, short_name, token, is_raw }` |
| `attributes` | object | Quality → effect mapping (energy restored, fire power, durability, etc.) |

**Food quality attributes** (from `pageDetails.food.attributes`):

| Quality | Effect (Energy) | Name |
|---------|----------------|------|
| 0 | 1 | *(base)* |
| 1 | 2 | Q1 Food |
| 2 | 4 | Q2 Food |
| 3 | 6 | Q3 Food |
| 4 | 8 | Q4 Food |
| 5 | 10 | Q5 Food |
| 6 | 12 | Q6 Food |
| 7 | 20 | Q7 Food |
| 10 | 100 | Energy Bar |
| 11 | 200 | Double Energy Bar |
| 12 | 50 | Treat |
| 13 | 50 | Carrot |
| 14 | 50 | Ice Cream |
| 15 | 50 | Pumpkin |
| 16 | 50 | Winter Treat |
| 17 | 300 | Triple Energy Bar |
| 18 | 10 | Bonbons |

#### `totals` — Production Totals

Aggregated production and raw material consumption across all companies.

```javascript
var totals = {
  "food_raw": 0,
  "weapon_raw": 109.65,
  "house_raw": 0,
  "airplane_raw": 0,
  "health_used": -2240,
  "own_works": 224,
  "food_units": 0,
  "weapon_units": 441.6,
  "house_units": 0,
  "airplane_units": 0,
  "preset_works": 0,
  "preset_own_works": 224,
  "work_as_manager_tax": -14673.28,
  "food_raw_img": "...",
  "food_img": "...",
  "weapon_img": "...",
  "weapon_raw_img": "...",
  "house_raw_img": "...",
  "house_img": "...",
  "airplane_raw_img": "...",
  "airplane_img": "..."
}
```

| Field | Type | Description |
|-------|------|-------------|
| `food_raw` | number | Total FRM consumed by all preset food companies |
| `weapon_raw` | number | Total WRM consumed by all preset weapon companies |
| `house_raw` | number | Total HRM consumed by all preset house companies |
| `airplane_raw` | number | Total ARM consumed by all preset aircraft companies |
| `health_used` | number | Total energy consumed (negative value) |
| `own_works` | number | Total WAM work count across all companies |
| `food_units` | number | Total food units produced |
| `weapon_units` | number | Total weapon units produced |
| `house_units` | number | Total house units produced |
| `airplane_units` | number | Total aircraft units produced |
| `preset_works` | number | Total preset employee works |
| `preset_own_works` | number | Total preset WAM works |
| `work_as_manager_tax` | number | Total WAM tax cost in local currency (negative value) |

#### `energyData` — Current Energy Status

```javascript
var energyData = {
  "energy": 128,
  "energyPoolLimit": 10220,
  "recoverableEnergy": 0,
  "energyPerInterval": 64,
  "energyStatus": "recovering"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `energy` | number | Current energy points |
| `energyPoolLimit` | number | Maximum energy pool capacity |
| `recoverableEnergy` | number | Energy that can be recovered |
| `energyPerInterval` | number | Energy recovered per 6-minute interval |
| `energyStatus` | string | Recovery status: `"recovering"` or `"full"` |

#### Scalar Configuration Variables

```javascript
var csrfToken        = '99e749d8bab079654efe0b67c298b9a2';
var food_remaining   = 0;
var has_captcha      = 0;
var has_pin          = 1;
var is_organization  = '';
var rawMultiplier    = 0.01;
var promoMultiplier  = parseFloat(0);       // active production promo multiplier
var promoMultiplierHouse = parseFloat(0);    // active house production promo multiplier
var defaultHoldingsOpenClosedState = "closed";

// Action URLs
var work_url          = '//www.erepublik.com/en/economy/work';
var work_overtime_url = '//www.erepublik.com/en/economy/workOvertime';
var resign_url        = '//www.erepublik.com/en/economy/resign';
var captcha_url       = '//www.erepublik.com/en/economy/recaptchaAjax';
var upgarade_url      = '//www.erepublik.com/en/economy/upgrade-company';  // note: typo is in original
var linkForBuy        = '/en/economy/marketplace#72/1';
```

| Variable | Type | Description |
|----------|------|-------------|
| `csrfToken` | string | CSRF token for POST requests on this page |
| `food_remaining` | number | Food raw materials remaining after preset production |
| `has_captcha` | number | Whether CAPTCHA verification is required (0=no, 1=yes) |
| `has_pin` | number | Whether the citizen has set a PIN (0=no, 1=yes) |
| `is_organization` | string | Empty string for citizens; set for organization accounts |
| `rawMultiplier` | number | Base raw material consumption multiplier per employee |
| `promoMultiplier` | number | Active production bonus multiplier (0 = no promo) |
| `promoMultiplierHouse` | number | Active house production bonus multiplier (0 = no promo) |
| `defaultHoldingsOpenClosedState` | string | UI default state for holding panels: `"closed"` or `"open"` |
| `work_url` | string | POST endpoint to perform employee work |
| `work_overtime_url` | string | POST endpoint to perform overtime work |
| `resign_url` | string | POST endpoint to resign from current job |
| `captcha_url` | string | AJAX endpoint for CAPTCHA verification |
| `upgarade_url` | string | POST endpoint to upgrade a company (note: variable name typo `upgarade` is in the original source) |
| `linkForBuy` | string | Marketplace link for buying raw materials |

### Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- Data is extracted from `<script>` tags embedded in the page HTML
- The `companies` map can be very large — a citizen with 315 companies generates ~1.8 MB of HTML
- Companies not assigned to a holding have `holding_company_id: false` and `cannot_work_as_manager_reason: "no_holding"`
- The `upgrades` object uses `type` to indicate relative position: `-1` = already surpassed, `0` = current level, `1` = available upgrade
- The `upgarade_url` variable has a typo in the original eRepublik source code (`upgarade` instead of `upgrade`)
- Industry IDs for raw materials: 7=FRM, 12=WRM, 17=HRM, 24=ARM; for raw resource companies: Deer, Cattle, Fish (food raws), Rubber, Aluminum, Granite, Neodymium (weapon/house/aircraft raws)
- The `promoMultiplier` and `promoMultiplierHouse` values are non-zero during special production bonus events
- Action URLs (`work_url`, `resign_url`, etc.) require a POST with `csrfToken` as `_token` parameter

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
- [Military API](API_MILITARY.md)
- [Social API](API_SOCIAL.md)
