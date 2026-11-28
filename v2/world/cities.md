# Cities and residence

[API docs](../README.md) › World and travel › Cities and residence

The city page of every region (Overview, Residents, My properties) and the residence you can establish there. The page is an Angular app (`ErpkResidenceController`) that loads each panel as JSON.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [City page](#city-page) | GET | `/en/main/city/{permalink}/{panel}` | ✅ Verified 2026-11-27 |
| [My residence](#my-residence) | GET | `/en/main/myResidence` | ✅ Verified 2026-11-27 |
| [City overview](#city-overview) | GET | `/en/main/city-data/{cityId}/overview` | ✅ Verified 2026-11-27 |
| [Residents](#residents) | GET | `/en/main/city-data/{cityId}/residents` | ✅ Verified 2026-11-27 |
| [My properties](#my-properties) | GET | `/en/main/city-data/{cityId}/properties` | ✅ Verified 2026-11-27 |
| [Single widget](#single-widget) | GET | `/en/main/city-data/{cityId}/{panel}/{widget}` | ✅ Verified 2026-11-27 |
| [Search cities](#search-cities) | GET | `/en/main/city-search` | ✅ Verified 2026-11-27 |
| [Establish residence](#establish-residence) | POST | `/en/main/city-actions` | 🔍 From code |
| [Help popup](#help-popup) | GET | `/en/main/city-help-popup` | ✅ Verified 2026-11-27 |

Activating a house from the properties panel calls `/en/economy/activateHouse`, described with the [boosters](../economy/inventory.md#activate-a-house).

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## City page

`GET /en/main/city/{permalink}/{panel}` · Auth not required · ✅ Verified 2026-11-27

HTML shell of the page; the data comes from [city data](#city-overview).

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `permalink` | path | string | Yes | City permalink, for example `Warsaw` |
| `panel` | path | string | No | `overview`, `residents` or `properties` |

```bash
curl 'https://www.erepublik.com/en/main/city/Warsaw/residents' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

The chosen panel appears in the page as `initialPanel: 'residents'`. An unknown city returns `404`. The page also answers `200` without a session. When the client switches tabs it only rewrites the browser address to this form.

**Source:** real requests, 2026-11-27; `ErpkResidenceController` in `erpk_angular_controllers.js` (version 1795779904).

---

## My residence

`GET /en/main/myResidence` · Auth required · ✅ Verified 2026-11-27

The menu link to your residence. It lands on `/en/main/city/{permalink}/overview` of your residence city.

```bash
curl -L 'https://www.erepublik.com/en/main/myResidence' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

The behaviour without a residence was not checked. Inside the city page, "my residence" does not call this: it reloads [city data](#city-overview) for your residence city ID.

**Source:** real request in a browser (final URL), 2026-11-27.

---

## City overview

`GET /en/main/city-data/{cityId}/overview` · Auth not required · ✅ Verified 2026-11-27

City statistics, the region, and the cost and bonuses of establishing residence.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `cityId` | path | number | Yes | City ID |

```bash
curl 'https://www.erepublik.com/en/main/city-data/231/overview' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

All three panels answer `200` without a session, with `citizenInfo.isLoggedIn: 0` and zeroed bonuses.

### Response

Shortened:

```json
{
  "citizenInfo": { "isLoggedIn": 1, "isResident": 0, "...": "..." },
  "cityInfo": { "id": 231, "name": "Warsaw", "regionId": 424, "countryId": 35, "numResidents": 78 },
  "panelId": "overview",
  "panelUrl": "//www.erepublik.com/en/city/231/overview",
  "widgets": { "searchCity": {}, "regionInfo": {}, "residence": {}, "government": {} }
}
```

<details>
<summary>Full example response (structure of 2026-11-27; edited)</summary>

The city budget is `0`. Timers, house counts and the establishment cost are made up, and `CUR` stands for the currency code.

```json
{
  "citizenInfo": {
    "isLoggedIn": 1,
    "residenceCityId": 0,
    "isResident": 0,
    "isLocatedInResidence": 0,
    "residentialBonuses": {
      "hasResidence": 1,
      "isInResidence": 0,
      "validUntil": 2000000000,
      "timeRemaining": 1000,
      "numHouses": 1,
      "energyBonus": 50,
      "recoveryBonus": 2,
      "description": "Residence bonuses partially available. Energy Pool bonus expires in:"
    }
  },
  "cityInfo": {
    "id": 231,
    "name": "Warsaw",
    "permalink": "Warsaw",
    "regionId": 424,
    "cityTypeId": 2,
    "regionName": "Mazovia",
    "regionPermalink": "Mazovia",
    "countryId": 35,
    "countryName": "Poland",
    "countryPermalink": "Poland",
    "avatar": "//www.erepublik.net/images/modules/cities/128px/Warsaw.png",
    "countryCapital": 0,
    "countryHistoricalCapital": 1,
    "citySize": "Large Town",
    "numResidents": 78,
    "numVoters": 0,
    "buildingTax": 2,
    "buildingBonus": 10,
    "numVisitors": 78,
    "cityBudget": 0,
    "grossDomesticProduct": 17942636,
    "cityCouncilSeats": 0,
    "regionUrl": "//www.erepublik.com/en/main/region/Mazovia",
    "countryUrl": "//www.erepublik.com/en/country/society/Poland",
    "nationalRank": 2,
    "globalRank": 21
  },
  "panelId": "overview",
  "panelUrl": "//www.erepublik.com/en/city/231/overview",
  "widgets": {
    "searchCity": {
      "actionUrl": "//www.erepublik.com/en/main/city-search",
      "action": "searchCity",
      "widgetId": "searchCity"
    },
    "regionInfo": {
      "regionId": 424,
      "regionName": "Mazovia",
      "regionPermalink": "Mazovia",
      "regionUrl": "//www.erepublik.com/en/main/region/Mazovia",
      "regionOf": { "countryId": 35, "countryName": "Poland", "countryPermalink": "Poland" },
      "underOccupation": 0,
      "underOccupationBy": { "countryId": null, "countryName": null, "countryPermalink": null },
      "underOccupationSince": "Day 6,802",
      "liberatedSince": "Day 6,804",
      "resources": [
        { "id": 11, "regionId": 424, "name": "Sand", "mtype": "house", "rarity": "very common", "bonus": 10 },
        { "id": 12, "regionId": 424, "name": "Clay", "mtype": "house", "rarity": "uncommon", "bonus": 20 },
        { "id": 13, "regionId": 424, "name": "Wood", "mtype": "house", "rarity": "common", "bonus": 15 },
        { "id": 15, "regionId": 424, "name": "Granite", "mtype": "house", "rarity": "very rare", "bonus": 30 }
      ],
      "productivityBonus": {
        "byToken": { "FOOD": 0, "WEAPON": 0, "HOUSE": 75, "AIRCRAFT": 0 },
        "byId": { "1": 0, "2": 0, "4": 75, "23": 0 }
      },
      "buildingTax": "2%",
      "residentialTax": "200 CUR",
      "gdp": "17,942,636 CUR"
    },
    "residence": {
      "isResident": 0,
      "canEstablishResidence": 0,
      "citizenResidenceCityId": 0,
      "establishResidenceCost": {
        "residentialTax": 200,
        "propertiesTransfer": { "houses": { "1": 1000 }, "total": 1000 },
        "total": 1200
      },
      "establishResidenceConfirmation": "Are you sure you want to pay 1200 CUR in local taxes and establish your residence in Warsaw?",
      "bonuses": {
        "houseDurability": 10,
        "energyBonus": 100,
        "recoveryBonus": 2,
        "numHouses": 1,
        "parkEnergyBonus": 100,
        "parkRecoveryBonus": 2,
        "cityBonuxText": "<b>+10%</b> House durability",
        "housesBonusText": "<b>+2</b> Energy / 6 minutes, <b>+100</b> Energy pool",
        "parkBonusText": "<b>+2</b> Energy / 6 minutes, <b>+100</b> Energy pool"
      },
      "actionUrl": "//www.erepublik.com/en/main/city-actions",
      "action": "establishResidence",
      "widgetUrl": "//www.erepublik.com/en/main/city-data/231/overview/residence",
      "widgetId": "residence"
    },
    "government": {
      "mayor": [],
      "numCityCouncilors": "-",
      "widgetUrl": "//www.erepublik.com/en/main/city-data/231/overview/government",
      "widgetId": "government"
    }
  }
}
```

</details>

### Common fields

Every panel returns `citizenInfo`, `cityInfo`, `panelId`, `panelUrl` and `widgets`.

| Field | Type | Description |
|-------|------|-------------|
| `citizenInfo.isLoggedIn` | number | `1` / `0` |
| `citizenInfo.residenceCityId` | number | `0` in all observed responses, including accounts with a residence |
| `citizenInfo.isResident` | number | You are a resident of this city |
| `citizenInfo.isLocatedInResidence` | number | You are in your residence |
| `citizenInfo.residentialBonuses` | object | See [residence bonus fields](travel.md#residence-bonus-fields) |
| `cityInfo.cityTypeId`, `citySize` | | City type and size name, for example "Large Town" |
| `cityInfo.countryCapital`, `countryHistoricalCapital` | number | Current / historical capital |
| `cityInfo.numResidents`, `numVoters`, `numVisitors` | number | Population counters |
| `cityInfo.buildingTax`, `buildingBonus` | number | Percent |
| `cityInfo.cityBudget`, `grossDomesticProduct` | number | In local currency |
| `cityInfo.cityCouncilSeats` | number | Council seats |
| `cityInfo.nationalRank`, `globalRank` | number | City ranks |

### Overview widgets

| Field | Description |
|-------|-------------|
| `regionInfo.regionOf` | Original owner of the region |
| `regionInfo.underOccupation`, `underOccupationBy` | Occupation state |
| `regionInfo.underOccupationSince`, `liberatedSince` | Game days; present even when not occupied |
| `regionInfo.resources[]` | `id`, `name`, `mtype`, `rarity`, `bonus` |
| `regionInfo.productivityBonus` | `byToken` and `byId` (industry ID) |
| `residence.canEstablishResidence` | `1` when the button is available |
| `residence.establishResidenceCost` | `residentialTax`, `propertiesTransfer` (by house quality), `total` |
| `residence.bonuses` | Bonuses of living here. `cityBonuxText` is spelled that way |
| `government.mayor` | Array; empty without a mayor |
| `government.numCityCouncilors` | Number, or `"-"` |

**Source:** real request, 2026-11-27.

---

## Residents

`GET /en/main/city-data/{cityId}/residents` · Auth not required · ✅ Verified 2026-11-27

Residents of a city, 10 per page.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `cityId` | path | number | Yes | City ID |
| `currentPage` | query | number | No | Page, from 1 |
| `search` | query | string | No | Filter by resident name |

The response names both query parameters itself (`pageParam`, `searchParam`).

```bash
curl 'https://www.erepublik.com/en/main/city-data/231/residents?currentPage=2' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

`widgets`, shortened to one resident. The resident, counts and totals are made up:

```json
{
  "searchCity": { "...": "..." },
  "residents": {
    "residents": [
      {
        "citizenId": 1234567,
        "citizenName": "CITIZEN_NAME",
        "hasAvatar": 1,
        "residentSince": "Day 1,000",
        "isMayor": 0,
        "isCouncilMember": 0,
        "isRegisteredVoter": 0,
        "countryId": 35,
        "countryName": "Poland",
        "countryPermalink": "Poland",
        "hasHolding": 1,
        "holdingName": "HOLDING_NAME",
        "numFactories": 1,
        "numHouses": 1,
        "activeHouses": ["q1"],
        "roleId": 1,
        "roleName": "Resident",
        "avatar": "AVATAR_URL",
        "holdingAvatar": null
      }
    ],
    "numResults": 78,
    "searchParam": "search",
    "pageParam": "currentPage"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `numResults` | number | Matching residents, for pagination; `0` when the search finds nobody |
| `residentSince` | string | Game day |
| `countryId`, `countryName` | | Citizenship, may differ from the city's country |
| `numFactories`, `numHouses` | number | The resident's factories and houses here |
| `activeHouses` | string[] | Qualities of active houses |
| `roleId`, `roleName` | | `1` = Resident |

The client numbers the rows itself: `index + 1 + 10 * (currentPage - 1)`.

**Source:** real requests (first page, second page, empty search), 2026-11-27; `ErpkResidenceFactory.getPanelData` (version 1795779904).

---

## My properties

`GET /en/main/city-data/{cityId}/properties` · Auth required · ✅ Verified 2026-11-27

Your houses and holding company. The client disables the tab when you are logged out.

```bash
curl 'https://www.erepublik.com/en/main/city-data/231/properties' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

`widgets` for a city where the account does not live. Names, IDs, times and counts are made up:

```json
{
  "houses": {
    "list": [],
    "currency": 0,
    "widgetUrl": "/en/main/city-data/231/properties/houses",
    "widgetId": "houses"
  },
  "holdingCompany": {
    "list": {
      "ownerId": 1234567,
      "ownerEntityType": "citizen",
      "regionId": 424,
      "name": "HOLDING_NAME",
      "createdAt": 1000000000,
      "numFactories": 1,
      "avatar": "AVATAR_URL",
      "buttonText": "Manage",
      "buttonUrl": "//www.erepublik.com/en/economy/myCompanies"
    },
    "factoriesList": [
      {
        "industryId": 4, "quality": 5, "name": "House Q5", "isRaw": 0,
        "numFactories": 1, "maxEmployees": 10,
        "icon": "//www.erepublik.net/images/modules/manager/factory_buildings/4_5.png"
      }
    ],
    "widgetUrl": "/en/main/city-data/231/properties/holdingCompany",
    "widgetId": "holdingCompany"
  }
}
```

The top level also has `hasPropertiesInCity`.

<details>
<summary>House list in the residence city (earlier notes, one entry per kind; edited)</summary>

The city, currency, times, counts and charge are placeholders.

```json
[
  { "isDefault": 1, "name": "Central Park", "energyBonus": 100, "energyBooster": 2, "effectsActive": 1 },
  {
    "quality": 1,
    "energyBonus": 100,
    "energyBooster": 2,
    "overtimePoints": 1,
    "effectsActive": 1,
    "activeTimeRemaining": 1000,
    "inventory": 1,
    "marketUrl": "//www.erepublik.com/en/economy/marketplace#1/4/1",
    "buttonText": "Extend",
    "activationMessage": "Extension successful",
    "activationData": {
      "params": {
        "action": "activate",
        "quality": 1,
        "cityId": 1,
        "cityWidget": "L2VuL21haW4vY2l0eS1kYXRhLzEvcHJvcGVydGllcy9ob3VzZXM="
      },
      "url": "/en/economy/activateHouse"
    },
    "confirmationMessage": "The city of CITY_NAME charges 100 CUR for this action. Continue?"
  }
]
```

</details>

| Field | Description |
|-------|-------------|
| `houses.list[]` | Central Park (`isDefault`) and houses Q1–Q5 |
| `energyBonus`, `energyBooster` | Energy pool and recovery per 6 minutes. Earlier notes: pool 100 / 160 / 200 / 300 / 400 for Q1–Q5 |
| `effectsActive`, `activeTimeRemaining` | Active state and seconds left |
| `inventory` | Houses of this quality you own |
| `buttonText` | `Use`, `Extend` or `Buy` |
| `activationData` | `url` and `params` to post for activation; `cityWidget` is the Base64 widget path |
| `confirmationMessage` | Shows the charge before activating |
| `houses.currency` | Your balance in the city's currency |
| `holdingCompany.list` | The holding; `createdAt` is a Unix time |
| `factoriesList[]` | Factories here by type: `industryId`, `quality`, `isRaw`, `numFactories`, `maxEmployees` |

Earlier notes (❔ Unconfirmed):

- Central Park is in every city; the list holds it plus all five qualities, owned or not.
- Only one house quality can be active at a time in a city; a house lasts about 7 days.
- Activation is paid in local currency and includes the city's residential tax. A `Use` house costing 0 may come from rewards.
- The endpoint returns only your own properties. `maxEmployees` is `numFactories` times the workers per factory.

**Source:** real request, 2026-11-27 (empty house list); house entries — earlier notes; `activateHouse` handling in `ErpkResidenceController` (version 1795779904).

---

## Single widget

`GET /en/main/city-data/{cityId}/{panel}/{widget}` · Auth unknown · ✅ Verified 2026-11-27 (overview widgets)

Returns one widget object, the same as inside the panel response. Each widget gives its own `widgetUrl`.

| Path | Status |
|------|--------|
| `…/overview/residence` | ✅ Verified 2026-11-27 |
| `…/overview/government` | ✅ Verified 2026-11-27 |
| `…/properties/houses` | Listed in `widgetUrl`, not requested |
| `…/properties/holdingCompany` | Listed in `widgetUrl`, not requested |

```bash
curl 'https://www.erepublik.com/en/main/city-data/231/overview/government' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

```json
{
  "mayor": [],
  "numCityCouncilors": "-",
  "widgetUrl": "//www.erepublik.com/en/main/city-data/231/overview/government",
  "widgetId": "government"
}
```

### Errors

| Response | When |
|----------|------|
| `200` `{"status":false,"message":"Invalid City","error":true}` | Unknown city ID (checked on a panel) |
| `404` `{"error":true,"message":"404 Not Found","status":false,"code":404}` | Unknown widget path, for example `…/residents/residents` |

**Source:** real requests, 2026-11-27.

---

## Search cities

`GET /en/main/city-search` · Auth not required · ✅ Verified 2026-11-27

The city search box of the city page. Works without a session.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `search` | query | string | Yes | At least 2 characters |
| `action` | query | string | No | The client sends `searchCity` |

```bash
curl 'https://www.erepublik.com/en/main/city-search?action=searchCity&search=War' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened:

```json
{
  "status": true,
  "data": [
    {
      "id": 231,
      "name": "Warsaw",
      "permalink": "Warsaw",
      "regionName": "Mazovia",
      "countryName": "Poland",
      "avatar": "//www.erepublik.net/images/modules/cities/128px/Warsaw.png",
      "url": "//www.erepublik.com/en/main/city/Warsaw"
    }
  ],
  "error": false
}
```

One character or no match returns `"data": []` with `status: true`.

Earlier notes: matching is a case-insensitive substring over city, region and country name; there is no pagination; `countryName` is the current holder of the region.

**Source:** real requests, 2026-11-27; `ErpkResidenceFactory.searchForCities` (version 1795779904).

---

## Establish residence

`POST /en/main/city-actions` · Auth required · 🔍 From code

> [!WARNING]
> Makes this city your residence and charges the residential tax and property transfer cost in local currency. The game's help says you cannot return to having no residence.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `action` | string | Yes | `establishResidence` |
| `cityId` | number | Yes | City |

The client also posts `url`, `widget` (`residence`) and `panel` (the current panel), taken from the [residence widget](#overview-widgets).

```bash
curl 'https://www.erepublik.com/en/main/city-actions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=establishResidence&cityId=231&_token=YOUR_CSRF_TOKEN'
```

### Response

The client reads `status`, `message` and `currency` (the new balance). On success it reloads the panel, and the whole page for your first residence.

### Errors

| Response | When |
|----------|------|
| `{"status":false,"message":"Invalid City","error":true}` | Missing or unknown `cityId` (earlier notes) |
| `"CSRF attack detected."` | Missing `_token` (earlier notes). The client compares with `CSRF attack detected3.` |

**Source:** `ErpkResidenceController.establishResidence` (version 1795779904); errors — earlier notes.

---

## Help popup

`GET /en/main/city-help-popup` · Auth required · ✅ Verified 2026-11-27

HTML walkthrough of cities and residence. The city page opens it once for citizens without a residence.

```bash
curl 'https://www.erepublik.com/en/main/city-help-popup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

Without a session: `{"error":"not_authorized"}`.

Rules it states:

- Cities have five size tiers by number of residents; size sets the residential and property taxes and the house durability bonus.
- To become a resident you travel to the city, then pay the residential tax and the prorated property tax of your active houses.
- Property tax is set by the mayor and city council.
- Residence gives +2 energy per 6 minutes and +50 energy pool, on top of the house bonuses, while you stay in the city. After leaving, the recovery bonus stops and the extra pool lasts 30 minutes.
- A resident's houses cannot be paused.

**Source:** real request, 2026-11-27; `launchHelpPopup` (version 1795779904).

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| `citizenInfo.residenceCityId` | Your residence city, `0` if none | `0` although the account has a residence; `residence.citizenResidenceCityId` is `0` too | Not settled |
| `hasPropertiesInCity` | Whether you have any properties there | `0` while the holding widget listed factories | Not settled; may count houses only |
| `regionInfo.regionOf` | Country information | Called "original owner" here from the field's role next to `underOccupationBy`; only an unoccupied region was read | Inference |
| `cityTypeId` | 1 = small, 2 = large | `2` for a "Large Town" | Not settled |
| Search and city page auth | Search needs none | Panels, search and page answer without a session | Live response |
| Energy bonus of a residence | +50 pool in one place, +100 in another | Help popup says +50; widget texts say +100 | Both reported |
