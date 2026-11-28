# Companies and holdings

[API docs](../README.md) › Economy › Companies

**My Places**: your companies and holding companies, building and buying new ones, upgrades, and moving companies between holdings. Producing is on [Work](work.md#work-as-manager).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [My companies page](#my-companies-page) | GET | `/en/economy/myCompanies` | ✅ Verified 2026-11-27 |
| [Build page](#build-page) | GET | `/en/economy/create-company` | ✅ Verified 2026-11-27 |
| [Build](#build) | POST | `/en/economy/build-company` | 🔍 From code |
| [Company market](#company-market) | GET | `/en/economy/buy-company/{countryId}` | ✅ Verified 2026-11-27 |
| [Buy a company](#buy-a-company) | POST | `/en/economy/buy-company/…` | 🔍 From code |
| [Upgrade a company](#upgrade-a-company) | POST | `/en/economy/upgrade-company` | 🔍 From code |
| [Sell or dissolve](#sell-or-dissolve) | GET | `/en/economy/sell-company/{companyId}` | 🔍 From code |
| [Advanced buildings page](#advanced-buildings-page) | GET | `/en/economy/advanced-buildings` | ✅ Verified 2026-11-27 |
| [Upgrade an advanced building](#upgrade-an-advanced-building) | POST | `/en/economy/upgrade-factory` | 🔍 From code |
| [Create holding popup](#create-holding-popup) | GET | `/en/economy/create-holding-popup` | ✅ Verified 2026-11-27 |
| [Create holding](#create-holding) | POST | `/en/economy/create-holding` | 🔍 From code |
| [Edit holding popup](#edit-holding-popup) | POST | `/en/economy/edit-holding-popup` | ✅ Verified 2026-11-27 |
| [Rename or delete holding](#rename-or-delete-holding) | POST | `/en/economy/edit-holding` | 🔍 From code |
| [Move one company popup](#move-one-company-popup) | POST | `/en/economy/assign-to-holding-popup` | ✅ Verified 2026-11-27 |
| [Move one company](#move-one-company) | POST | `/en/economy/assign-to-holding` | 🔍 From code |
| [Move several popup](#move-several-popup) | POST | `/en/economy/assign-multiple-to-holding-popup` | ✅ Verified 2026-11-27 |
| [Move several](#move-several) | POST | `/en/economy/assign-multiple-to-holding` | 🔍 From code |
| [Unassign popup](#unassign-popup) | POST | `/en/economy/unassign-factories-popup` | ✅ Verified 2026-11-27 |
| [Unassign](#unassign) | POST | `/en/economy/unassign-factories` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## My companies page

`GET /en/economy/myCompanies` · Auth required · ✅ Verified 2026-11-27

HTML page; the data is in JavaScript variables inside `<script>` tags. With hundreds of companies the page is large (earlier notes: about 1.8 MB for 315 companies).

```bash
curl 'https://www.erepublik.com/en/economy/myCompanies' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Variable | Contents |
|----------|----------|
| [`companies`](#companies) | Companies, keyed by ID |
| [`holdingCompanies`](#holdingcompanies) | Holdings, keyed by ID |
| [`pageDetails`](#pagedetails) | Stocks, job, energy, overtime, industry definitions |
| [`totals`](#totals) | Production totals of the preset work |
| [`energyData`](#energydata) | Energy state |
| [other](#scalar-variables) | Flags and action URLs |

### `companies`

<details>
<summary>One entry (sample values)</summary>

```json
{
  "111111": {
    "id": 111111, "holding_company_id": "222222",
    "industry_id": 2, "industry_name": "Weapons", "industry_token": "WEAPON",
    "products_img": "https://www.erepublik.net/images/icons/industry/2/q7_55x55.png",
    "quality": 7, "building_name": "Weapons Factory",
    "building_img": "//www.erepublik.net/images/modules/myland/buildings/weapons_q7.png",
    "is_raw": false, "raw_img": "https://www.erepublik.net/images/icons/industry/12/default.png",
    "wam_enabled": true, "can_work_as_manager": true, "cannot_work_as_manager_reason": "",
    "can_assign_employees": true, "is_assigned_to_holding": true,
    "preset_works": 0, "preset_own_work": 1,
    "base_production": 10, "resource_bonus": 84, "effective_bonus": 184,
    "raw_usage": 36.8, "production": 18.4, "employee_limit": "10",
    "upgrade_url": "//economy.erepublik.com/en/company/customize/111111",
    "already_worked": false, "todays_works": 0, "total_works": 10,
    "sell_url": "//www.erepublik.com/en/economy/sell-company/111111",
    "upgrades": {
      "1": { "level": 1, "employees": 1, "cost": 610, "img": "…", "raw_usage": 0.1, "type": -1 },
      "2": { "level": 2, "employees": 2, "cost": 600, "img": "…", "raw_usage": 0.2, "type": -1 },
      "3": { "level": 3, "employees": 3, "cost": 575, "img": "…", "raw_usage": 0.3, "type": -1 },
      "4": { "level": 4, "employees": 5, "cost": 525, "img": "…", "raw_usage": 0.4, "type": -1 },
      "5": { "level": 5, "employees": 10, "cost": 425, "img": "…", "raw_usage": 0.5, "type": -1 },
      "6": { "level": 6, "employees": 10, "cost": 225, "img": "…", "raw_usage": 0.6, "type": -1 },
      "7": { "level": 7, "employees": 10, "cost": 0, "img": "…", "raw_usage": 2, "type": 0 }
    },
    "max_quality": 7, "own_tax": 100
  }
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Company ID |
| `holding_company_id` | string \| false | Holding, or `false` |
| `industry_id`, `industry_name`, `industry_token` | | 1 Food, 2 Weapons, 4 House, 23 Aircraft; raw companies use resource IDs (7–19, 21–22, 24–28). Tokens `FOOD`, `WEAPON`, `HOUSE`, `AIRCRAFT`, … |
| `quality` | number | 1–7 |
| `building_name`, `building_img`, `products_img`, `raw_img` | string | Display data |
| `is_raw` | boolean | Raw material company |
| `wam_enabled`, `can_work_as_manager` | boolean | WAM allowed for the type; possible now |
| `cannot_work_as_manager_reason` | string | `""`, `no_holding`, `location`, `war` |
| `can_assign_employees`, `is_assigned_to_holding` | boolean | |
| `preset_works`, `preset_own_work` | number | Preselected employee works and own work (0/1) |
| `base_production` | number | Units per work (10 weapons, 100 food) |
| `resource_bonus`, `effective_bonus` | number | Region bonus %, and base plus bonus (184) |
| `raw_usage`, `production` | number | Raw material used and units produced per work |
| `employee_limit` | string | Maximum employees |
| `already_worked` | boolean | WAM done today |
| `todays_works`, `total_works` | number | Employee works done; maximum today |
| `upgrade_url`, `sell_url` | string | Links |
| `upgrades.{level}` | object | `level`, `employees`, `cost` (Gold), `img`, `raw_usage` (per product), `type`: `-1` below current, `0` current, `1` above |
| `max_quality` | number | Highest level |
| `own_tax` | number | WAM tax in local currency |

The client locks levels above 5 for the `HOUSE` and `AIRCRAFT` industries.

### `holdingCompanies`

```json
{
  "222222": {
    "id": 222222, "owner_id": 1000001, "owner_entity_type": "citizen", "region_id": 1,
    "name": "Holding Name", "created_at": 1700000000, "num_factories": 10,
    "avatar": "https://www.erepublik.net/images/default_avatars/holding_100x100.png"
  }
}
```

`region_id` is the holding's region; `created_at` is Unix time.

### `pageDetails`

| Field | Type | Description |
|-------|------|-------------|
| `total_works` | string | Employee work slots |
| `food_raw_stock`, `weapon_raw_stock`, `house_raw_stock`, `airplane_raw_stock` | number | Raw material stocks (FRM, WRM, HRM, ARM) |
| `health`, `energy` | number | Current energy; the client counts `energy` on top for energy bars. Earlier notes call `energy` the cost of the preset work |
| `recoverable_health` | object | `value`, `reset` (Unix) |
| `recoverable_health_in_food` | number | Energy value of stored food |
| `employee` | object | Your job: `salary`, `total_daily_salary`, `currency`, `employer` (`id`, `name`, `created_at`, `has_avatar`), `alreadyWorked`, `taxes` |
| `next_overtime_work` | number | Unix time of the next overtime |
| `overtime_points` | number | Overtime points |
| `overtime_works` | number | Earlier notes: total overtime work count |
| `president` | array | Empty, or president data |
| `show_first_view`, `tutorial` | boolean | Interface flags |
| `food`, `weapon`, `house`, `airplane` | object | Industry definitions, below |
| `*_tip`, `*_tooltip`, `*_warn*`, `*_critical` | string | Interface texts |

Industry definition: `id` (1, 2, 4, 23), `name`, `token`, `bp` (base production: 100 food, 10 weapons), `max_quality`, `all_qualities` (includes special items), `attributes` (quality → `effect`, `uses`, `name`; plus `effect_name`, `use_name`, `type`), `tooltip_attr`, `bomb_ids` (weapons), `buildingName`, `wam_enabled`, `raw` (`id`, `name`, `short_name`, `token`, `is_raw`), `industryBuildingIcon`.

<details>
<summary>Food and weapon qualities (2026-11-27)</summary>

| Food quality | Energy | Name |
|--------------|--------|------|
| 0 | 1 | |
| 1–6 | 2, 4, 6, 8, 10, 12 | |
| 7 | 20 | |
| 10 | 100 | Energy Bar |
| 11 | 200 | Double Energy Bar |
| 12 | 50 | Pumpkin |
| 13 | 50 | Carrot |
| 14 | 50 | Ice Cream |
| 15 | 50 | Pumpkin |
| 16 | 50 | Winter Treat |
| 17 | 300 | Triple Energy Bar |
| 18 | 10 | Bonbons |

| Weapon quality | Fire power | Uses | Name |
|----------------|-----------|------|------|
| 0 | 5 | 1 | |
| 1–6 | 20–120 (step 20) | 1–6 | |
| 7 | 200 | 10 | |
| 8 | 250 | 999 | |
| 10 | 10000 | 3 | Bazooka |
| 11, 100 | 100 | 3 | |
| 21, 22 | 1500000, 5000000 | 1 | Small Bomb, Big Bomb |
| 51, 52 | 50000000, 100000000 | 1 | SLG Peacemaker, BB Scatter Bomb |
| 211, 212, 213 | 75000, 375000, 750000 | 1 | Small Bomb |
| 215 | 1500000 | 1 | Cruise Missile |
| 216 | 180000000 | 1 | Annihilator MK18 |

</details>

### `totals`

`food_raw`, `weapon_raw`, `house_raw`, `airplane_raw` (raw materials the preset work uses), `food_units`, `weapon_units`, `house_units`, `airplane_units`, `health_used` (negative), `own_works`, `preset_works`, `preset_own_works`, `work_as_manager_tax` (negative, local currency), and `*_img` icons.

### `energyData`

```json
{ "energy": 100, "energyPoolLimit": 1000, "recoverableEnergy": 0, "energyPerInterval": 10, "energyStatus": "recovering" }
```

`energyPerInterval` is per 6 minutes (earlier notes). `energyStatus`: `recovering`, `full`, or `limitReached` (energy above the pool limit). Details: [Energy](../home/energy.md).

### Scalar variables

| Variable | Description |
|----------|-------------|
| `csrfToken` | [CSRF token](../guides/requests.md#csrf-token) |
| `food_remaining` | Earlier notes: food raw materials remaining after preset production (see [Open questions](#open-questions)) |
| `has_captcha` | Whether a [captcha](work.md#submit-a-captcha) is required (`0` / `1`) |
| `has_pin` | Whether a safety code is set (`0` / `1`) |
| `is_organization` | Empty for citizens |
| `rawMultiplier` | Raw material multiplier per employee (`0.01`) |
| `promoMultiplier`, `promoMultiplierHouse` | Upgrade discount fraction, `0` without a promotion; the second applies to House companies |
| `defaultHoldingsOpenClosedState` | `closed` or `open` |
| `work_url`, `work_overtime_url`, `resign_url`, `captcha_url` | See [Work](work.md) and [Resign](job-market.md#resign) |
| `upgarade_url` | [Upgrade](#upgrade-a-company) (misspelled by the game) |
| `linkForBuy` | [Marketplace](marketplace.md) link |
| `serverTime`, `resign_confirm`, `raw_consumption`, `MY_COMPANIES_TEXTS` | Interface values |

**Source:** real request, 2026-11-27; field notes from earlier documentation.

---

## Build page

`GET /en/economy/create-company` · Auth required · ✅ Verified 2026-11-27

HTML page for new companies, storage and training grounds. Variables: `prices` (industry ID → `value`, `currency`: `gold` or `local`), `training_grounds_prices` (`[0,100,200,300]`), your `gold` and `money`.

**Source:** real request, 2026-11-27.

---

## Build

`POST /en/economy/build-company` · Auth required · 🔍 From code

> [!WARNING]
> Spends Gold or currency.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `buildingType` | number | Yes | `1` company, `2` storage, `3` training ground |
| `industryId` | number | Yes | Industry; for storage `1` small (currency), `2` large (Gold) |
| `amountToBuild` | number | Yes | 1–99 |

```bash
curl 'https://www.erepublik.com/en/economy/build-company' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'buildingType=1&industryId=1&amountToBuild=1&_token=YOUR_CSRF_TOKEN'
```

JSON with `error` and `message`.

**Source:** `company/create.js` (version 1698060179).

---

## Company market

`GET /en/economy/buy-company/{countryId}` · Auth required · ✅ Verified 2026-11-27

HTML list of companies for sale. It links filters of the form `/en/economy/buy-company/{industryId}/{quality}` for industries 1, 2, 4 and 23. The observed listings were empty.

**Source:** real request, 2026-11-27.

---

## Buy a company

`POST /en/economy/buy-company/…` · Auth required · 🔍 From code

> [!WARNING]
> Spends Gold.

Buying several at once sends one request per company to a URL built by the server, observed as `/en/economy/buy-company/{countryId}/0/asc/1`:

| Name | Type | Description |
|------|------|-------------|
| `_token` | string | CSRF token |
| `companyId` | number | The offer |
| `format` | string | `json` |

The answer has `error` and `message`. A single purchase submits a page form with the field `buyCompany_companyId`.

**Source:** inline script of the company market page, 2026-11-27.

---

## Upgrade a company

`POST /en/economy/upgrade-company` · Auth required · 🔍 From code

> [!WARNING]
> Changes the company level: an upgrade spends Gold, a downgrade refunds it.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `type` | string | Yes | `upgrade` |
| `companyId` | number | Yes | |
| `level` | number | Yes | Target level |
| `pin` | string | Yes | Safety code when required |
| `shownCost` | number | Yes | The price displayed to the player |

Answers: `status: true`; or `status: false` with `message` `redirect` or `not_enough_gold`; `message` `INVALID_PIN`, `PIN_ATTEMPTS`.

**Source:** `citizen/myCompanies.js` (version 1760600930), `upgradeCompany`.

---

## Sell or dissolve

`GET /en/economy/sell-company/{companyId}` · Auth required · 🔍 From code

> [!WARNING]
> The **Sell/Dissolve** button of a company links here. The page was not opened; do not call it unless you mean to sell or dissolve.

Parameters of the form behind it are unknown.

**Source:** My Places markup and `companies.{id}.sell_url`, 2026-11-27.

---

## Advanced buildings page

`GET /en/economy/advanced-buildings` · Auth required · ✅ Verified 2026-11-27

HTML page with special buildings such as the rocket factory. Variables: `upgrade_factory_url`, `FACTORY_TEXTS` (the first three factory levels produce 2 rockets a day, higher ones one rocket and one damage booster), `has_pin`. Assembling items: [Inventory](inventory.md#assemble).

**Source:** real request, 2026-11-27.

---

## Upgrade an advanced building

`POST /en/economy/upgrade-factory` · Auth required · 🔍 From code

> [!WARNING]
> Changes the building level and may spend Gold.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `type` | string | Yes | `upgrade_factory` |
| `level` | number | Yes | Target level |
| `pin` | string \| boolean | Yes | Safety code for a downgrade, otherwise `false` |
| `shownCost` | number | Yes | Displayed price |

Answers as for [Upgrade a company](#upgrade-a-company).

The older script `citizen/healthBuildings.js` (version 1704452307), still loaded, posts `type=townHall`, `shownCost=0` to a variable `upgrade_url` that the page no longer defines.

**Source:** `citizen/armory.js` (version 1704452307), `upgradeBuilding`.

---

## Create holding popup

`GET /en/economy/create-holding-popup` · Auth required · ✅ Verified 2026-11-27

HTML popup for a new holding in your current region. The script variable `popupPageDetails` describes the place:

| Field | Description |
|-------|-------------|
| `region`, `country` | Region (with `city`, `resources`, `determination`) and its owner (with `productivityBonus`) |
| `currencies` | `country`, `entity` (yours) |
| `cost` | Price of the holding, formatted |
| `taxes` | `vat`, `import`, `income` per industry ID |
| `incomeTax` | Formatted percent |
| `salary` | `minimum`, `average`, `highest` |
| `embargoes`, `pollution` | Trade embargoes; pollution per industry and level |
| `industries` | Industry definitions as in [`pageDetails`](#pagedetails) |

**Source:** real request, 2026-11-27.

---

## Create holding

`POST /en/economy/create-holding` · Auth required · 🔍 From code

> [!WARNING]
> Creates a holding and spends its price.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `create` |
| `holdingCompanyName` | string | Yes | Name |

Answer: `error`, `message`.

**Source:** script of the create holding popup, 2026-11-27.

---

## Edit holding popup

`POST /en/economy/edit-holding-popup` · Auth required · ✅ Verified 2026-11-27

HTML popup with the holding's location, bonuses, taxes, salaries and the rename and delete controls. Read-only. A `GET` answers `404`.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `holdingCompanyId` | number | Yes | Holding |

```bash
curl 'https://www.erepublik.com/en/economy/edit-holding-popup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'holdingCompanyId=222222&_token=YOUR_CSRF_TOKEN'
```

**Source:** real request, 2026-11-27.

---

## Rename or delete holding

`POST /en/economy/edit-holding` · Auth required · 🔍 From code

> [!WARNING]
> Renames or deletes the holding.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `changeName` or `delete` |
| `holdingCompanyId` | number | Yes | |
| `holdingCompanyName` | string | For `changeName` | New name |

Answer: `error`, `message`.

**Source:** script of the edit holding popup, 2026-11-27.

---

## Move one company popup

`POST /en/economy/assign-to-holding-popup` · Auth required · ✅ Verified 2026-11-27

HTML popup listing target holdings with the price of moving there. Read-only. A `GET` answers `404`.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `factoryId` | number | Yes | Company ID |

The script variable `assignPageDetails.holdingCompanies.{id}` holds the [holding fields](#holdingcompanies) plus `locationInfo` (`region`, `country`, `city`), `incorporationCost` (formatted) and `incorporationData`:

```json
{
  "base_value": 17000, "upgrades_value": 121000, "multiplier": 0, "company_value": 138000,
  "holding_company_id": 222222, "current_region_id": 2,
  "travel_distance": "N/A", "travel_cost": "N/A",
  "tokens_cost": 3, "cost": 0, "quality": 3
}
```

`cost` is the relocation price in currency, `tokens_cost` the price in Factory Reset Tokens.

**Source:** real request, 2026-11-27.

---

## Move one company

`POST /en/economy/assign-to-holding` · Auth required · 🔍 From code

> [!WARNING]
> Moves the company and spends currency or Factory Reset Tokens.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `assign` |
| `holdingCompanyId` | number | Yes | Target holding |
| `factoryId` | number | Yes | Company |
| `acceptedCost` | number | Yes | `incorporationData.cost` of the target |
| `acceptedTokens` | number | Yes | `incorporationData.tokens_cost` |

The script's comment says the server refuses the move when the price no longer matches. Answer: `error`, `message`.

**Source:** script of the popup, 2026-11-27.

---

## Move several popup

`POST /en/economy/assign-multiple-to-holding-popup` · Auth required · ✅ Verified 2026-11-27

As [Move one company popup](#move-one-company-popup), with `factoriesIds[]` instead of `factoryId`; it shows the total price. Read-only.

```bash
curl 'https://www.erepublik.com/en/economy/assign-multiple-to-holding-popup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode 'factoriesIds[0]=111111' \
  --data-urlencode 'factoriesIds[1]=111112' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

**Source:** real request, 2026-11-27.

---

## Move several

`POST /en/economy/assign-multiple-to-holding` · Auth required · 🔍 From code

> [!WARNING]
> Moves the companies and spends currency or Factory Reset Tokens.

Parameters: `_token`, `action=assign`, `holdingCompanyId`, `factoriesIds[]`, `acceptedCost`, `acceptedTokens`. Answer: `error`, `message`.

**Source:** script of the popup, 2026-11-27.

---

## Unassign popup

`POST /en/economy/unassign-factories-popup` · Auth required · ✅ Verified 2026-11-27

HTML popup with the price (in Factory Reset Tokens) of removing companies from their holdings. Parameters: `_token`, `factoriesIds[]`. Read-only.

**Source:** real request, 2026-11-27.

---

## Unassign

`POST /en/economy/unassign-factories` · Auth required · 🔍 From code

> [!WARNING]
> Removes the companies from their holdings and spends Factory Reset Tokens.

Parameters: `_token`, `action=unassign`, `factoriesIds[]`. Answer: `error`, `message`.

**Source:** script of the popup, 2026-11-27.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Food quality 12 and 18 | 12 `Treat`, 18 `Bonbons` | 12 `Pumpkin`, 18 `Bonbons` | Live page |
| `pageDetails.energy` | Energy cost of preset work | Added to recoverable energy in the energy bar check | Not settled |
| `effective_bonus` | `base_production + resource_bonus` | `10` and `84` give `184` only with base 100% | Not settled |
| `food_remaining` | Food raw materials left after preset production | [Marketplace](marketplace.md#marketplace-page) notes call the same name the remaining daily food limit | Not settled |
| `upgrade_url` of a company | Customize URL | Upgrades go to `upgrade-company` | — |
