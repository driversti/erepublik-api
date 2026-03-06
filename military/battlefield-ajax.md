# Battlefield AJAX Endpoints

#erepublik #api #military #battlefield #ajax

[< Back to Military Module](README.md)

AJAX endpoints called by the battlefield page's JavaScript (AngularJS controllers and jQuery) to perform actions during combat. All require the `erpk` session cookie and most require the CSRF token from `SERVER_DATA.csrfToken`.

**Related:** [battlefield.md](battlefield.md) | [deployment.md](deployment.md) | [websocket.md](websocket.md) | [frontend-reference.md](frontend-reference.md)

---

## Common Headers for All AJAX Endpoints

| Header | Value | Required |
|--------|-------|----------|
| `Cookie` | `erpk=YOUR_SESSION_TOKEN` | Yes |
| `Content-Type` | `application/x-www-form-urlencoded` | Yes (POST) |
| `X-Requested-With` | `XMLHttpRequest` | Recommended |
| `Referer` | `https://www.erepublik.com/en/military/battlefield/{battleId}` | Recommended |

---

## Battle Console (Get Live Battle State)

**Method:** POST
**URL:** `/en/military/battle-console`
**Auth Required:** Yes

### Description

Fetches real-time battle zone state including live stats (domination bar), fighter rankings, combat orders, war history, and allied forces. This is the **primary polling endpoint** for battlefield UI updates. Called by `BattleStatsController`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `zoneId` | number | Yes | The zone (round) number |
| `battleZoneId` | number | Yes | The specific battle zone ID (division) |
| `action` | string | Yes | Action type: `"battleStatistics"`, `"fighterStatistics"`, `"warHistory"`, `"combatOrders"` |
| `_token` | string | Yes | CSRF token from `SERVER_DATA.csrfToken` |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/battle-console' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&zoneId=8&battleZoneId=38158390&action=battleStatistics&_token=YOUR_CSRF_TOKEN'
```

</details>

### Notes

- `action=battleStatistics` -- returns live domination stats, wall info, allied forces. See [Get Battle Statistics](battle-info.md#get-battle-statistics)
- `action=fighterStatistics` -- returns top fighters on each side with damage rankings. See [Get Battle Statistics (Console)](battle-info.md#get-battle-statistics-console)
- `action=combatOrders` -- returns active combat orders (bounties) for each side
- `action=warHistory` -- returns previous rounds' results. See [Get War History](battle-info.md#get-war-history-previous-rounds)
- `action=battleConsole` -- returns overall battle state for all divisions. See [Get Battle Console Data](battle-info.md#get-battle-console-data)

---

## Deploy Bomb

**Method:** POST
**URL:** `/en/military/deploy-bomb`
**Auth Required:** Yes

### Description

Deploys a special weapon (Big Bomb, Small Bomb, or Cruise Missile) in the current battle zone. Called from `hit_bomb()` in `battle_new.js`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `bombId` | number | Yes | The bomb type ID: `21` (Small Bomb), `22` (Big Bomb), `215` (Cruise Missile) |
| `_token` | string | Yes | CSRF token |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/deploy-bomb' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&battleZoneId=38158390&_token=YOUR_CSRF_TOKEN&bombId=22'
```

</details>

### Notes

- Bomb damage: Big Bomb = 5,000,000; Small Bomb = 1,500,000; Cruise Missile = 1,500,000
- Can be disabled server-side: check `SERVER_DATA.bigBombsDisabled` and `SERVER_DATA.cruiseMissilesDisabled`
- Response is JSON -- check for `error` field

---

## Activate Booster

**Method:** POST
**URL:** `/en/military/fight-activateBooster`
**Auth Required:** Yes

### Description

Activates a combat booster (speed accelerator, damage boost, prestige points, deploy size, influence, rank points). Called by `SpecialItemsController`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `type` | string | Yes | Booster type: `speed`, `damage`, `catchup`, `prestige_points`, `deploy_size`, `air_damage`, `ground_deploy_influence`, `air_deploy_influence`, `ground_deploy_rank_points`, `air_deploy_rank_points` |
| `quality` | number | Yes | Booster quality tier (e.g., `1`, `2`, `3`, `5`, `10`, `100`, `200`, `500`) |
| `duration` | number | Yes | Duration in seconds (e.g., `60`, `180`, `300`, `600`, `1200`, `3600`, `7200`, `28800`, `86400`) |
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `sideId` | number | Yes | Country ID of the side you're fighting for (`SERVER_DATA.mySideCountryId`) |
| `_token` | string | Yes | CSRF token |
| `confirmExtendBooster` | string | No | Set to `"yes"` when extending/replacing an active booster of the same type |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/fight-activateBooster' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'type=speed&quality=2&duration=600&battleId=869119&battleZoneId=38158390&sideId=72&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "status": "succes",
  "message": "Booster activated"
}
```

</details>

### Notes

- Success status is `"succes"` (note the typo -- single 's')
- If a booster of the same type is already active, the UI prompts for confirmation and resends with `confirmExtendBooster=yes`
- Available boosters and their params are listed in `SERVER_DATA.boosters.inactive`
- Each booster's `canActivateBooster` flag indicates if it can currently be activated

---

## Battlefield Travel (Choose Side & Travel)

**Method:** POST
**URL:** `/en/main/battlefieldTravel`
**Auth Required:** Yes

### Description

Handles choosing a side in the battle and traveling to the region where the fight takes place. Called by `BattleSetupController`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `sideCountryId` | number | Yes | Country ID to fight for |
| `battleZoneId` | number | Yes | Target battle zone ID |
| `_token` | string | Yes | CSRF token |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/battlefieldTravel' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&sideCountryId=72&battleZoneId=38158390&_token=YOUR_CSRF_TOKEN'
```

