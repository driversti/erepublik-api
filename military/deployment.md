# Deployment

#erepublik #api #military #deployment #fuel

[< Back to Military Module](README.md)

Endpoints for battle deployment inventory, starting/cancelling deployments, fuel management, and deployment reporting.

**Related:** [battlefield.md](battlefield.md) | [battlefield-ajax.md](battlefield-ajax.md) | [armory.md](armory.md)

---

## Get Battle Deployment Inventory

**Method:** POST
**URL:** `/en/military/fightDeploy-getInventory`
**Auth Required:** Yes

### Description

Retrieves the authenticated citizen's complete combat inventory for deploying to a specific battle, including available weapons (with damage per hit calculations), energy sources (food, energy bars, and pool energy), vehicles with country-specific enrollment status, and recoverable energy. This endpoint is called when preparing to fight in a battle zone to determine available combat resources and deployment options.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for security |
| battleId | number | Yes | The ID of the battle to deploy to |
| sideCountryId | number | Yes | The country ID for the side being joined |
| battleZoneId | number | Yes | The specific battle zone ID to deploy to |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-getInventory' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&battleId=863500&sideCountryId=72&battleZoneId=37861235'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "weapons": [
    {
      "industryId": 23,
      "quality": -1,
      "name": "No weapon",
      "amount": null,
      "preselect": true,
      "attributes": {
        "firepower": null,
        "maxEnergy": null
      },
      "damageperHit": 143,
      "isBest": false
    },
    {
      "industryId": 23,
      "quality": 5,
      "name": "Air-to-Air Missile Q5",
      "amount": 1169,
      "uses": 5844,
      "preselect": false,
      "attributes": {
        "firepower": "+100%",
        "maxEnergy": 58450
      },
      "damageperHit": 286,
      "isBest": true
    }
  ],
  "energySources": [
    {
      "type": "pool",
      "energy": 6654
    },
    {
      "type": "food",
      "tier": 1,
      "quality": 1,
      "amount": 284072,
      "energy": 568144
    },
    {
      "type": "food",
      "tier": 2,
      "quality": 2,
      "amount": 5678,
      "energy": 22712
    },
    {
      "type": "food",
      "tier": 3,
      "quality": 3,
      "amount": 2332887,
      "energy": 13997322
    },
    {
      "type": "energy_bar",
      "tier": 11,
      "quality": 11,
      "amount": 50,
      "energy": 10000
    }
  ],
  "recoverableEnergy": 0,
  "minEnergy": 10,
  "fuelConsumed": 1,
  "vehicles": [
    {
      "id": 30,
      "type": "aircraft",
      "isActive": true,
      "name": "F8U-16 Gunslinger",
      "status": "enrolled",
      "intStatus": 1,
      "isDefault": false,
      "blueprints": null,
      "countryId": 72,
      "countryData": {
        "id": 72,
        "level": 13,
        "maxLevel": 50,
        "xp": 20309840,
        "requiredXP": 43076000,
        "damageBonus": 13,
        "title": "Protector of Lithuania"
      },
      "title": "Protector of Lithuania",
      "attributes": [
        {
          "type": "damageBonus",
          "value": 13
        }
      ],
      "isRecommended": true
    },
    {
      "id": 40,
      "type": "aircraft",
      "isActive": false,
      "name": "Ember Firefly SH-18",
      "status": "notEnrolled",
      "intStatus": 2,
      "isDefault": false,
      "blueprints": null,
      "countryId": null,
      "countryData": null,
      "title": null,
      "attributes": [],
      "isRecommended": false
    }
  ],
  "poolEnergy": 6654,
  "maxEnergy": 16654,
  "recoverableEnergyBuyFood": 0
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `weapons` | array | List of available weapons for this battle type (air or ground) |
| `weapons[].industryId` | number | Industry ID for the weapon type (e.g., 23 = aircraft weapons) |
| `weapons[].quality` | number | Weapon quality (-1 for no weapon, 1-7 for regular weapons, 51 for SLG Peacemaker) |
| `weapons[].name` | string | Display name of the weapon |
| `weapons[].amount` | number/null | Number of weapons in inventory (null for "No weapon") |
| `weapons[].uses` | number | Total uses available (amount x uses per weapon) |
| `weapons[].preselect` | boolean | Whether this weapon should be preselected in UI |
| `weapons[].attributes` | object | Weapon attributes (firepower, maxEnergy) |
| `weapons[].attributes.firepower` | string/null | Firepower bonus (e.g., "+100%"). Warning: May contain invisible Unicode word joiner characters (U+2060) around the text -- use `.trim()` or strip non-printable chars before comparing |
| `weapons[].attributes.maxEnergy` | number/null | Maximum energy that can be consumed per hit |
| `weapons[].damageperHit` | number | Calculated damage per hit with this weapon |
| `weapons[].isBest` | boolean | Whether this is the highest quality weapon available |
| `energySources` | array | List of available energy sources (food, energy bars, pool) |
| `energySources[].type` | string | Energy source type ("pool", "food", "energy_bar") |
| `energySources[].tier` | number | Tier/quality level (food: 1-7, energy bars: 11) |
| `energySources[].quality` | number | Quality level (matches tier) |
| `energySources[].amount` | number | Number of items in inventory (not present for pool) |
| `energySources[].energy` | number | Total energy available from this source |
| `recoverableEnergy` | number | Energy that can be recovered from previous battles |
| `minEnergy` | number | Minimum energy cost per hit (typically 10) |
| `fuelConsumed` | number | Fuel consumed per new battle zone deployment (typically 1) |
| `vehicles` | array | List of all available vehicles for this battle type |
| `vehicles[].id` | number | Unique vehicle ID |
| `vehicles[].type` | string | Vehicle type ("aircraft" or "tanks") |
| `vehicles[].isActive` | boolean | Whether this vehicle is currently active |
| `vehicles[].name` | string | Display name of the vehicle |
| `vehicles[].status` | string | Enrollment status ("enrolled", "notEnrolled", etc.) |
| `vehicles[].intStatus` | number | Integer status code (1=enrolled, 2=notEnrolled) |
| `vehicles[].isDefault` | boolean | Whether this is a default vehicle |
| `vehicles[].blueprints` | object/null | Blueprint progress (null if not applicable) |
| `vehicles[].countryId` | number/null | Country ID for enrolled vehicles |
| `vehicles[].countryData` | object/null | Country-specific protection data |
| `vehicles[].countryData.level` | number | Protection level for this country (1-50). At max level (50), `xp` and `requiredXP` are null |
| `vehicles[].countryData.damageBonus` | number/null | Damage bonus percentage (200 at max level 50; null for non-max-level vehicles where the bonus is derived from level) |
| `vehicles[].countryData.title` | string | Protection title (e.g., "Protector of Lithuania") |
| `vehicles[].title` | string/null | Current protection title |
| `vehicles[].attributes` | array | Vehicle attributes (typically damage bonus) |
| `vehicles[].isRecommended` | boolean | Whether this vehicle is recommended for this battle |
| `poolEnergy` | number | Current pool energy available |
| `maxEnergy` | number | Maximum energy capacity |
| `recoverableEnergyBuyFood` | number | Energy recoverable by purchasing food |

### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Battle type detection**: The endpoint automatically determines whether the battle zone is air or ground division and returns appropriate weapons and vehicles
  - Air divisions (division 11) return aircraft and air-to-air missiles
  - Ground divisions (1-4) return tanks and ground weapons
- **Weapon selection**:
  - `preselect: true` indicates the default weapon (usually "No weapon")
  - `isBest: true` marks the highest quality weapon available
  - Higher quality weapons provide better `damageperHit` values
- **Damage calculation**: The `damageperHit` value is pre-calculated based on:
  - Base damage
  - Weapon quality and firepower bonuses
  - Vehicle protection level and damage bonuses
  - Citizen level and attributes
- **Energy sources**:
  - `type: "pool"` represents the citizen's energy pool (regenerates over time)
  - `type: "food"` represents consumable food items of various qualities
  - `type: "energy_bar"` represents premium energy bars (tier/quality varies -- e.g., Q11 at 200 energy/bar, Q16 at 50 energy/bar)
- **Food tiers**:
  - Tier 1 (Q1): 2 energy per unit
  - Tier 2 (Q2): 4 energy per unit
  - Tier 3 (Q3): 6 energy per unit
  - Higher tiers provide proportionally more energy
- **Vehicle recommendations**:
  - `isRecommended: true` indicates the vehicle is enrolled for the battle's side country
  - Vehicles enrolled for the correct country provide damage bonuses
  - Vehicles enrolled for other countries appear but are not recommended
