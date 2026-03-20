# Inventory

#erepublik #api #economy #inventory

[< Back to Economy Overview](README.md)

Complete inventory snapshot -- all currencies, items, boosters, active enhancements, production status, and assembly components.

**Related:** [Boosters](boosters.md) | [Marketplace](marketplace.md) | [Shop](shop.md)

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

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/inventory-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

</details>

<details>
<summary>Example Response</summary>

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
        "amountDisplay": "\u200672.9k\u2060"
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
        "amountDisplay": "\u20066.27M\u2060"
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
          "energy": "\u2060+6\u2060",
          "refillValue": "\u2060+6\u2060",
          "highStorage": true
        },
        "description": "Consuming food recovers your Energy",
        "amount": 2332968,
        "amountDisplay": "\u20062.33M\u2060",
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
          "battleDamageGnd": "\u2060+200%\u2060"
        },
        "description": "Using Ammunition improves your damage in battles",
        "uses": 476183,
        "amount": 47619,
        "amountDisplay": "\u206047.6k\u2060",
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
        "amountDisplay": "\u2060486\u2060",
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
          "energyPool": "\u2060+100\u2060",
          "active": {
            "activeTimeLeft": 701048,
            "activeUntil": 1770053964,
            "showProgress": true,
            "progress": 1.29,
            "state": null
          },
          "overtimePoints": "\u2060+1/h\u2060",
          "residenceBonus": "active"
        },
        "description": null,
        "amount": 701048,
        "amountDisplay": "\u20608d\u2060"
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
          "energy": "\u2060+6\u2060",
          "refillValue": "\u2060+6\u2060"
        },
        "description": "Consuming food recovers your Energy",
        "amount": 0.71,
        "amountDisplay": "\u206071%\u2060"
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
          "amountDisplay": "\u20604.53k\u2060",
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
            "amountDisplay": "\u20604.53k\u2060"
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

</details>

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
  - `energyRecovery`: Energy recovery rate boost
  - `durability`: Number of uses (weapons)
  - `battleDamageGnd` / `battleDamageAir`: Combat damage boost percentage (directional)
  - `battleInfluence` / `battleInfluenceGnd` / `battleInfluenceAir`: Battle influence boost (flat or directional)
  - `fixedDamage`: Weapon deals fixed damage (not affected by boosters)
  - `firepowerGnd` / `firepowerAir`: Fixed damage value (directional)
  - `militaryRank` / `militaryRankGnd` / `militaryRankAir`: Military rank point boost (flat or directional)
  - `prestigePoints` / `prestige_points`: Prestige point boost — **note:** API uses both camelCase and snake_case inconsistently
  - `deploySize` / `deploy_size`: Deploy size boost — **note:** same API inconsistency in naming
  - `ground_deploy_influence` / `air_deploy_influence`: Deploy influence boost (directional)
  - `ground_deploy_rank_points` / `air_deploy_rank_points`: Rank points from deploy (directional)
  - `air_damage`: Air damage boost (specific naming for some booster types)
  - `acceleration`: Movement/speed acceleration boost
  - `speed`: Speed boost
  - `distance`: Travel distance bonus
  - `heroReward`: Hero reward bonus
  - `overtimePoints`: Overtime point generation rate (e.g. `+1/h` from Houses)
  - `temporary`: Time-limited item with expiration data
  - `actionable`: Item can be activated/used (boosters, houses)
  - `component`: Used in assembly/production
  - `armory`: Item associated with armory mechanics
  - `packBooster`: Pack booster indicator
  - `blitzkrieg_pack`: Blitzkrieg pack booster type
  - `catchup`: Catch-up mechanic booster
  - `power_pack`: Power pack booster type

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
  - `amountDisplay`: Formatted display string (e.g., "72.9k", "2.33M")
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
