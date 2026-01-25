# eRepublik API - Economy

#erepublik #api #economy #marketplace

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Economy endpoints cover marketplace, work mechanics, companies, currencies (Gold/CC), and financial transactions.

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
