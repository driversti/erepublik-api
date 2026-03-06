# Armory

#erepublik #api #military #armory #vehicles

[< Back to Military Module](README.md)

Endpoints for managing military vehicles, enrollment, and the Protector system.

**Related:** [deployment.md](deployment.md) | [battlefield.md](battlefield.md)

---

## Get Armory Overview

**Method:** POST
**URL:** `/en/military/armory-data/overview`
**Auth Required:** Yes

### Description

Retrieves the authenticated citizen's complete armory overview, including all owned and available military vehicles (tanks and aircraft) with their enrollment status, country-specific protection levels, damage bonuses, and blueprint progress. This endpoint provides a comprehensive view of the citizen's military vehicle collection and progression system.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for security |

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
curl -X POST 'https://www.erepublik.com/en/military/armory-data/overview' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "panelUrl": "/en/military/armory-data/overview",
  "vehicles": [
    {
      "id": 9,
      "type": "tanks",
      "isActive": false,
      "name": "S-07 Sentinel",
      "desc": "<p>The <b>S-07 Sentinel</b> was obtained from the eRepublik <b>12 Years Anniversary Challenge</b>.</p>",
      "status": "enrolled",
      "intStatus": 1,
      "isDefault": false,
      "blueprints": null,
      "countryId": 40,
      "countryData": {
        "id": 40,
        "level": 50,
        "maxLevel": 50,
        "xp": null,
        "requiredXP": null,
        "damageBonus": 200,
        "title": "Protector of Ukraine"
      },
      "title": "Protector of Ukraine",
      "attributes": [
        {
          "type": "damageBonus",
          "value": 200
        }
      ],
      "displayPriority": 1
    },
    {
      "id": 17,
      "type": "tanks",
      "isActive": false,
      "name": "D4 Overlord",
      "desc": "<p>The D4 Overlord brings its own genre of drama to the battlefield.</p>",
      "status": "enrolled",
      "intStatus": 1,
      "isDefault": true,
      "blueprints": null,
      "countryId": 65,
      "countryData": {
        "id": 65,
        "level": 27,
        "maxLevel": 50,
        "xp": 1682228238,
        "requiredXP": 2000000000,
        "damageBonus": 41,
        "title": "Protector of Serbia"
      },
      "title": "Protector of Serbia",
      "attributes": [
        {
          "type": "damageBonus",
          "value": 41
        }
      ],
      "displayPriority": 5
    },
    {
      "id": 30,
      "type": "aircraft",
      "isActive": true,
      "name": "F8U-16 Gunslinger",
      "desc": "<p>The <b>F8U-16 Gunslinger</b> was obtained from the <b>Amazing Journey</b> 16th anniversary event.</p>",
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
      "displayPriority": 12
    },
    {
      "id": 1,
      "type": "tanks",
      "isActive": false,
      "name": "Red Baron",
      "desc": "<p>The <i>Red Baron</i> tank skin was obtained during <b>Winner Takes it All Event</b>.</p>",
      "status": "notEnrolled",
      "intStatus": 2,
      "isDefault": false,
      "blueprints": null,
      "countryId": null,
      "countryData": null,
      "title": null,
      "attributes": [],
      "displayPriority": 18
    },
    {
      "id": 2,
      "type": "tanks",
      "isActive": false,
      "name": "Monster Grinder",
      "desc": "<p>The <i>Monster Grinder</i> tank skin was obtained during <b>Tales of the New World: Headless Horror Event</b>.</p>",
      "status": "blueprints",
      "intStatus": 4,
      "isDefault": false,
      "blueprints": {
        "current": 0,
        "required": 10
      },
      "countryId": null,
      "countryData": null,
      "title": null,
      "attributes": [],
      "displayPriority": 34
    }
  ],
  "statuses": {
    "unavailable": "Unavailable",
    "blueprints": "Locked",
    "notAssembled": "Assemble",
    "notEnrolled": "Enroll",
    "enrolled": "Enrolled"
  },
  "settings": {
    "maxEnrollPerCountry": 3,
    "universalPoints": {
      "tanks": 4081310987,
      "aircraft": 0
    }
  }
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `panelUrl` | string | URL path to the armory overview panel |
| `vehicles` | array | List of all available vehicles (tanks and aircraft) |
| `vehicles[].id` | number | Unique vehicle ID |
| `vehicles[].type` | string | Vehicle type ("tanks" or "aircraft") |
| `vehicles[].isActive` | boolean | Whether this vehicle is currently active in combat |
| `vehicles[].name` | string | Display name of the vehicle |
| `vehicles[].desc` | string | HTML description of the vehicle and how it was obtained |
| `vehicles[].status` | string | Vehicle status (see `statuses` mapping) |
| `vehicles[].intStatus` | number | Integer status code (1=enrolled, 2=notEnrolled, 4=blueprints) |
| `vehicles[].isDefault` | boolean | Whether this is a default vehicle available to all players |
| `vehicles[].blueprints` | object/null | Blueprint progress if status is "blueprints", null otherwise |
| `vehicles[].blueprints.current` | number | Number of blueprints currently owned |
| `vehicles[].blueprints.required` | number | Total blueprints required to unlock |
| `vehicles[].countryId` | number/null | Country ID for which this vehicle is enrolled (null if not enrolled) |
| `vehicles[].countryData` | object/null | Country-specific protection level data (null if not enrolled) |
| `vehicles[].countryData.id` | number | Country ID |
| `vehicles[].countryData.level` | number | Current protection level for this country |
| `vehicles[].countryData.maxLevel` | number | Maximum protection level (typically 50) |
| `vehicles[].countryData.xp` | number/null | Current XP points (null if max level reached) |
| `vehicles[].countryData.requiredXP` | number/null | XP required for next level (null if max level) |
| `vehicles[].countryData.damageBonus` | number | Damage bonus percentage from this protection level |
| `vehicles[].countryData.title` | string | Protection title (e.g., "Protector of Ukraine") |
| `vehicles[].title` | string/null | Current protection title for enrolled vehicles |
| `vehicles[].attributes` | array | List of vehicle attributes (typically damage bonus) |
| `vehicles[].attributes[].type` | string | Attribute type (e.g., "damageBonus") |
| `vehicles[].attributes[].value` | number | Attribute value |
| `vehicles[].displayPriority` | number | Display order priority (lower = higher priority) |
| `statuses` | object | Map of status keys to human-readable labels |
| `statuses.unavailable` | string | Label for unavailable vehicles |
| `statuses.blueprints` | string | Label for locked vehicles requiring blueprints |
| `statuses.notAssembled` | string | Label for vehicles that need assembly |
| `statuses.notEnrolled` | string | Label for owned but not enrolled vehicles |
| `statuses.enrolled` | string | Label for enrolled vehicles |
| `settings` | object | Armory configuration settings |
| `settings.maxEnrollPerCountry` | number | Maximum number of vehicles that can be enrolled per country |
| `settings.universalPoints` | object | Universal points available for vehicle upgrades |
| `settings.universalPoints.tanks` | number | Universal points for tank upgrades |
| `settings.universalPoints.aircraft` | number | Universal points for aircraft upgrades |

### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Vehicle statuses**:
  - `enrolled` (intStatus=1): Vehicle is enrolled for a specific country and can be used in combat
  - `notEnrolled` (intStatus=2): Vehicle is owned but not enrolled for any country
  - `blueprints` (intStatus=4): Vehicle is locked and requires collecting blueprints to unlock
- **Country enrollment**: Each vehicle can be enrolled for one country at a time, earning "Protector of {Country}" titles
- **Protection levels**: Each country enrollment has its own XP progression system (levels 1-50)
- **Damage bonus**: Higher protection levels provide increased damage bonus percentages
- **Default vehicles**: Some vehicles (`isDefault: true`) are available to all players at certain division thresholds
- **Universal points**: Accumulated points that can be used to upgrade vehicle protection levels across countries
- **Active vehicle**: Only one tank and one aircraft can be `isActive: true` at a time for ground and air combat respectively
- **Display priority**: Vehicles are sorted by `displayPriority`, with enrolled vehicles typically appearing first
- **HTML descriptions**: The `desc` field contains HTML-formatted text with vehicle lore and obtaining requirements
- **Anniversary vehicles**: Many special vehicles were obtained from anniversary events and challenges
- **Blueprint system**: Locked vehicles show current/required blueprint counts (typically 0-10 blueprints needed)

---

## Enroll Vehicle (Armory Actions)

