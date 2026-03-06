# Power Spin

Power Spin (Wheel of Fortune) is a gamification feature where players spend currency to spin a prize wheel. The wheel has 12 regular prize slots and a progressive jackpot system with 3 tiers. Players can spin using Country Currency (CC) with single or bulk spin options.

#erepublik #api #powerspin #wheeloffortune #gamification

[< Back to Table of Contents](../API_TOC.md)

---

## Overview

**Spin pricing:** The first spin is free. The second spin costs 500 CC. Each subsequent spin increases by 100 CC (i.e., 0 -> 500 -> 600 -> 700 -> 800 -> ...).

---

## Build Wheel of Fortune

**Method:** POST
**URL:** `/en/main/wheeloffortune-build`
**Auth Required:** Yes

### Description

Builds the Wheel of Fortune (Power Spin) configuration, returning the complete wheel setup including all available prizes, jackpot prizes, spin costs, player progress, and multispin pricing. This is a read/init endpoint -- it does not perform a spin, only returns the data needed to render the wheel UI.

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
curl -X POST 'https://www.erepublik.com/en/main/wheeloffortune-build' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "progress": {
    "jackpot": 0,
    "free_spin": 0,
    "spins": 9
  },
  "type": "cc",
  "cost": 1400,
  "prizes": {
    "jackpot": {
      "1": {
        "type": "damage_booster",
        "amount": 150,
        "ttl": 600,
        "tooltip": "+150% Ground Damage for 10m",
        "plate": {
          "id": "100_damage_15_600",
          "industryId": 100,
          "quality": 15,
          "name": "+150% Ground Damage",
          "type": "booster",
          "rarity": "epic",
          "icon": "https://www.erepublik.net/images/icons/boosters/128px/damage.png",
          "attributes": {
            "booster": "damage",
            "duration": "10m",
            "durationSeconds": 600,
            "battleDamageGnd": "+150%",
            "damage": 150
          }
        },
        "value": 1,
        "icon": "https://www.erepublik.net/images/icons/boosters/128px/damage.png"
      },
      "2": {
        "value": 2,
        "type": "energy_house",
        "amount": 1000,
        "ttl": 604800,
        "tooltip": "+1000 Energy Center for 7d"
      },
      "3": {
        "value": 3,
        "type": "gold",
        "amount": 500,
        "tooltip": "500 Gold",
        "plate": {
          "id": "gold",
          "name": "Gold",
          "type": "currency",
          "rarity": "epic"
        }
      }
    },
    "prizes": {
      "1": {
        "type": "triple_energy_bar",
        "ttl": 1209600,
        "amount": 1,
        "tooltip": "Triple Energy Bar",
        "value": 1
      },
      "2": {
        "type": "speed_booster_2",
        "amount": 1,
        "ttl": 600,
        "tooltip": "x5 Damage Accelerator for 10m",
        "value": 2
      },
      "3": {
        "type": "air_damage_booster_20",
        "amount": 1,
        "ttl": 600,
        "tooltip": "+20% Air Damage for 10m",
        "value": 3
      },
      "4": {
        "type": "stinger_missile",
        "amount": 3,
        "tooltip": "[3x] AIM-92 Stingers",
        "value": 4
      },
      "5": {
        "type": "prestige_points",
        "amount": 100,
        "tooltip": "+100 Prestige Points",
        "value": 5
      },
      "6": {
        "type": "energy_house",
        "amount": 200,
        "ttl": 604800,
        "tooltip": "+200 Energy Center for 7d",
        "value": 6
      },
      "7": {
        "type": "jackpot",
        "amount": 1,
        "tooltip": "Jackpot",
        "value": 7
      },
      "8": {
        "type": "energy_bars",
        "amount": 2,
        "tooltip": "+2 Simple Energy Bars",
        "value": 8
      },
      "9": {
        "type": "damage_booster",
        "amount": 100,
        "ttl": 600,
        "tooltip": "+100% Ground Damage for 10m",
        "value": 9
      },
      "10": {
        "type": "small_bomb",
        "amount": 1,
        "ttl": 2592000,
        "tooltip": "Small Bomb",
        "value": 10
      },
      "11": {
        "type": "prestige_points_booster",
        "amount": 1,
        "ttl": 600,
        "tooltip": "+1 Prestige Points for 10m",
        "value": 11
      },
      "12": {
        "type": "overtime_points",
        "amount": 1,
        "tooltip": "Overtime Points",
        "value": 12
      }
    }
  },
  "multispin": {
    "1": {
      "spins": 1,
      "cost": 1400,
      "text": "Spin 1 times"
    },
    "5": {
      "spins": 5,
      "cost": 8000,
      "text": "Spin 5 times"
    }
  },
  "theme": null
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| progress | object | Player's spin progress in the current session |
| progress.jackpot | number | Progress toward the jackpot (increments when landing on jackpot slot) |
| progress.free_spin | number | Number of free spins available |
| progress.spins | number | Total spins performed |
| type | string | Currency type used for spins (`"cc"` = Country Currency) |
| cost | number | Base cost per spin in the specified currency |
| prizes.jackpot | object | Jackpot prize tiers (keyed `"1"`, `"2"`, `"3"`) -- awarded when jackpot progress is filled |
| prizes.prizes | object | Regular wheel prizes (keyed `"1"` through `"12"`) -- one per wheel segment |
| multispin | object | Bulk spin options with per-option cost and count |
| theme | string/null | Active seasonal theme identifier (`null` when no theme) |

