# Companies

#erepublik #api #economy #companies

[< Back to Economy Overview](README.md)

Company management page -- viewing and managing factories, holdings, production stats, and upgrade trees.

**Related:** [Work](work.md) | [Job Market](job-market.md) | [Inventory](inventory.md)

---

## My Companies Page

**Method:** GET
**URL:** `/en/economy/myCompanies`
**Auth Required:** Yes

### Description

The company management page where citizens view and manage all their factories and holding companies. Returns an HTML page containing embedded JavaScript variables with full company data, production stats, upgrade trees, raw material stocks, employee/work info, and action URLs. This is a **page endpoint** (returns HTML, not JSON) -- structured data is embedded in `<script>` tags.

### Parameters

No URL parameters.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Accept | `text/html,application/xhtml+xml` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/myCompanies' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

</details>

### Embedded JavaScript Variables

#### `companies` -- All Owned Companies

A map keyed by company ID containing every company the citizen owns. Each company includes production configuration, upgrade tree, work-as-manager (WAM) status, and holding assignment.

<details>
<summary>companies variable example</summary>

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

</details>

**Company fields:**

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Unique company ID |
| `holding_company_id` | string\|false | ID of the holding this company is assigned to, or `false` if unassigned |
| `industry_id` | number | Industry type ID (1=Food, 2=Weapons, 4=House, 7=FRM, 12=WRM, 17=HRM, 23=Aircraft, 24=ARM) |
| `industry_name` | string | Human-readable industry name (e.g., "Food", "Weapons", "Rubber", "Deer") |
| `industry_token` | string | Token identifier (e.g., `FOOD`, `WEAPON`, `HOUSE`, `AIRCRAFT`) |
| `quality` | number | Product quality level (1-7) |
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
| `level` | number | Upgrade level (1-7) |
| `employees` | number | Employee slots at this level |
| `cost` | number | Gold cost to upgrade to this level (0 = current level) |
| `raw_usage` | number | Raw materials consumed per product at this level |
| `type` | number | `-1` = below current, `0` = current level, `1` = above current (available upgrade) |

#### `holdingCompanies` -- Holding Companies

A map keyed by holding ID containing all the citizen's holding companies.

<details>
<summary>holdingCompanies variable example</summary>

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

</details>

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

#### `pageDetails` -- Page-Level Economy Data

Aggregated economy information including raw material stocks, employee data, energy, overtime stats, and product attribute definitions.

<details>
<summary>pageDetails variable example</summary>

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
  "food": { /* product definition -- see below */ },
  "weapon": { /* product definition */ },
  "house": { /* product definition */ },
  "airplane": { /* product definition */ }
}
```

</details>

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
| `employee` | object | Employment data (salary, employer, taxes -- same as `/en/main/job-data`) |
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
| `attributes` | object | Quality -> effect mapping (energy restored, fire power, durability, etc.) |

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

#### `totals` -- Production Totals

Aggregated production and raw material consumption across all companies.

<details>
<summary>totals variable example</summary>

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

</details>

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

#### `energyData` -- Current Energy Status

<details>
<summary>energyData variable example</summary>

```javascript
var energyData = {
  "energy": 128,
  "energyPoolLimit": 10220,
  "recoverableEnergy": 0,
  "energyPerInterval": 64,
  "energyStatus": "recovering"
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `energy` | number | Current energy points |
| `energyPoolLimit` | number | Maximum energy pool capacity |
| `recoverableEnergy` | number | Energy that can be recovered |
| `energyPerInterval` | number | Energy recovered per 6-minute interval |
| `energyStatus` | string | Recovery status: `"recovering"`, `"full"`, or `"limitReached"` (when current energy exceeds pool limit, e.g. after eating food) |

#### Scalar Configuration Variables

<details>
<summary>Scalar variables</summary>

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

</details>

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
- The `companies` map can be very large -- a citizen with 315 companies generates ~1.8 MB of HTML
- Companies not assigned to a holding have `holding_company_id: false` and `cannot_work_as_manager_reason: "no_holding"`
- The `upgrades` object uses `type` to indicate relative position: `-1` = already surpassed, `0` = current level, `1` = available upgrade
- The `upgarade_url` variable has a typo in the original eRepublik source code (`upgarade` instead of `upgrade`)
- Industry IDs for raw materials: 7=FRM, 12=WRM, 17=HRM, 24=ARM; for raw resource companies: Deer, Cattle, Fish (food raws), Rubber, Aluminum, Granite, Neodymium (weapon/house/aircraft raws)
- The `promoMultiplier` and `promoMultiplierHouse` values are non-zero during special production bonus events
- Action URLs (`work_url`, `resign_url`, etc.) require a POST with `csrfToken` as `_token` parameter
