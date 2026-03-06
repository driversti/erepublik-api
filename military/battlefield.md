# Battlefield Page

#erepublik #api #military #battlefield

[< Back to Military Module](README.md)

The battlefield page endpoint and its embedded `BATTLE_SERVER_DATA` JSON structure containing rich battle state data.

**Related:** [battlefield-ajax.md](battlefield-ajax.md) | [websocket.md](websocket.md) | [deployment.md](deployment.md) | [frontend-reference.md](frontend-reference.md)

---

## Get Battlefield Page

**Method:** GET
**URL:** `/en/military/battlefield/{battleId}`
**Auth Required:** Yes

### Description

Returns the full battlefield HTML page for a specific battle. This is **not a JSON API** -- it returns a 200+ KB HTML document with embedded JavaScript containing rich battle state data.

The useful data is embedded in a `<script id="BATTLE_SERVER_DATA">` tag as:

```javascript
SERVER_DATA = $j.extend(SERVER_DATA, { /* battle state JSON */ });
```

This merges battle-specific fields into the global `SERVER_DATA` object (which already contains session-level data like `csrfToken`, `isLoggedIn`, `serverTime` from an earlier `<script>` block on line ~49).

### Path Parameters

| Parameter  | Type   | Required | Description                          |
|------------|--------|----------|--------------------------------------|
| `battleId` | number | Yes      | The battle (campaign) ID to load     |

### Headers

| Header                  | Value                                                                                      | Required |
|-------------------------|--------------------------------------------------------------------------------------------|----------|
| `Cookie`                | `erpk=SESSION_TOKEN; erpk_mid=...; erpk_plang=en; erpk_auth=1; erpk_rm=...; cf_clearance=...` | Yes      |
| `Accept`                | `text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8`                         | Yes      |
| `User-Agent`            | Standard browser UA                                                                        | Yes      |
| `sec-fetch-dest`        | `document`                                                                                 | No       |
| `sec-fetch-mode`        | `navigate`                                                                                 | No       |
| `upgrade-insecure-requests` | `1`                                                                                    | No       |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/military/battlefield/851234' \
  -H 'accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' \
  -H 'accept-language: en-GB,en;q=0.9' \
  -b 'erpk=YOUR_SESSION_TOKEN; erpk_mid=YOUR_MID; erpk_plang=en; erpk_auth=1; erpk_rm=YOUR_RM_TOKEN; cf_clearance=YOUR_CF_TOKEN' \
  -H 'sec-fetch-dest: document' \
  -H 'sec-fetch-mode: navigate' \
  -H 'upgrade-insecure-requests: 1' \
  -H 'user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36'