### Prize Object Fields

Each prize in `prizes.jackpot` and `prizes.prizes` contains:

| Field | Type | Description |
|-------|------|-------------|
| type | string | Prize type identifier (see Prize Types table) |
| amount | number | Quantity awarded |
| ttl | number | Time-to-live in seconds (for temporary items like boosters, energy centers) |
| tooltip | string | Human-readable prize description |
| value | number | Wheel position/slot number |
| icon | string | URL to the prize icon image |
| plate | object/null | Rich item metadata (inventory-style data with rarity, attributes, tooltips) |

### Prize Types

| Type | Description | Example |
|------|-------------|---------|
| `damage_booster` | Ground damage booster (temporary) | +100%/+150% Ground Damage for 10m |
| `air_damage_booster_20` | Air damage booster (temporary) | +20% Air Damage for 10m |
| `speed_booster_2` | Damage accelerator (temporary) | x5 Damage Accelerator for 10m |
| `prestige_points_booster` | Prestige points booster (temporary) | +1 Prestige Points for 10m |
| `energy_bars` | Simple Energy Bars | +2 Energy Bars (+100 energy each) |
| `triple_energy_bar` | Triple Energy Bar | +300 energy, 14-day TTL |
| `energy_house` | Temporary Energy Center | +200 or +1000 Energy Center for 7d |
| `stinger_missile` | AIM-92 Stinger air weapons | 3x missiles with 1k firepower each |
| `small_bomb` | Small Bomb (ground weapon) | 75k fixed damage, 30-day TTL |
| `prestige_points` | Prestige Points (permanent) | +100 Prestige Points |
| `overtime_points` | Overtime Points | +1 Overtime Point |
| `gold` | Gold currency (jackpot only) | 500 Gold |
| `jackpot` | Jackpot slot -- increments jackpot progress | Advances toward jackpot prizes |

### Notes