**Method:** POST
**URL:** `/en/military/armory-actions`
**Auth Required:** Yes

### Description

Enrolls a military vehicle (tank or aircraft) for a specific country's Protector program. The vehicle must be owned and have status `notEnrolled` (intStatus=2). Once enrolled, the vehicle earns "Protector of {Country}" XP when used in battles for that country, providing increasing damage bonuses as the protection level rises (up to level 50).

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| action | string | Yes | Must be `enroll` |
| skinId | number | Yes | Vehicle ID (from armory overview `vehicles[].id`) |
| countryId | number | Yes | Country ID to enroll for (use `get_countries` for the full list) |
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
curl -X POST 'https://www.erepublik.com/en/military/armory-actions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'action=enroll&skinId=14&countryId=35&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{"status": true, "message": "Vehicle successfully enrolled"}
```

</details>

<details>
<summary>Example Response (Error -- Invalid Parameters)</summary>

```json
{"status": false, "message": "Invalid parameters"}
```

</details>

### Notes

- **Modifying action** -- this endpoint changes game state (enrolls vehicle for a country)
- The `skinId` parameter corresponds to `vehicles[].id` from the `/en/military/armory-data/overview` endpoint
- Only vehicles with status `notEnrolled` (intStatus=2) can be enrolled
- Vehicles with status `blueprints` (intStatus=4) or `unavailable` (intStatus=5) cannot be enrolled
- Each vehicle can only be enrolled for ONE country at a time
- Maximum 3 vehicles can be enrolled per country (`maxEnrollPerCountry` from armory settings)
- The `action` parameter uses `enroll` (not `enrollVehicle`)
- Returns `"Invalid parameters"` if the vehicle ID or country ID is invalid, or if the vehicle cannot be enrolled

---

## Get Armory Protectors

**Method:** POST
**URL:** `/en/military/armory-data/protectors`
**Auth Required:** Yes

### Description

Retrieves the authenticated citizen's complete Protector status across all countries, showing country-specific protection levels, XP progression, damage bonuses, and enrolled vehicles for both tanks and aircraft. This endpoint provides a comprehensive overview of the citizen's country-by-country vehicle protection system, including earned titles and universal points available for upgrades.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for security |

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
curl -X POST 'https://www.erepublik.com/en/military/armory-data/protectors' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "panelUrl": "/en/military/armory-data/protectors",
  "countries": [
    {
      "id": 72,
      "tanks": {
        "level": 50,
        "maxLevel": 50,
        "xp": null,
        "requiredXP": null,
        "levelProgress": "100%",
        "damageBonus": 200,
        "title": "Protector of Lithuania",
        "vehicles": [20]
      },
      "aircraft": {
        "level": 13,
        "maxLevel": 50,
        "xp": 20309840,
        "requiredXP": 22766160,
        "levelProgress": "47.1489%",
        "damageBonus": 13,
        "title": "Protector of Lithuania",
        "vehicles": [30]
      }
    },
    {
      "id": 40,
      "tanks": {
        "level": 50,
        "maxLevel": 50,
        "xp": null,
        "requiredXP": null,
        "levelProgress": "100%",
        "damageBonus": 200,
        "title": "Protector of Ukraine",
        "vehicles": [9]
      },
      "aircraft": {
        "level": 11,
        "maxLevel": 50,
        "xp": 9785703,
        "requiredXP": 1258297,
        "levelProgress": "88.6065%",
        "damageBonus": 11,
        "title": "Protector of Ukraine",
        "vehicles": [10]
      }
    },
    {
      "id": 1,
      "tanks": {
        "level": 28,
        "maxLevel": 50,
        "xp": 2527825031,
        "requiredXP": 1472174969,
        "levelProgress": "63.1956%",
        "damageBonus": 44,
        "title": "Protector of Romania",
        "vehicles": [6]
      },
      "aircraft": {
        "level": 6,
        "maxLevel": 50,
        "xp": 397571,
        "requiredXP": 31429,
        "levelProgress": "92.6739%",
        "damageBonus": 6,
        "title": "Protector of Romania",
        "vehicles": [26]
      }
    },
    {
      "id": 57,
      "tanks": {
        "level": 0,
        "maxLevel": 50,
        "xp": 822183,
        "requiredXP": 127817,
        "levelProgress": "86.5456%",
        "damageBonus": 0,
        "title": "Protector of Pakistan",
        "vehicles": []
      },
      "aircraft": {
        "level": 0,
        "maxLevel": 50,
        "xp": 1350,
        "requiredXP": 7750,
        "levelProgress": "14.8352%",
        "damageBonus": 0,
        "title": "Protector of Pakistan",
        "vehicles": []
      }
    }
  ],
  "settings": {
    "maxEnrollPerCountry": 3,
    "universalPoints": {
      "tanks": 4081310987,
      "aircraft": 0
    }
  }
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `panelUrl` | string | URL path to the armory protectors panel |
| `countries` | array | List of all countries with protection progress |
| `countries[].id` | number | Country ID |
| `countries[].tanks` | object | Tank protection data for this country |
| `countries[].tanks.level` | number | Current protection level (0-50) |
| `countries[].tanks.maxLevel` | number | Maximum protection level (typically 50) |
| `countries[].tanks.xp` | number/null | Current XP points (null if max level reached) |
| `countries[].tanks.requiredXP` | number/null | XP needed for next level (null if max level) |
| `countries[].tanks.levelProgress` | string | Progress percentage toward next level (e.g., "63.1956%") |
| `countries[].tanks.damageBonus` | number | Damage bonus percentage at current level |
| `countries[].tanks.title` | string | Protection title (e.g., "Protector of Ukraine") |
| `countries[].tanks.vehicles` | array | Array of enrolled tank vehicle IDs for this country |
| `countries[].aircraft` | object | Aircraft protection data for this country |
| `countries[].aircraft.level` | number | Current protection level (0-50) |
| `countries[].aircraft.maxLevel` | number | Maximum protection level (typically 50) |
| `countries[].aircraft.xp` | number/null | Current XP points (null if max level reached) |
| `countries[].aircraft.requiredXP` | number/null | XP needed for next level (null if max level) |
| `countries[].aircraft.levelProgress` | string | Progress percentage toward next level |
| `countries[].aircraft.damageBonus` | number | Damage bonus percentage at current level |
| `countries[].aircraft.title` | string | Protection title |
| `countries[].aircraft.vehicles` | array | Array of enrolled aircraft vehicle IDs for this country |
| `settings` | object | Armory configuration settings |
| `settings.maxEnrollPerCountry` | number | Maximum vehicles that can be enrolled per country (typically 3) |
| `settings.universalPoints` | object | Universal points available for protection upgrades |
| `settings.universalPoints.tanks` | number | Universal points for tank protection upgrades |
| `settings.universalPoints.aircraft` | number | Universal points for aircraft protection upgrades |

### Notes

- **CSRF protection**: The `_token` parameter is required and must be a valid CSRF token from the current session
- **Country ordering**: Countries are typically sorted by total protection level/progress (highest first)
- **Protection system**:
  - Each country has independent protection levels for tanks and aircraft (0-50)
  - Fighting with enrolled vehicles earns XP toward the next protection level
  - Higher levels provide increased damage bonuses
  - At level 0, citizens can still earn XP but receive no damage bonus
- **Max level behavior**: When `level: 50` is reached, both `xp` and `requiredXP` become `null`, and `levelProgress` shows "100%"
- **Damage bonuses**:
  - Bonuses scale with protection levels (typically 2% per level at higher tiers)
  - Level 50 provides 200% damage bonus (maximum)
  - Level 0 provides 0% damage bonus
- **Vehicle enrollment**: The `vehicles` arrays contain vehicle IDs that are currently enrolled for that country
  - Empty arrays indicate no vehicles are enrolled, but XP can still be earned with default vehicles
  - Vehicle IDs correspond to the `id` field from the `/armory-data/overview` endpoint
- **Universal points**:
  - Can be spent to increase protection levels without fighting
  - Separate pools for tanks and aircraft
  - Accumulated through gameplay and events
- **Title format**: All titles follow the pattern "Protector of {Country Name}"
- **XP progression**:
  - XP requirements increase significantly at higher levels
  - Fighting for a country with an enrolled vehicle earns XP for that country's protection level
  - Different battles may provide different XP amounts
- **Level progress**: The `levelProgress` percentage shows how far through the current level the citizen is (current XP / required XP for next level)