- **Weapon uses**: The `uses` field shows total available uses, calculated as `floor(maxEnergy / minEnergy)`. Uses per unit varies by type:
  - Air-to-Air Missiles: uses/unit = quality (e.g., Q5 → 5 uses/unit)
  - Ground Ammunition: Q1=1, Q2=2, Q3=3, Q4=4, Q5=5, Q6=6, Q7=10 uses/unit
  - Special weapons (SLG Peacemaker, AIM-7 Grid-Lock): 1 use/unit
- **Energy limits**: `maxEnergy` represents the citizen's maximum energy capacity including pool and storage
- **Industry IDs**:
  - 2 = Ground weapons (Ammunition Q1–Q7)
  - 23 = Aircraft weapons (Air-to-Air Missile Q1–Q7)
- **Quality scale**: Regular weapons range from Q1 (lowest) to Q7 (highest), with Q5+ providing significant bonuses
- **Special weapons** (quality=51): Always marked `isBest: true` when present, overriding regular `isBest` logic. 1 use per unit.
  - **SLG Peacemaker** (ground, industryId=2): Fixed **50,000,000** damage per hit (firepower: "50M")
  - **AIM-7 Grid-Lock** (air, industryId=23): Fixed **50,000** damage per hit (firepower: "50k")
- **No weapon option**: Always available with `quality: -1` and basic damage per hit
- **Country matching**: The `sideCountryId` parameter determines which vehicles are recommended based on their enrollment status
- **Max-level protectors**: At level 50, `countryData.xp` and `countryData.requiredXP` are `null` (no further progression), and `damageBonus` is 200 (the maximum)

---

## Start Battle Deployment

**Method:** POST
**URL:** `/en/military/fightDeploy-startDeploy`
**Auth Required:** Yes

### Description

Initiates a battle deployment (auto-fight) for the authenticated citizen in a specific battle zone. This endpoint queues the citizen for combat with their specified energy allocation, weapon quality, and vehicle selection. The system will automatically fight using the configured resources until the energy is depleted or the battle round ends. This is the companion endpoint to `/en/military/fightDeploy-getInventory` - first retrieve available resources with getInventory, then start the deployment with this endpoint.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for security |
| battleId | number | Yes | The ID of the battle to deploy to |
| battleZoneId | number | Yes | The specific battle zone ID to deploy to |
| sideCountryId | number | Yes | The country ID for the side being joined |
| weaponQuality | number | Yes | Weapon quality to use (-1 = no weapon, 1-7 for weapons) |
| totalEnergy | number | Yes | Total energy to allocate for this deployment |
| energySources[n][quality] | number | Yes | Quality/tier of the energy source (1-3 for food, 11 for energy bars) |
| energySources[n][amount] | number | Yes | Amount of energy to consume from this source |
| skinId | number | Yes | Vehicle/skin ID to use for combat |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-startDeploy' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&battleId=863657&battleZoneId=37861650&sideCountryId=29&weaponQuality=-1&totalEnergy=340&energySources%5B0%5D%5Bquality%5D=3&energySources%5B0%5D%5Bamount%5D=0&skinId=18'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "message": "Successfully deployed",
  "deploymentId": 122127169,
  "data": {
    "energyItems": {
      "food": 0,
      "energyBars": []
    },
    "energyTotal": 340,
    "weaponType": "aircraft",
    "chosenWeaponQuality": -1,
    "weaponQuality": -1,
    "battleId": 863657,
    "sideCountryId": 29,
    "battleZoneId": 37861650,
    "skinId": 18,
    "fuelLeft": 36
  }
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | Whether an error occurred (false = success) |
| `message` | string | Human-readable status message |
| `deploymentId` | number | Unique ID for this deployment session |
| `data` | object | Deployment details |
| `data.energyItems` | object | Energy sources consumed |
| `data.energyItems.food` | number | Amount of food energy consumed |
| `data.energyItems.energyBars` | array | Energy bars consumed |
| `data.energyTotal` | number | Total energy allocated for this deployment |
| `data.weaponType` | string | Type of combat ("aircraft" for air, "tanks" for ground) |
| `data.chosenWeaponQuality` | number | The weapon quality selected by the user |
| `data.weaponQuality` | number | The actual weapon quality being used |
| `data.battleId` | number | The battle ID for this deployment |
| `data.sideCountryId` | number | The country ID the citizen is fighting for |
| `data.battleZoneId` | number | The battle zone ID for this deployment |
| `data.skinId` | number | The vehicle/skin ID being used |
| `data.fuelLeft` | number | Remaining fuel for the vehicle after deployment starts |

### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Workflow**: This endpoint is typically called after `/en/military/fightDeploy-getInventory`:
  1. Call `getInventory` to retrieve available weapons, energy sources, and vehicles
  2. Select desired combat configuration
  3. Call `startDeploy` to begin the auto-fight deployment
- **Energy sources array**: The `energySources` parameter is an array of objects, each specifying a quality tier and amount:
  - Format: `energySources[0][quality]=3&energySources[0][amount]=100`
  - Multiple sources can be specified: `energySources[1][quality]=11&energySources[1][amount]=50`
- **Weapon quality values**:
  - `-1` = No weapon (base damage only)
  - `1-7` = Weapon quality levels (Q1-Q7)
- **Vehicle selection**: The `skinId` must be a vehicle the citizen owns and should be appropriate for the battle type:
  - Aircraft skins for air divisions (division 11)
  - Tank skins for ground divisions (divisions 1-4)
- **Fuel consumption**: The `fuelLeft` value shows remaining fuel after deployment starts; 1 fuel is consumed per new battle zone (see [Get Fuel Status](#get-fuel-status-campaigns-page) for current fuel balance)
- **Auto-fight behavior**: Once deployed, the system automatically executes combat until:
  - All allocated energy is consumed
  - The battle round ends
  - The citizen cancels the deployment
- **Energy allocation**: The `totalEnergy` should match the sum of energy from all sources in `energySources`
- **Deployment ID**: Save the `deploymentId` to check deployment status or cancel the deployment later
- **Country matching**: For optimal damage bonuses, use a vehicle enrolled for the `sideCountryId` country

---

## Get Fuel Status (Campaigns Page)

**Method:** GET
**URL:** `/en/military/campaigns`
**Auth Required:** Yes

### Description

Returns the campaigns HTML page which includes the player's current fuel status in the sidebar. Fuel is a **weekly deployment limit** -- fighting in a new battle zone consumes 1 fuel. Fuel resets weekly and unused fuel does **not** carry over to the next week. Additional fuel can be purchased from the Gold Shop (`/en/main/gold-items/`).

This is **not a JSON API** -- the fuel data is embedded in HTML elements within the page sidebar.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN; cf_clearance=...` | Yes |
| User-Agent | Browser user agent | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/military/campaigns' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN; erpk_auth=1; cf_clearance=YOUR_CF_TOKEN' \
  -H 'User-Agent: Mozilla/5.0 ...'
```

</details>

<details>
<summary>Relevant HTML Structure</summary>

```html
<div class="energyBg margin-25 fuel">
  <div id="citizenFuel" class="energyBar">
    <img src="//www.erepublik.net/images/modules/sidebar/fuel_icon.png" class="icon fuelIcon" alt=""/>
    <span class="currentEnergy">
      <q id="fuelLeft">68</q>&nbsp;/&nbsp;<q id="maxFuel">70</q>
    </span>
    <div class="currentEnergyBar fuel" style="width: 97%"><q></q></div>
    <a class="recoverEnergy" href="/en/main/gold-items/">
      <div class="recoverEnergyBtn"></div>
    </a>
  </div>
  <div id="fuelTooltip" class="fuelTooltip tooltip">
    <!-- Tooltip contains: -->
    <p class="title">Fuel</p>
    <p>You recover up to <big id="fuelPerInterval">70</big> Fuel every week</p>
    <p class="bullets">Fighting in a new Battle Zone consumes 1 Fuel</p>
    <p class="bullets">You can purchase more Fuel in the shop</p>
    <p class="bullets">Your Fuel tank resets weekly</p>
    <p class="bullets">Leftover Fuel does not carry over to the next week</p>
  </div>
</div>
```

</details>

### Data Extraction

| HTML Element | ID | Type | Description |
|---|---|---|---|
| `<q>` | `fuelLeft` | number | Current fuel remaining this week |
| `<q>` | `maxFuel` | number | Maximum fuel capacity (base + purchased) |
| `<big>` | `fuelPerInterval` | number | Fuel recovered per weekly reset (equals base max fuel) |

<details>
<summary>Extraction Example (JavaScript)</summary>

```javascript
function extractTagValue(html, id) {
  const regex = new RegExp(`id="${id}"[^>]*>\\s*(\\d+)\\s*<`);
  const match = html.match(regex);
  return match ? parseInt(match[1], 10) : null;
}

const fuelLeft = extractTagValue(html, "fuelLeft");       // e.g. 68
const maxFuel = extractTagValue(html, "maxFuel");          // e.g. 70
const fuelPerInterval = extractTagValue(html, "fuelPerInterval"); // e.g. 70
```

</details>

### Fuel Mechanics

| Property | Details |
|----------|---------|
| **Consumption** | 1 fuel per new battle zone (not per hit) |
| **Reset** | Weekly (does not carry over) |
| **Recovery** | `fuelPerInterval` fuel restored on weekly reset |
| **Purchase** | Gold Shop (`/en/main/gold-items/`) -- price starts at 1 Gold, increases with each purchase |
| **Base capacity** | Depends on citizen level/upgrades; `maxFuel` may exceed `fuelPerInterval` if extra fuel was purchased |
| **Relation to `fuelConsumed`** | The `fuelConsumed` field in deploy inventory (`fightDeploy-getInventory`) shows the cost per deployment (typically 1) |
| **Relation to `fuelLeft`** | The `fuelLeft` field in deploy response (`fightDeploy-startDeploy`) shows remaining fuel after that deployment |

### Notes

- The fuel sidebar is only present on the campaigns page (`/en/military/campaigns`), not on the homepage or other pages
- The `style="width: 97%"` on `div.currentEnergyBar.fuel` reflects the visual progress bar percentage
- The tooltip is shown on hover via `hoverTooltip('#citizenFuel', '#fuelTooltip')` (JavaScript at bottom of page)
- `maxFuel` can temporarily exceed `fuelPerInterval` when the player has purchased additional fuel from the Gold Shop

---

## Start Deployment (AJAX)

**Method:** POST
**URL:** `/en/military/fightDeploy-startDeploy`
**Auth Required:** Yes

### Description

Starts an automated deployment that gradually consumes resources (food, energy bars, fuel) to deal damage over time. While deployed, the player **cannot** travel, eat, fight manually, or use bombs -- but **can** activate boosters. Called by `DeploymentController.startDeploy()`.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `battleZoneId` | number | Yes | The specific battle zone ID |
| `sideCountryId` | number | Yes | Country ID to fight for |
| `energyUsed` | number | Yes | Total energy to deploy |
| `weaponQuality` | number | Yes | Weapon quality to use (0 = bare hands) |
| `vehicleId` | number | No | Vehicle/protector to use |
| `_token` | string | Yes | CSRF token |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-startDeploy' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&battleZoneId=38158390&sideCountryId=72&energyUsed=5000&weaponQuality=7&_token=YOUR_CSRF_TOKEN'
```

</details>

### Notes

- Only one deployment can be active at a time (check `SERVER_DATA.deployment`)
- Level-up energy is automatically added and consumed during deployment
- Deploy estimates (damage, kills, time) are calculated client-side
- Boosters like `deploy_size` (+100%, +200%, +500%) increase the effective deployment size

---

## Cancel Deployment

**Method:** POST
**URL:** `/en/military/fightDeploy-cancelDeploy`
**Auth Required:** Yes

### Description

Cancels an in-progress deployment. Called when the player clicks the cancel button on the deploy panel.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `battleId` | number | Yes | The battle/campaign ID |
| `_token` | string | Yes | CSRF token |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/fightDeploy-cancelDeploy' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -d 'battleId=869119&_token=YOUR_CSRF_TOKEN'
```

</details>

---

## Deployment Report Popup

**Method:** GET (popup)
**URL:** `/en/military/fightDeploy-deployReportPopup`
**Auth Required:** Yes

### Description

Returns an HTML popup fragment with the completed deployment's results (damage dealt, kills, resources consumed). Shown after deployment finishes.