```

</details>

### Response

**Status:** `200 OK`
**Content-Type:** `text/html; charset=UTF-8`
**Size:** ~221 KB (full HTML page)

---

## Embedded `BATTLE_SERVER_DATA` JSON Structure

The `<script id="BATTLE_SERVER_DATA">` block contains 90+ fields. Below is the full structure with example values from a **finished** battle (Latvia vs Sweden, battle 851234).

> **Note:** Date fields use `new Date('...')` in the raw JavaScript. When parsing, convert these to strings first (see [Data Extraction](#data-extraction) below).

### Battle Metadata

<details>
<summary>Battle Metadata JSON</summary>

```json
{
  "initialLoad": true,
  "battleId": 851234,
  "battleZoneId": 37214694,
  "countryId": 71,
  "invaderId": 71,
  "defenderId": 38,
  "leftBattleId": 38,
  "rightBattleId": 71,
  "realInvaderId": 71,
  "realDefenderId": 38,
  "zoneId": 12,
  "currentRoundNumber": 12,
  "battleFinished": 1,
  "isResistance": false,
  "isCivilWar": false,
  "isLiberation": false,
  "isDictatorship": false,
  "isMultiZone": true,
  "mustInvert": false,
  "spectatorOnly": true,
  "onAirforceBattlefield": false,
  "battle_close_to_finish": 0
}
```

</details>

### Timestamps

<details>
<summary>Timestamps JSON</summary>

```json
{
  "battle_start_at": "Tue, 16 Dec 2025 17:59:05 -0800",
  "battle_critical_at": "Tue, 16 Dec 2025 19:59:05 -0800",
  "server_time": "Sun, 08 Feb 2026 02:01:41 -0800",
  "zoneElapsedTime": "1280:2:36"
}
```

</details>

> Raw JS uses `new Date('...')` for `battle_start_at`, `battle_critical_at`, and `server_time`.

### Campaign Points & Scores

<details>
<summary>Campaign Points JSON</summary>

```json
{
  "campaignPointsThreshold": 150,
  "points": {
    "defender": 210,
    "attacker": 1650
  },
  "campaignHeroId": {
    "38": 45422,
    "71": 1637391
  }
}
```

</details>

- `campaignPointsThreshold` -- points needed to win the campaign
- `campaignHeroId` -- keys are country IDs, values are citizen IDs of campaign heroes

### Battle Zone Situation

<details>
<summary>Battle Zone Situation JSON</summary>

```json
{
  "battleZoneSituation": {
    "37214691": 0,
    "37214692": 0,
    "37214693": 0,
    "37214694": 0,
    "37214695": 0
  },
  "activeBattleZones": [],
  "zoneStarted": false
}
```

</details>

- Keys in `battleZoneSituation` are `battleZoneId`s, values are situation codes (`0` = finished/inactive)
- `activeBattleZones` is empty for finished battles

### Player State

<details>
<summary>Player State JSON</summary>

```json
{
  "citizenId": 4690052,
  "militaryUnitId": 893,
  "regimentId": 1390,
  "division": 4,
  "currentDivision": 4,
  "naturalDivision": 4,
  "canSwitchDivisions": true,
  "health": 274,
  "level": 1716,
  "currentWeaponId": -1,
  "hasBazookaAmmo": -1,
  "weaponSkinId": 20,
  "opponentSkinId": 0,
  "mySideCountryId": 71,
  "showTravelPopup": false,
  "fightAgainstBrothers": false,
  "travelRequired": false,
  "has_jid": 1,
  "pvpKillCount": 0,
  "pvpShowLock": false,
  "isDeployOnly": false
}
```

</details>

- `currentWeaponId: -1` means no weapon selected
- `hasBazookaAmmo: -1` means no bazooka ammo available

### Legend Data

Per-country vehicle protection levels with XP progression:

<details>
<summary>Legend Data JSON</summary>

```json
{
  "legendData": {
    "71": {
      "id": 71,
      "tanks": {
        "level": 32,
        "maxLevel": 50,
        "xp": 34730991005,
        "requiredXP": 5269008995,
        "levelProgress": "86.8275%",
        "damageBonus": 60,
        "title": "Protector of Latvia",
        "vehicles": [35]
      },
      "aircraft": {
        "level": 3,
        "maxLevel": 50,
        "xp": 46545,
        "requiredXP": 25855,
        "levelProgress": "64.2887%",
        "damageBonus": 3,
        "title": "Protector of Latvia",
        "vehicles": []
      }
    },
    "38": {
      "id": 38,
      "tanks": {
        "level": 12,
        "maxLevel": 50,
        "xp": 6757808,
        "requiredXP": 242192,
        "levelProgress": "96.5401%",
        "damageBonus": 12,
        "title": "Protector of Sweden",
        "vehicles": []
      },
      "aircraft": {
        "level": 2,
        "maxLevel": 50,
        "xp": 23298,
        "requiredXP": 12202,
        "levelProgress": "65.6282%",
        "damageBonus": 2,
        "title": "Protector of Sweden",
        "vehicles": []
      }
    }
  }
}
```

</details>

- Keys are country IDs for the two sides of the battle
- `damageBonus` is the percentage bonus from legend level
- `vehicles` array lists active vehicle skin IDs

### Special Weapons

Three weapon types -- Big Bomb, Small Bomb, and Cruise Missile:

<details>
<summary>Special Weapons JSON</summary>

```json
{
  "specialWeapons": {
    "2_22": {
      "name": "Big Bomb",
      "id": "2_22",
      "industryId": 100,
      "quality": 22,
      "amount": 1944,
      "activable": 1,
      "deactivable": 0,
      "activableFromInventory": 0,
      "activableFromBattlefield": 1,
      "activationData": {
        "tooltip": "",
        "url": "/en/military/deploy-bomb",
        "params": { "bombId": 22 }
      },
      "active": 0,
      "icon": "/images/icons/industry/999/22.png",
      "tooltip": "Big Bomb",
      "token": "booster_q22",
      "attributes": {
        "firePower": { "id": "firePower", "name": "Fire power", "type": "use", "value": 5000000 },
        "durability": { "id": "durability", "name": "Durability", "type": "uses", "value": 1 },
        "expirationInfo": { "id": "expirationInfo", "type": "expirationInfo", "value": ["Expires on Day 6,665, 23:54"] }
      },
      "isRaw": 0,
      "isPartial": 0,
      "isBooster": 0,
      "isBomb": 1,
      "isPackBooster": 0,
      "isExpirable": 1,
      "type": "bomb",
      "is_expirable": 1
    },
    "2_215": {
      "name": "Cruise Missile",
      "id": "2_215",
      "industryId": 100,
      "quality": 215,
      "amount": 7,
      "activable": 1,
      "activableFromBattlefield": 1,
      "activationData": {
        "url": "/en/military/deploy-bomb",
        "params": { "bombId": 215 }
      },
      "attributes": {
        "firePower": { "value": 1500000 },
        "durability": { "value": 1 }
      },
      "isBomb": 1,
      "isExpirable": 1,
      "type": "bomb"
    },
    "2_21": {
      "name": "Small Bomb",
      "id": "2_21",
      "industryId": 100,
      "quality": 21,
      "amount": 1645,
      "activable": 1,
      "activableFromBattlefield": 1,
      "activationData": {
        "url": "/en/military/deploy-bomb",
        "params": { "bombId": 21 }
      },
      "attributes": {
        "firePower": { "value": 1500000 },
        "durability": { "value": 1 }
      },
      "isBomb": 1,
      "isExpirable": 1,
      "type": "bomb"
    }
  },
  "specialWeaponsDisabled": false,
  "bigBombsDisabled": false,
  "cruiseMissilesDisabled": false,
  "rocket_damage": 1500000,
  "bomb_damage": 5000000
}
```

</details>

- `activationData.url` -> POST to `/en/military/deploy-bomb` with `bombId` param
- Big Bomb deals 5,000,000 damage; Small Bomb and Cruise Missile deal 1,500,000
- Special weapon key format: `{industryId}_{quality}` (e.g., `2_22`)

### Boosters

<details>
<summary>Boosters Structure JSON</summary>

```json
{
  "boosters": {
    "active": [],
    "inactive": { "...see categories below..." },
    "canUseCatchupBooster": false,
    "catchupBoosterTextDetails": null,
    "events": []
  },
  "canUseCatchupBooster": false,
  "regimentBoosters": [],
  "regimentBooster": false,
  "eventDamageBoosters": [],
  "eventDamageBooster": false
}
```

</details>

**Booster categories** (in `boosters.inactive`):

| Category | Description | Example IDs |
|----------|-------------|-------------|
| `catchup` | Ghost boosters -- add % of best damage | `100_catchup_30_60` |
| `damage` | Ground damage multiplier | `100_damage_5_86400`, `100_damage_10_600`, `100_damage_15_600` |
| `speed` | Damage accelerators (hit faster) | `100_speed_1_180`, `100_speed_2_600`, `100_speed_3_600` |
| `prestige_points` | Prestige bonus per hit | `100_prestige_points_1_600` |
| `deploy_size` | Deploy size multiplier | `100_deploy_size_100_600`, `100_deploy_size_500_600` |
| `ground_deploy_influence` | Ground influence bonus | `100_ground_deploy_influence_1_600`, `100_ground_deploy_influence_3_600` |
| `ground_deploy_rank_points` | Ground rank points bonus | `100_ground_deploy_rank_points_1_600`, `100_ground_deploy_rank_points_3_600` |
| `air_damage` | Air damage multiplier | `100_air_damage_2_600`, `100_air_damage_5_600`, `100_air_damage_10_600` |
| `air_deploy_influence` | Air influence bonus | `100_air_deploy_influence_1_600`, `100_air_deploy_influence_3_600` |
| `air_deploy_rank_points` | Air rank points bonus | `100_air_deploy_rank_points_1_600`, `100_air_deploy_rank_points_3_600` |

**Booster ID naming convention:** `{industryId}_{type}_{quality}_{duration}` (e.g., `100_damage_10_7200` = +100% damage for 2 hours)

<details>
<summary>Full Booster Item Structure (example)</summary>

```json
{
  "name": "+30% Ghost Booster",
  "id": "100_catchup_30_60",
  "industryId": 100,
  "quality": 30,
  "amount": 812,
  "activable": 1,
  "deactivable": 0,
  "activableFromInventory": 0,
  "activableFromBattlefield": 1,
  "activationData": {
    "tooltip": "Add 30% of the Best Damage per Hit in a Ground Battle to your Damage for 1 minute",
    "url": "/en/military/fight-activateBooster",
    "params": {
      "type": "catchup",
      "quality": 30,
      "duration": 60
    }
  },
  "active": 0,
  "icon": "https://www.erepublik.net/images/icons/boosters/52px/catchup.png?1702893011",
  "tooltip": "Add 30% of the Best Damage per Hit in a Ground Battle to your Damage for 1 minute",
  "token": "",
  "attributes": {
    "damageBoost": {
      "id": "damageBoost",
      "name": "Add 30% of the Best Damage per Hit in a Ground Battle to your Damage for 1 minute",
      "type": "use",
      "value": "30%"
    },
    "duration": {
      "id": "duration",
      "name": "Duration",
      "type": "minute",
      "value": 1
    }
  },
  "isRaw": 0,
  "isPartial": 0,
  "isBooster": 1,
  "isBomb": 0,
  "isPackBooster": 0,
  "isExpirable": 0,
  "type": "catchup",
  "duration": 60,
  "canActivateBooster": 1
}
```

</details>

- `activationData.url` -> POST to `/en/military/fight-activateBooster` with `type`, `quality`, `duration` params
- Speed boosters use `damageAcceleration` attribute (values like `"x2"`, `"x5"`, `"x9"`) instead of `damageBoost`

**Known boosters reference:**

| ID | Name | Effect |
|----|------|--------|
| `100_catchup_30_60` | +30% Ghost Booster | +30% of best damage for 1 min |
| `100_damage_5_86400` | +50% Ground Damage | +50% for 24h |
| `100_damage_5_28800` | +50% Ground Damage | +50% for 8h |
| `100_damage_5_7200` | +50% Ground Damage | +50% for 2h |
| `100_damage_10_86400` | +100% Ground Damage | +100% for 24h |
| `100_damage_10_28800` | +100% Ground Damage | +100% for 8h |
| `100_damage_10_7200` | +100% Ground Damage | +100% for 2h |
| `100_damage_10_3600` | +100% Ground Damage | +100% for 1h |
| `100_damage_10_600` | +100% Ground Damage | +100% for 10 min |
| `100_damage_15_600` | +150% Ground Damage | +150% for 10 min |
| `100_speed_1_180` | x2 Damage Accelerator | x2 speed for 3 min |
| `100_speed_2_600` | x5 Damage Accelerator | x5 speed for 10 min |
| `100_speed_2_300` | x5 Damage Accelerator | x5 speed for 5 min |
| `100_speed_3_600` | x9 Damage Accelerator | x9 speed for 10 min |
| `100_prestige_points_1_600` | +1 Prestige Points | +1 prestige for 10 min |
| `100_deploy_size_100_600` | +100% Deploy Size | +100% for 10 min |
| `100_deploy_size_200_1200` | +200% Deploy Size | +200% for 20 min |
| `100_deploy_size_200_600` | +200% Deploy Size | +200% for 10 min |
| `100_deploy_size_500_600` | +500% Deploy Size | +500% for 10 min |
| `100_ground_deploy_influence_1_600` | +10% Ground Influence | +10% for 10 min |
| `100_ground_deploy_influence_2_1200` | +20% Ground Influence | +20% for 20 min |
| `100_ground_deploy_influence_2_600` | +20% Ground Influence | +20% for 10 min |
| `100_ground_deploy_influence_3_600` | +30% Ground Influence | +30% for 10 min |
| `100_ground_deploy_rank_points_1_600` | +10% Ground Rank Points | +10% for 10 min |
| `100_ground_deploy_rank_points_2_1200` | +20% Ground Rank Points | +20% for 20 min |
| `100_ground_deploy_rank_points_2_600` | +20% Ground Rank Points | +20% for 10 min |
| `100_ground_deploy_rank_points_3_600` | +30% Ground Rank Points | +30% for 10 min |
| `100_air_damage_2_600` | +20% Air Damage | +20% for 10 min |
| `100_air_damage_5_600` | +50% Air Damage | +50% for 10 min |
| `100_air_damage_5_300` | +50% Air Damage | +50% for 5 min |
| `100_air_damage_10_600` | +100% Air Damage | +100% for 10 min |
| `100_air_deploy_influence_1_600` | +10% Air Influence | +10% for 10 min |
| `100_air_deploy_influence_2_1200` | +20% Air Influence | +20% for 20 min |
| `100_air_deploy_influence_2_600` | +20% Air Influence | +20% for 10 min |
| `100_air_deploy_influence_3_600` | +30% Air Influence | +30% for 10 min |
| `100_air_deploy_rank_points_1_600` | +10% Air Rank Points | +10% for 10 min |
| `100_air_deploy_rank_points_2_1200` | +20% Air Rank Points | +20% for 20 min |
| `100_air_deploy_rank_points_2_600` | +20% Air Rank Points | +20% for 10 min |
| `100_air_deploy_rank_points_3_600` | +30% Air Rank Points | +30% for 10 min |

### Rounds

The `rounds` object contains entries keyed by `battleZoneId`. Each battle has 5 zones per round (Div I-IV + Aircraft), so a 12-round battle has 60 entries:

<details>
<summary>Rounds JSON</summary>

```json
{
  "rounds": {
    "37214695": {
      "id": 37214695,
      "zone": 12,
      "round": 12,
      "division": 11,
      "created_at": 1765936745,
      "type": "aircraft",
      "terrain_type_id": null,
      "started": 1,
      "finished": 1
    },
    "37214691": {
      "id": 37214691,
      "zone": 12,
      "round": 12,
      "division": 1,
      "created_at": 1765936745,
      "type": "tanks",
      "terrain_type_id": null,
      "started": 1,
      "finished": 1
    }
  }
}
```

</details>

**Division mapping:** `1` = Div I, `2` = Div II, `3` = Div III, `4` = Div IV, `11` = Aircraft

**Type values:** `"tanks"` for ground divisions, `"aircraft"` for air division

**Terrain types observed:**
- `terrain_type_id: 3` -- ground divisions in round 1
- `terrain_type_id: 0` -- aircraft in round 1
- `terrain_type_id: null` -- all subsequent rounds

### Travel Data

<details>
<summary>Travel Data JSON</summary>

```json
{
  "travel": {
    "travelData": {
      "travelRequired": false,
      "isCivilWar": null,
      "isResistance": null,
      "battleFinished": true,
      "travelData": [],
      "allDestinations": {
        "countries": [],
        "regions": []
      }
    },
    "battleZones": [
      {
        "label": "aircraft",
        "name": "Aircraft",
        "division": 11,
        "zoneId": 12,
        "battleZoneId": 37214695,
        "selected": false,
        "disabled": false,
        "terrain": { "id": 0, "codename": "aircraft", "name": "Aircraft" }
      },
      {
        "label": "division_i",
        "name": "Division I",
        "division": 1,
        "zoneId": 12,
        "battleZoneId": 37214691,
        "selected": false,
        "disabled": false,
        "terrain": { "id": 0, "codename": "standard", "name": "Standard" }
      },
      {
        "label": "division_iv",
        "name": "Division IV",
        "division": 4,
        "zoneId": 12,
        "battleZoneId": 37214694,
        "selected": true,
        "disabled": false,
        "terrain": { "id": 0, "codename": "standard", "name": "Standard" }
      }
    ],
    "travelMethod": "preferCurrency"
  }
}
```

</details>

- `battleZones` lists all available divisions with their `battleZoneId` and terrain info
- `selected: true` indicates the division the player is currently viewing
- `travelMethod` options: `"preferCurrency"`

### Battle Effects

<details>
<summary>Battle Effects JSON</summary>

```json
{
  "battle_effects": {
    "valentinesDay": { "is_active": 0 },
    "snowFight": { "is_active": 0 },
    "heroRewardEffect": null
  }
}
```

</details>

### Countries Map

A lookup table mapping country IDs to names (74 entries). Truncated:

<details>
<summary>Countries Map JSON</summary>

```json
{
  "countries": {
    "1": "Romania",
    "9": "Brazil",
    "10": "Italy",
    "11": "France",
    "12": "Germany",
    "13": "Hungary",
    "14": "China",
    "15": "Spain",
    "23": "Canada",
    "24": "USA",
    "29": "United-Kingdom",
    "35": "Poland",
    "38": "Sweden",
    "41": "Russia",
    "71": "Latvia",
    "..."
  }
}
```

</details>

### Texts Map

Localized UI strings (56 keys). Used for battlefield UI labels:

<details>
<summary>Texts Map JSON</summary>

```json
{
  "texts": {
    "consume_food": "Consume Food",
    "cosume_energy_bar_text": "Consume Energy Bar",
    "cosume_winter_treat_text": "Consume Treat",
    "buy_health_kit": "Get more energy",
    "won_zone": "won the previous battle",
    "division": "Division",
    "the_airforce": "The Airforce",
    "won_division": "won this battle",
    "won_battle": "won this campaign",
    "yourHealth": "Your Energy:",
    "opponentHealth": "Opponent Energy:",
    "defenderHero": "Our battle hero",
    "attackerHero": "...",
    "conqueredText": "%%1%% CONQUERED %%2%%",
    "defendedText": "...",
    "..."
  }
}
```

</details>

- Template placeholders: `%%1%%`, `%%2%%` are used for variable substitution

### Objectives & Missions

<details>
<summary>Objectives & Missions JSON</summary>

```json
{
  "hasActiveObjective": false,
  "hasDestroyedObjective": false,
  "canDeployObjective": false,
  "tutorial": 0,
  "mission1Completed": true,
  "mission2Completed": true,
  "mission3Completed": true
}
```

</details>

### Other Fields

<details>
<summary>Other Fields JSON</summary>

```json
{
  "deployment": null,
  "aoe_effects": [],
  "aoeMyDivision": true,
  "aoeOtherDivisionData": [],
  "fightersData": [],
  "historyStats": [],
  "culture": "en",
  "csrfToken": "99e749d8bab079654efe0b67c298b9a2",
  "isLoggedIn": true,
  "ERPK_ECONOMY_HOST": "economy.erepublik.com",
  "ERPK_MAIN_HOST": "www.erepublik.com",
  "extras": "winter treats",
  "webDeployEnabled": true,
  "battleDetermination": 1,
  "fightSessionKey": "",
  "fighterCountryId": 72,
  "oldEnemyHealth": 51,
  "remainingHospitals": 0
}
```

</details>

- `deployment` -- `null` for finished battles; likely populated during active combat with deployment state
- `fightersData`, `historyStats` -- empty arrays in finished battles; populated during active rounds
- `aoe_effects` -- Area of Effect active effects (empty when battle is over)
- `battleDetermination` -- `1` when battle determination is active (affects campaign point calculation), `0` otherwise
- `fightSessionKey` -- session key for fight actions; empty string when not actively fighting
- `fighterCountryId` -- country ID the player is currently fighting for
- `oldEnemyHealth` -- previous opponent's health value in PvP combat
- `remainingHospitals` -- number of hospital items remaining for the player

---

## Response Fields Reference

| Field | Type | Description |
|-------|------|-------------|
| `battleId` | number | Campaign/battle identifier |
| `battleZoneId` | number | Current battle zone (division) being viewed |
| `countryId` | number | Country ID of the viewer's citizenship |
| `invaderId` | number | Attacking country ID |
| `defenderId` | number | Defending country ID |
| `realInvaderId` / `realDefenderId` | number | Actual countries (differs from `invaderId`/`defenderId` in liberation wars) |
| `leftBattleId` / `rightBattleId` | number | Country IDs for left/right sides of the UI |
| `currentRoundNumber` | number | Current round number (1-based) |
| `battleFinished` | number | `1` if battle is over, `0` if active |
| `isResistance` | boolean | True if this is a resistance war |
| `isCivilWar` | boolean | True if this is a civil war |
| `isLiberation` | boolean | True if this is a liberation war |
| `isDictatorship` | boolean | True if dictatorship battle |
| `isMultiZone` | boolean | True if battle has multiple divisions |
| `mustInvert` | boolean | Whether to invert the left/right display |
| `spectatorOnly` | boolean | True if the player can only watch (e.g., finished battle) |
| `onAirforceBattlefield` | boolean | True if viewing the aircraft division |
| `battleZoneSituation` | object | Map of `battleZoneId` -> situation code (`0` = finished) |
| `activeBattleZones` | array | Currently active zone IDs (empty when finished) |
| `campaignPointsThreshold` | number | Points needed to win the campaign (typically 83 or 150) |
| `points` | object | `{ defender: number, attacker: number }` -- campaign points |
| `campaignHeroId` | object | Country ID -> citizen ID of campaign hero |
| `battle_start_at` | Date | When the battle started |
| `battle_critical_at` | Date | When the battle becomes critical |
| `server_time` | Date | Current server time |
| `zoneElapsedTime` | string | Format `"hours:minutes:seconds"` since zone started |
| `citizenId` | number | Logged-in player's citizen ID |
| `division` | number | Player's natural division (1-4) |
| `currentDivision` | number | Division currently being viewed |
| `health` | number | Player's current energy |
| `level` | number | Player's experience level |
| `currentWeaponId` | number | Selected weapon ID (`-1` = none) |
| `hasBazookaAmmo` | number | Bazooka ammo count (`-1` = none) |
| `legendData` | object | Per-country legend progression (tanks/aircraft levels) |
| `specialWeapons` | object | Available bombs/missiles keyed by `{industryId}_{quality}` |
| `boosters` | object | Active and inactive boosters by category |
| `rounds` | object | All rounds keyed by `battleZoneId` with division/type/terrain info |
| `travel` | object | Travel data, battle zones list, and travel method |
| `battle_effects` | object | Active seasonal effects (valentinesDay, snowFight, heroRewardEffect) |
| `countries` | object | Country ID -> name lookup map (74 entries) |
| `texts` | object | Localized UI strings (56 keys) with `%%N%%` placeholders |
| `deployment` | object/null | Active deployment state (`null` when no deployment) |
| `webDeployEnabled` | boolean | Whether web-based deployment is enabled |
| `battleDetermination` | number | `1` if battle determination is active, `0` otherwise |
| `fightSessionKey` | string | Session key for fight actions (empty when not fighting) |
| `fighterCountryId` | number | Country ID the player is fighting for |
| `oldEnemyHealth` | number | Previous opponent's health in PvP combat |
| `remainingHospitals` | number | Remaining hospital items for the player |

---

## Data Extraction

To extract the `BATTLE_SERVER_DATA` JSON from the HTML response:

**Python example:**

```python
import re
import json