</details>

### Notes

- Travel costs are visible in `SERVER_DATA.travel.travelData.allDestinations.regions[regionId].cost`
- Can use travel tickets instead of currency -- check `ticket` and `ticketAmount` fields
- If `travelRequired` is `false`, the player is already in a valid region
- The `travelMethod` field indicates preference: `"preferCurrency"` or ticket-based

---

## Switch Division

**Method:** POST
**URL:** `/en/main/switch-division`
**Auth Required:** Yes

### Description

Switches the player to a different division (Div I-IV or Aircraft). Requires `canSwitchDivisions: true` in citizen data. Causes a page reload on success. Called from `battle_new.js`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `countryId` | number | Yes | Player's country ID (`SERVER_DATA.countryId`) |
| `zoneId` | number | Yes | Current zone/round number |
| `division` | number | Yes | Target division: `1`-`4` (ground) or `11` (aircraft) |
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `"activate"` |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/switch-division' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&countryId=72&zoneId=8&division=4&_token=YOUR_CSRF_TOKEN&action=activate'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false
}
```

</details>

### Notes

- On success (`error: false`), the client reloads the page
- Only available when `SERVER_DATA.canSwitchDivisions` is `true`
- Division `11` = Aircraft

---

## PvP Queue (Join/Leave)

**Method:** POST
**URL:** `/en/main/pvp-queue`
**Auth Required:** Yes

### Description

Joins or leaves the PvP (duel) queue in a battle zone. Minimum 100 energy required to join. Called from `battle_new.js`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `"join"` or `"leave"` |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/pvp-queue' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&battleZoneId=38158390&_token=YOUR_CSRF_TOKEN&action=join'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "message": "SUCCESS"
}
```

</details>

### Notes

- Messages: `"SUCCESS"`, `"JOIN"` for successful queue join
- `pvpShowLock` in `SERVER_DATA` gates whether PvP is available
- Points threshold: PvP disabled when both sides exceed 1400 campaign points

---

## PvP Inventory

**Method:** GET
**URL:** `/en/military/pvp-inventory/`
**Auth Required:** Yes

### Description

Returns an HTML fragment containing the player's PvP-related inventory. Rendered inline in the battlefield UI.

### Notes

- Returns **HTML**, not JSON
- Used to display weapons/items available during PvP duels

---

## Activate Battle Effect

**Method:** POST
**URL:** `/en/main/fight-activateBattleEffect`
**Auth Required:** Yes

### Description

Activates a special battle effect (e.g., snow fight, Valentine's Day). Called by `SnowFightFactory`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `citizenId` | number | Yes | Player's citizen ID |
| `battleId` | number | Yes | The battle/campaign ID |
| `type` | string | Yes | Effect type (e.g., `"snowFight"`, `"valentinesDay"`) |
| `_token` | string | Yes | CSRF token |

---

## List Battle Objectives

**Method:** GET
**URL:** `/en/main/fight-listObjectives`
**Auth Required:** Yes

### Description

Returns the list of battle objectives (special destructible targets). Called by `ObjectivesFactory`.

---

## Battle Stats

**Method:** POST
**URL:** `/en/military/battle-stats/{battleId}`
**Auth Required:** Yes

### Description

Returns battle statistics data. The `{battleId}` is appended to the URL path.

---

## Energy Refill - Get Inventory

**Method:** POST
**URL:** `/en/economy/energyRefill-getInventory`
**Auth Required:** Yes

### Description

Fetches the player's food and energy bar inventory for the energy refill popup. Called by `EnergyPopupFactory`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |

---

## Refill Energy (Eat Food / Use Energy Bar)

**Method:** POST
**URL:** `/en/economy/refillEnergy`
**Auth Required:** Yes

### Description

Consumes food or energy bars to restore energy. Called by `EnergyPopupFactory.recoverEnergy()`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| *(additional params)* | | | Type and quality of food/energy bar to consume |

### Notes

- Food consumption has daily limits (`energyFromFoodRemaining`)
- Energy bars bypass the food limit
- Special food (treats) restore 50 energy per use, `specialFoodAmount` tracked separately
- Energy pool limit is `energyPoolLimit` (e.g., 10220)
- `energyPerInterval` is the passive recovery rate

---

## Mercenary Campaign Subscribe/Unsubscribe

**Method:** POST
**URL:** `/en/main/mercenary-campaign/subscribe/`
**URL:** `/en/main/mercenary-campaign/unsubscribe/`
**Auth Required:** Yes

### Description

Subscribe to or unsubscribe from a mercenary campaign (combat order).

---

## Battle Map Status

**Method:** POST
**URL:** `/en/military/battle-map-status/{battleId}`
**Auth Required:** Yes

### Description

Returns the campaign map status showing which divisions each side has won. Called by `CampaignMapController`.