- CSRF token (`_token`) must be obtained from the page
- The `type` field value `"cc"` indicates the wheel costs Country Currency
- **Progressive pricing**: The first spin is free, the second costs 500 CC, and each subsequent spin increases by 100 CC (0 -> 500 -> 600 -> 700 -> ...). The `cost` field always reflects the current price for the next spin
- Multispin costs also escalate with spin count
- The jackpot has 3 progressive tiers; landing on the `"jackpot"` wheel slot (prize #7) increments `progress.jackpot`
- Prize `ttl` values represent item expiration: 600s (10 minutes) for boosters, 604800s (7 days) for energy centers, 1209600s (14 days) for triple energy bars, 2592000s (30 days) for bombs
- The `plate` object follows the same item metadata schema used across the game's inventory system (see `fightDeploy-getInventory` in Military API)
- The `theme` field activates seasonal wheel variants (e.g., holiday themes); `null` means the standard wheel
- Prize amounts and types may rotate periodically or vary by player level

---

## Spin Wheel of Fortune

**Method:** POST
**URL:** `/en/main/wheeloffortune-spin`
**Auth Required:** Yes

### Description

Performs a spin (or multi-spin) on the Wheel of Fortune, deducting currency from the player's account and awarding a random prize. Returns the updated wheel state including new cost, progress, and prizes. The client must send the current expected cost (`_currentCost`) which the server validates against its own state -- a mismatch returns `WOF_ERR_INVALID_CLIENT_STATE`.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| _currentCost | number | Yes | The cost the client expects to pay (must match server's current `cost` value from `wheeloffortune-build`) |
| spins | number | Yes | Number of spins to perform (matches `multispin` keys: `1` or `5`) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/wheeloffortune-spin' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  --data-raw '_token=YOUR_CSRF_TOKEN&_currentCost=1400&spins=1'
```

</details>

<details>
<summary>Example Response (Error -- Client State Mismatch)</summary>

```json
{
  "progress": {
    "jackpot": 0,
    "free_spin": 0,
    "spins": 10
  },
  "type": "cc",
  "cost": 1500,
  "prizes": {
    "jackpot": {
      "1": {
        "type": "damage_booster",
        "amount": 150,
        "ttl": 600,
        "tooltip": "+150% Ground Damage for 10m",
        "plate": {
          "id": "100_damage_15_600",
          "industryId": 100,
          "quality": 15,
          "name": "+150% Ground Damage",
          "type": "booster",
          "rarity": "epic",
          "icon": "https://www.erepublik.net/images/icons/boosters/128px/damage.png",
          "attributes": {
            "booster": "damage",
            "duration": "10m",
            "durationSeconds": 600,
            "battleDamageGnd": "+150%",
            "battleDamage": "+150%",
            "damage": 150
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 },
              { "type": "battleDamageGnd", "text": "Damage Boost", "value": "+150%" }
            ],
            "info": null,
            "help": null
          },
          "description": "Hit 150% stronger in Ground Battles for 10 minutes",
          "amount": 1,
          "amountDisplay": "\u20601\u2060"
        },
        "value": 1,
        "icon": "https://www.erepublik.net/images/icons/boosters/128px/damage.png"
      },
      "2": {
        "value": 2,
        "type": "energy_house",
        "amount": 1000,
        "ttl": 604800,
        "tooltip": "+1000 Energy Center for 7d",
        "plate": null,
        "icon": "https://www.erepublik.net/images/icons/rewards/energy_house.png"
      },
      "3": {
        "value": 3,
        "type": "gold",
        "amount": 500,
        "tooltip": "500 Gold",
        "plate": {
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
              { "type": "quantity", "text": "Quantity", "value": 500 }
            ],
            "info": null,
            "help": null
          },
          "description": "eRepublik's universal currency",
          "amount": 500,
          "amountDisplay": "\u2060500\u2060"
        },
        "icon": "https://www.erepublik.net/images/icons/rewards/128px/gold.png"
      }
    },
    "prizes": {
      "1": {
        "type": "triple_energy_bar",
        "ttl": 1209600,
        "amount": 1,
        "tooltip": "Triple Energy Bar",
        "plate": {
          "id": "1_17",
          "industryId": 1,
          "quality": 17,
          "name": "Triple Energy Bar",
          "type": "food",
          "rarity": "epic",
          "icon": "https://www.erepublik.net/images/icons/industry/1/q17_128x128.png",
          "attributes": {
            "canBeSold": true,
            "food": true,
            "energy": "\u2060+300\u2060",
            "refillValue": "\u2060+300\u2060"
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 },
              { "type": "energy", "text": "Energy", "value": "\u2060+300\u2060" }
            ],
            "info": null,
            "help": null
          },
          "description": "Consuming food recovers your Energy",
          "amount": 1,
          "amountDisplay": "\u20601\u2060",
          "amountForSale": 1
        },
        "value": 1,
        "icon": "https://www.erepublik.net/images/icons/industry/1/q17_128x128.png"
      },
      "2": {
        "type": "speed_booster_2",
        "amount": 1,
        "ttl": 600,
        "tooltip": "x5 Damage Accelerator for 10m",
        "plate": {
          "id": "100_speed_2_600",
          "industryId": 100,
          "quality": 2,
          "name": "x5 Damage Accelerator",
          "type": "booster",
          "rarity": "rare",
          "icon": "https://www.erepublik.net/images/icons/boosters/128px/speed.png",
          "attributes": {
            "booster": "speed",
            "duration": "10m",
            "durationSeconds": 600,
            "acceleration": "x5",
            "speed": 5
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 },
              { "type": "acceleration", "text": "Acceleration", "value": "x5" }
            ],
            "info": null,
            "help": null
          },
          "description": "Hit x5 faster for 10 minutes",
          "amount": 1,
          "amountDisplay": "\u20601\u2060"
        },
        "value": 2,
        "icon": "https://www.erepublik.net/images/icons/boosters/128px/speed.png"
      },
      "3": {
        "type": "air_damage_booster_20",
        "amount": 1,
        "ttl": 600,
        "tooltip": "+20% Air Damage for 10m",
        "plate": {
          "id": "100_air_damage_2_600",
          "industryId": 100,
          "quality": 2,
          "name": "+20% Air Damage",
          "type": "booster",
          "rarity": "uncommon",
          "icon": "https://www.erepublik.net/images/icons/boosters/128px/air_damage.png",
          "attributes": {
            "booster": "air_damage",
            "duration": "10m",
            "durationSeconds": 600,
            "battleDamageAir": "+20%",
            "battleDamage": "+20%",
            "air_damage": 20
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 },
              { "type": "battleDamageAir", "text": "Damage Boost", "value": "+20%" }
            ],
            "info": null,
            "help": null
          },
          "description": "Hit 20% stronger in Air Battles for 10 minutes",
          "amount": 1,
          "amountDisplay": "\u20601\u2060"
        },
        "value": 3,
        "icon": "https://www.erepublik.net/images/icons/boosters/128px/air_damage.png"
      },
      "4": {
        "type": "stinger_missile",
        "amount": 3,
        "tooltip": "[3x] AIM-92 Stingers",
        "plate": {
          "id": "23_10",
          "industryId": 23,
          "quality": 10,
          "name": "AIM-92 Stinger",
          "type": "airWeapon",
          "rarity": "epic",
          "icon": "https://www.erepublik.net/images/icons/industry/23/q10_128x128.png",
          "attributes": {
            "canBeSold": true,
            "airWeapon": true,
            "durability": "3 uses",
            "fixedDamage": true,
            "firepowerAir": "1k"
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 3 },
              { "type": "durability", "text": "Durability", "value": "3 uses" },
              { "type": "firepowerAir", "text": "Firepower", "value": "1k" }
            ],
            "info": null,
            "help": null
          },
          "description": null,
          "uses": 9,
          "amount": 3,
          "amountDisplay": "\u20603\u2060",
          "amountForSale": 3
        },
        "value": 4,
        "icon": "https://www.erepublik.net/images/icons/industry/23/q10_128x128.png"
      },
      "5": {
        "type": "prestige_points",
        "amount": 100,
        "tooltip": "+100 Prestige Points",
        "value": 5,
        "icon": "https://www.erepublik.net/images/modules/power_spin/prizes/prestige-wheel.png"
      },
      "6": {
        "type": "energy_house",
        "amount": 200,
        "ttl": 604800,
        "tooltip": "+200 Energy Center for 7d",
        "value": 6,
        "icon": "https://www.erepublik.net/images/icons/rewards/energy_house.png"
      },
      "7": {
        "type": "jackpot",
        "amount": 1,
        "tooltip": "Jackpot",
        "value": 7,
        "icon": "https://www.erepublik.net/images/icons/rewards/128px/jackpot.png"
      },
      "8": {
        "type": "energy_bars",
        "amount": 2,
        "tooltip": "+2 Simple Energy Bars",
        "plate": {
          "id": "1_10",
          "industryId": 1,
          "quality": 10,
          "name": "Energy Bar",
          "type": "food",
          "rarity": "uncommon",
          "icon": "https://www.erepublik.net/images/icons/industry/1/q10_128x128.png",
          "attributes": {
            "canBeSold": true,
            "food": true,
            "energy": "\u2060+100\u2060",
            "refillValue": "\u2060+100\u2060"
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 2 },
              { "type": "energy", "text": "Energy", "value": "\u2060+100\u2060" }
            ],
            "info": null,
            "help": null
          },
          "description": "Consuming food recovers your Energy",
          "amount": 2,
          "amountDisplay": "\u20602\u2060",
          "amountForSale": 2
        },
        "value": 8,
        "icon": "https://www.erepublik.net/images/icons/industry/1/q10_128x128.png"
      },
      "9": {
        "type": "damage_booster",
        "amount": 100,
        "ttl": 600,
        "tooltip": "+100% Ground Damage for 10m",
        "plate": {
          "id": "100_damage_10_600",
          "industryId": 100,
          "quality": 10,
          "name": "+100% Ground Damage",
          "type": "booster",
          "rarity": "rare",
          "icon": "https://www.erepublik.net/images/icons/boosters/128px/damage.png",
          "attributes": {
            "booster": "damage",
            "duration": "10m",
            "durationSeconds": 600,
            "battleDamageGnd": "+100%",
            "battleDamage": "+100%",
            "damage": 100
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 },
              { "type": "battleDamageGnd", "text": "Damage Boost", "value": "+100%" }
            ],
            "info": null,
            "help": null
          },
          "description": "Hit 100% stronger in Ground Battles for 10 minutes",
          "amount": 1,
          "amountDisplay": "\u20601\u2060"
        },
        "value": 9,
        "icon": "https://www.erepublik.net/images/icons/boosters/128px/damage.png"
      },
      "10": {
        "type": "small_bomb",
        "amount": 1,
        "ttl": 2592000,
        "tooltip": "Small Bomb",
        "plate": {
          "id": "100_211",
          "industryId": 100,
          "quality": 211,
          "name": "Small Bomb",
          "type": "groundBomb",
          "rarity": "rare",
          "icon": "https://www.erepublik.net/images/icons/industry/2/q211_128x128.png",
          "attributes": {
            "groundWeapon": true,
            "fixedDamage": true,
            "firepowerGnd": "75k"
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 },
              { "type": "firepowerGnd", "text": "Firepower", "value": "75k" }
            ],
            "info": [
              { "type": "warn", "text": "This weapon is not affected by Damage Boosters" },
              { "type": "warn", "text": "Using this weapon does not increase your Military Rank" },
              { "type": "warn", "text": "Using this weapon does not increase your Protector Level" }
            ],
            "help": null
          },
          "description": "Instantly deal 75k damage without using Energy",
          "uses": 1,
          "amount": 1,
          "amountDisplay": "\u20601\u2060"
        },
        "value": 10,
        "icon": "https://www.erepublik.net/images/icons/industry/2/q21_128x128.png"
      },
      "11": {
        "type": "prestige_points_booster",
        "amount": 1,
        "ttl": 600,
        "tooltip": "+1 Prestige Points for 10m",
        "plate": {
          "id": "100_prestige_points_1_600",
          "industryId": 100,
          "quality": 1,
          "name": "+1 Prestige Points",
          "type": "booster",
          "rarity": "epic",
          "icon": "https://www.erepublik.net/images/icons/boosters/128px/prestige_points.png",
          "attributes": {
            "booster": "prestige_points",
            "duration": "10m",
            "durationSeconds": 600,
            "prestigePoints": "\u2060+1\u2060",
            "prestige_points": 1
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 },
              { "type": "prestigePoints", "text": "Prestige Boost", "value": "\u2060+1\u2060" }
            ],
            "info": null,
            "help": null
          },
          "description": "Increase your prestige by +1 for 10 minutes",
          "amount": 1,
          "amountDisplay": "\u20601\u2060"
        },
        "value": 11,
        "icon": "https://www.erepublik.net/images/icons/boosters/128px/prestige_points.png"
      },
      "12": {
        "type": "overtime_points",
        "amount": 1,
        "tooltip": "Overtime Points",
        "plate": {
          "id": "overtime_points",
          "industryId": 4,
          "quality": 100,
          "name": "Overtime Points",
          "type": "miscellaneous",
          "rarity": "uncommon",
          "icon": "https://www.erepublik.net/images/icons/industry/1000/overtime_points_128x128.png",
          "attributes": {
            "miscellaneous": true,
            "token": true
          },
          "tooltip": {
            "attributes": [
              { "type": "quantity", "text": "Quantity", "value": 1 }
            ],
            "info": null,
            "help": null
          },
          "description": "You receive one Overtime Point every hour for each active house you own.",
          "amount": 1,
          "amountDisplay": "\u20601\u2060"
        },
        "value": 12,
        "icon": "https://www.erepublik.net/images/icons/industry/1000/overtime_points_128x128.png"
      }
    }
  },
  "multispin": {
    "1": { "spins": 1, "cost": 1500, "text": "Spin 1 times" },
    "5": { "spins": 5, "cost": 8500, "text": "Spin 5 times" }
  },
  "theme": null,
  "error": true,
  "message": "WOF_ERR_INVALID_CLIENT_STATE"
}
```

</details>

<details>
<summary>Example Response (Success -- Single Spin)</summary>

```json
{
  "cost": 2000,
  "estCost": 1900,
  "account": {
    "initial": 6278874,
    "spent": 1900,
    "remaining": 6276974
  },
  "jackpot": 0,
  "free_spin": 0,
  "spins": 15,
  "prizes": [
    {
      "prize": "small_bomb",
      "index": 10,
      "icon": "https://www.erepublik.net/images/icons/industry/2/q21_128x128.png?1763108780",
      "tooltip": "Small Bomb"
    }
  ],
  "multispin": {
    "1": { "spins": 1, "cost": 2000, "text": "Spin 1 times" },
    "5": { "spins": 5, "cost": 11000, "text": "Spin 5 times" }
  }
}
```

</details>

### Response Fields

**IMPORTANT:** The success response has a **different structure** from the error response and from `wheeloffortune-build`. Progress fields are **flat** (not nested under `progress`), and `prizes` is an **array** of won items (not the wheel configuration map).

| Field | Type | Description |
|-------|------|-------------|
| cost | number | Price for the **next** spin (progressive pricing) |
| estCost | number | The cost that was charged for this spin (matches `_currentCost` sent) |
| account | object | Player's currency balance details for this transaction |
| account.initial | number | CC balance before this spin |
| account.spent | number | CC deducted for this spin |
| account.remaining | number | CC balance after this spin |
| jackpot | number | Current jackpot progress (0-3), flat -- NOT nested under `progress` |
| free_spin | number | Free spins remaining, flat |
| spins | number | Total spins performed, flat |
| prizes | array | **Array of won prize objects** (NOT the wheel prize map) |
| prizes[].prize | string | Prize type identifier (e.g., `"small_bomb"`, `"energy_bars"`) |
| prizes[].index | number | Wheel slot index (maps to `prizes.prizes[index]` from `wheeloffortune-build`) |
| prizes[].icon | string | URL to the prize icon |
| prizes[].tooltip | string | Human-readable prize description |
| multispin | object | Updated bulk spin options with new costs |
| error | boolean | Whether the spin failed (`true` = error, absent on success) |
| message | string | Error code (only present when `error: true`) |

### Error Codes

| Code | Description |
|------|-------------|
| `WOF_ERR_INVALID_CLIENT_STATE` | The `_currentCost` sent by the client doesn't match the server's current spin price. Even on this error, the response includes updated state, so the client can retry with the correct `cost` without re-calling `wheeloffortune-build`. |

### Notes

- **Success vs Error response structures differ significantly**: On error, the response mirrors `wheeloffortune-build` (with nested `progress` and `prizes` as the wheel map). On success, fields are flat and `prizes` becomes an array of won items
- The `_currentCost` parameter acts as an **optimistic concurrency check** -- the client must know the exact current price before spinning
- The `cost` field in the response reflects the price for the **next** spin (progressive pricing)
- The `spins` parameter should match a valid key from the `multispin` object (currently `1` or `5`)
- The `account.spent` field provides server-authoritative spending data -- more reliable than client-side cost tracking
- The `prizes[].index` field corresponds to wheel slot numbers from the `wheeloffortune-build` response, which can be used to look up the full prize details (including `amount`)
- For multi-spin (`spins=5`), the `prizes` array likely contains 5 elements, one per individual spin result
- Insufficient currency balance likely returns a different error code (not yet documented)

---

## Related

- [API Table of Contents](../API_TOC.md)
- [Authentication](../auth/README.md)
- [Rewards](../rewards/README.md)