# 1. Extract the raw JS object
match = re.search(
    r'id="BATTLE_SERVER_DATA".*?SERVER_DATA = \$j\.extend\(SERVER_DATA, (\{.*?\})\)',
    html_content,
    re.DOTALL
)
raw_js = match.group(1)

# 2. Convert new Date('...') calls to JSON strings
json_str = re.sub(r"new Date\('([^']+)'\)", r'"\1"', raw_js)

# 3. Parse as JSON
data = json.loads(json_str)
```

---

## Notes

- **HTML page, not JSON API**: This endpoint returns a full HTML document (~221 KB). The battle state is embedded in JavaScript, not returned as JSON. You must parse the HTML to extract the data.
- **Two `SERVER_DATA` blocks**: The page contains two `<script>` blocks that populate `SERVER_DATA`:
  1. Line ~49: Generic session data (`csrfToken`, `isLoggedIn`, `serverTime`, `fbAppId`, etc.)
  2. `<script id="BATTLE_SERVER_DATA">` (line ~1311): Battle-specific state merged via `$j.extend(SERVER_DATA, {...})`
- **Division numbering**: Ground divisions use `1`-`4` (Div I-IV), aircraft uses `11`
- **`spectatorOnly: true`** appears when the battle is finished -- the player can only view the results
- **Empty arrays in finished battles**: `activeBattleZones`, `fightersData`, `historyStats`, `aoe_effects`, `aoeOtherDivisionData` are all empty when the battle is over. They are populated during active combat rounds.
- **`deployment` is `null`** in finished battles -- this field contains active deployment state during combat
- **Booster activation endpoint**: `POST /en/military/fight-activateBooster` with params `type`, `quality`, `duration` (discovered from `activationData`)
- **Bomb deployment endpoint**: `POST /en/military/deploy-bomb` with param `bombId` (discovered from `specialWeapons.activationData`)
- **Booster ID naming convention**: `{industryId}_{type}_{quality}_{duration}` (e.g., `100_damage_10_7200`)
- **Date fields**: `battle_start_at`, `battle_critical_at`, `server_time` use `new Date('...')` in the raw JavaScript -- these must be converted to strings before JSON parsing
- **Text placeholders**: The `texts` map uses `%%1%%`, `%%2%%` for variable substitution (e.g., `"%%1%% CONQUERED %%2%%"`)
- **`terrain_type_id`**: Observed values: `0` (aircraft round 1), `3` (ground round 1 -- one battle), `4` (ground round 1 -- another battle), `null` (subsequent rounds). Different terrain types may represent different battlefield terrain bonuses.
- **Active vs Finished battle differences**:
  - `activeBattleZones` -- empty array when finished; populated with objects `{ battleZoneId, zoneId, division, type, terrainTypeId, sector }` during active battles
  - `battleFinished` -- `0` for active, `1` for finished
  - `spectatorOnly` -- `false` for active (player can fight), `true` for finished
  - `deployment` -- `null` when not deploying; contains `{ myTotalDamage, isMaxLevel, rankText, rankIcon, currentRankPoints, nextRankPoints, rankPercentProgress }` when deployment UI is shown
  - `fightersData`, `historyStats` -- populated during active rounds
  - `battleDetermination` -- present in both active and finished battles; `1` when determination is active
  - `fightSessionKey` -- empty string when not in an active fight session; populated with a session key during combat
  - `fighterCountryId` -- the country ID the player chose to fight for; present in both active and finished battles
  - `oldEnemyHealth` -- PvP opponent's previous health; may be `0` in finished battles
  - `remainingHospitals` -- hospital count available at current location; present in both active and finished battles
- **Player strength**: Available via `erepublik.promos.April1st2017.playerStrength` (e.g., `414481.469`) -- the "April 1st 2017" promo is permanently active and carries the player's current strength value
- **Pomelo WebSocket**: Real-time battle updates via `gate.erepublik.com:3050` with auth token = session token (`erpk` cookie value). See `erepublik.settings.pomelo` in the page's global object. Details in [websocket.md](websocket.md).

---

## Additional Embedded Data Objects

Beyond `BATTLE_SERVER_DATA`, the battlefield page embeds several other useful data objects:

### `erepublik.citizen` (line ~47)

Full citizen state including identity, economy, party, residence, daily orders, terrain skills (43 keys):

<details>
<summary>erepublik.citizen JSON</summary>

```json
{
  "citizenId": 4690052,
  "isOrganization": 0,
  "isAdult": true,
  "isNewCitizen": false,
  "country": 72,
  "citizenshipCountryId": 72,
  "name": "driver sti",
  "avatar": "https://cdnt.erepublik.net/enQ9lXJMVlMaSYRgyEsa10ndQwA=/55x55/smart/avatars/Citizens/2011/04/12/1907aee62f42c11beba21159840cdc16.png?fa70b874e2f337186b979af9404828eb",
  "currency": "LTL",
  "citizenshipCurrencyName": "LTL",
  "isEmpire": false,
  "isDictatorship": 0,
  "countryLocationId": 72,
  "regionLocationId": 663,
  "countryLocationName": "Lithuania",
  "countryLocationPermalink": "Lithuania",
  "residence": {
    "hasResidence": 1,
    "cityId": 710,
    "regionId": 663,
    "countryId": 72,
    "bonuses": {
      "has_residence": 1,
      "is_in_residence": 1,
      "valid_until": 0,
      "time_remaining": 0,
      "num_houses": 1,
      "energy_bonus": 100,
      "recovery_bonus": 4,
      "description": "Residence bonuses available"
    }
  },
  "division": 4,
  "muId": 893,
  "muCountryId": 72,
  "regimentId": 1390,
  "party": {
    "id": 3773,
    "party_id": 3773,
    "is_party_president": 0,
    "name": "Lietuvos Tevynes Sajunga",
    "economical_orientation": "Center",
    "stripped_title": "lietuvos-tevynes-sajunga",
    "has_avatar": 1,
    "created_at": 1315032136,
    "joined_at": 1681043826
  },
  "energy": 900,
  "energyToRecover": 10220,
  "energyFromFoodRemaining": 0,
  "energyPerInterval": 64,
  "hasFoodInInventory": true,
  "gold": 75678,
  "currencyAmount": 7120147,
  "userLevel": 1725,
  "hasCoreLoopAccess": false,
  "canWorkTrainAgainIn": 70214,
  "currentExperiencePoints": 8503402,
  "dailyOrders": [
    {
      "battleId": 869119,
      "sideCountryId": 72,
      "regionId": 372,
      "title": "Fight for Lithuania in Jiangsu",
      "battleLink": "//www.erepublik.com/en/military/battlefield-choose-side/869119/72",
      "setBy": {
        "name": "priorities",
        "id": 0,
        "role": "Military Unit's priorities",
        "description": "Set by Military Unit's priorities"
      },
      "required": 25,
      "progress": 0,
      "completed": false,
      "isCivilWar": false
    }
  ],
  "dailiesToCollect": 0,
  "canChangeMPPPriorities": false,
  "canChangeTerrainPriorities": false,
  "canSwitchDivisions": true,
  "terrainSkills": {
    "1": { "terrain_id": 1, "skill_points": 40000 },
    "2": { "terrain_id": 2, "skill_points": 33333 },
    "3": { "terrain_id": 3, "skill_points": 17728 },
    "4": { "terrain_id": 4, "skill_points": 0 },
    "5": { "terrain_id": 5, "skill_points": 33333 },
    "6": { "terrain_id": 6, "skill_points": 40000 },
    "7": { "terrain_id": 7, "skill_points": 0 },
    "8": { "terrain_id": 8, "skill_points": 0 },
    "9": { "terrain_id": 9, "skill_points": 33333 },
    "10": { "terrain_id": 10, "skill_points": 33333 },
    "11": { "terrain_id": 11, "skill_points": 0 },
    "12": { "terrain_id": 12, "skill_points": 0 },
    "13": { "terrain_id": 13, "skill_points": 0 },
    "14": { "terrain_id": 14, "skill_points": 0 }
  },
  "isTopCustomer": true,
  "loyaltyPoints": 2620,
  "boxKeys": { "cupid_key": 223 }
}
```

</details>

**New fields (compared to earlier captures):**

| Field | Type | Description |
|-------|------|-------------|
| `avatar` | string | Citizen avatar URL (CDN, with image proxy path) |
| `isOrganization` | number | `0` for regular citizen, `1` for organization account |
| `isAdult` | boolean | Whether the citizen is an adult account |
| `isNewCitizen` | boolean | Whether the citizen is considered "new" |
| `isEmpire` | boolean | Whether the citizen's country is currently an empire |
| `isDictatorship` | number | `0` or `1` -- whether the country is under dictatorship |
| `citizenshipCurrencyName` | string | Currency name for citizenship country (e.g., `"LTL"`) |
| `countryLocationId` | number | Country ID where the citizen is currently located |
| `countryLocationName` | string | Display name of current country location |
| `countryLocationPermalink` | string | URL-friendly country name (e.g., `"Lithuania"`) |
| `regionLocationId` | number | Region ID of current location |
| `muCountryId` | number | Country ID of the citizen's military unit |
| `currentExperiencePoints` | number | Current XP total |
| `canWorkTrainAgainIn` | number | Seconds until next work/train action is available |
| `dailiesToCollect` | number | Number of uncollected daily rewards |
| `hasCoreLoopAccess` | boolean | Whether citizen has access to the core loop feature |
| `canChangeMPPPriorities` | boolean | Can change MPP (Mutual Protection Pact) priorities |
| `canChangeTerrainPriorities` | boolean | Can change terrain priorities |
| `party` | object | Full party membership info (see below) |

**`party` sub-object:**

| Field | Type | Description |
|-------|------|-------------|
| `id` / `party_id` | number | Party ID (duplicated) |
| `is_party_president` | number | `0` or `1` -- whether citizen is party president |
| `name` | string | Party display name |
| `economical_orientation` | string | Political orientation (e.g., `"Center"`) |
| `stripped_title` | string | URL-safe slug of the party name |
| `has_avatar` | number | `0` or `1` -- whether party has a custom avatar |
| `created_at` | number | Unix timestamp of party creation |
| `joined_at` | number | Unix timestamp of when the citizen joined |

**Updated `dailyOrders[]` fields:**

| Field | Type | Description |
|-------|------|-------------|
| `battleLink` | string | Protocol-relative URL to the battlefield choose-side page |
| `setBy` | object | Who set the order: `{ name, id, role, description }` |
| `isCivilWar` | boolean | Whether the battle is a civil war |

**Updated `residence.bonuses` fields:**

| Field | Type | Description |
|-------|------|-------------|
| `valid_until` | number | Expiry timestamp (`0` = permanent/no expiry) |
| `time_remaining` | number | Seconds remaining until bonuses expire (`0` = permanent) |
| `num_houses` | number | Number of houses the citizen owns |
| `description` | string | Human-readable description (e.g., `"Residence bonuses available"`) |

**`terrainSkills`** -- expanded from 2 to 14 terrain entries (IDs 1-14), reflecting the full terrain system. Each entry: `{ terrain_id: number, skill_points: number }`.

### `energyData` (line ~272)

<details>
<summary>energyData JSON</summary>

```json
{
  "energy": 772,
  "energyPoolLimit": 10220,
  "recoverableEnergy": 0,
  "energyPerInterval": 64,
  "energyStatus": "recovering"
}
```

</details>

### `globalNS.userInfo` (line ~1069)

```javascript
globalNS.userInfo.wellness = 772;
globalNS.userInfo.specialFoodAmount = 71353;  // treats count
globalNS.userInfo.specialFoodValue = 100;     // energy per treat
globalNS.userInfo.energyPerInterval = 64;
```

### Deploy Rank Data (line ~2653)

<details>
<summary>Deploy Rank Data JSON</summary>

```json
{
  "deploy": {
    "myTotalDamage": 0,
    "isMaxLevel": false,
    "rankText": "Air Commodore *****",
    "rankIcon": "//www.erepublik.net/images/modules/ranks/aircraft/32px/061_air_commodore.png",
    "currentRankPoints": 59284166,
    "nextRankPoints": 76360000,
    "rankPercentProgress": 3.088
  }
}
```

</details>
