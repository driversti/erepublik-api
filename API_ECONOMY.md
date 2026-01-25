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
| industryId | number | Yes | Product industry type (1=Food, 2=Weapons, 23=Aircraft, 24=House) |
| quality | number | Yes | Product quality level (1-7 for most products) |
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

- **Industry IDs:**
  - 1: Food
  - 2: Weapons
  - 23: Aircraft
  - 24: House
  - See raw materials for IDs 1-20 (documented in "Work as Manager" endpoint)

- **Quality Levels:**
  - Food, Weapons, Aircraft, House: Q1-Q7
  - Higher quality provides better effects/energy restoration

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
  - 1: Food (Q1-Q11)
  - 2: Weapons/Ammunition (Q1-Q7)
  - 4: Houses (Q1-Q5)
  - 23: Aircraft Weapons (Q1-Q5)
  - 7, 12, 17, 24: Raw Materials

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
