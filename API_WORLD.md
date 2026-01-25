# eRepublik API - World & Travel

#erepublik #api #world #travel #geography

This module covers world geography, travel mechanics, regions, and countries.

---

## Get Travel Data

**Method:** POST
**URL:** `/en/main/travelData`
**Auth Required:** Yes

### Description

Retrieves comprehensive travel information including all countries, regions, distances, travel costs, and current citizen location. This endpoint provides the data needed for the travel interface, showing where a citizen can travel, costs in currency/tickets, and distance calculations.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for the request |
| holdingId | number | No | Holding company ID (use 0 if not applicable) |
| battleId | number | No | Battle ID if traveling for battle (use 0 if not applicable) |
| regionId | number | Yes | Target region ID to get travel data for |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/travelData' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&holdingId=0&battleId=0&regionId=663'
```

### Example Response

```json
{
  "alreadyInRegion": false,
  "battleInfo": {
    "battleInfoText": "",
    "travelRequired": false,
    "travelData": false
  },
  "citizen": {
    "id": 123456,
    "citizenship": 72,
    "residence": {
      "hasResidence": 1,
      "cityId": 710,
      "regionId": 663,
      "countryId": 72,
      "bonuses": {
        "hasResidence": 1,
        "isInResidence": 0,
        "validUntil": 1769355117,
        "timeRemaining": 1777,
        "numHouses": 1,
        "energyBonus": 100,
        "recoveryBonus": 4,
        "description": "Residence bonuses partially available. Energy Pool bonus expires in:"
      },
      "cityInfo": {
        "id": 710,
        "name": "Klaipeda",
        "permalink": "Klaipeda",
        "regionId": 663,
        "cityTypeId": 2,
        "regionName": "Lithuania Minor",
        "regionPermalink": "Lithuania-Minor",
        "countryId": 72,
        "countryName": "Lithuania",
        "countryPermalink": "Lithuania"
      }
    }
  },
  "preselectCountryId": 72,
  "preselectRegionId": 663,
  "countries": {
    "167": {
      "id": 167,
      "name": "Albania",
      "permalink": "Albania",
      "hasRegionsFromOthers": true,
      "hasRegionsOccupiedByOthers": true,
      "regions": [626, 746, 745, 744]
    }
  },
  "regions": {
    "626": {
      "id": 626,
      "name": "Istria and Kvarner",
      "permalink": "Istria-Kvarner",
      "zoneName": "B3",
      "distanceInZones": 2,
      "distanceInKm": 1306,
      "isConquered": true,
      "isConqueredFrom": 63,
      "isConqueredBy": 167,
      "countryId": 167,
      "cityId": 666,
      "cityName": "Rijeka",
      "cost": 130,
      "ticket": 5,
      "ticketAmount": 1,
      "canMove": true,
      "isAlly": false,
      "isDiscounted": false,
      "isFree": false
    }
  }
}
```

### Response Fields

#### Top Level
- `alreadyInRegion` - Boolean indicating if citizen is already in the target region
- `battleInfo` - Object containing battle-related travel requirements
- `citizen` - Current citizen information including residence and bonuses
- `preselectCountryId` - Country ID to preselect in UI
- `preselectRegionId` - Region ID to preselect in UI
- `countries` - Object mapping country IDs to country data (74 countries total)
- `regions` - Object mapping region IDs to region data with travel details (573+ regions)

#### Region Object
- `distanceInZones` - Distance in zone jumps (affects cost)
- `distanceInKm` - Visual distance in kilometers
- `cost` - Travel cost in local currency
- `ticket` - Required ticket type ID (Q1-Q5)
- `ticketAmount` - Number of tickets required
- `canMove` - Whether travel is possible
- `isConquered` - Whether region is occupied
- `isConqueredFrom` / `isConqueredBy` - Original/occupying country IDs
- `isAlly` - Whether target is in an allied country
- `isDiscounted` - Travel cost discount applied
- `isFree` - Whether travel is free (e.g., to residence)

#### Residence Bonuses
- `energyBonus` - Energy pool bonus (default 100)
- `recoveryBonus` - Energy recovery bonus per 6 minutes
- `validUntil` - Unix timestamp when bonus expires
- `timeRemaining` - Seconds remaining on bonus
- `numHouses` - Number of houses owned

### Notes

- Response includes ALL countries and regions in the game (200KB+ of data)
- Travel costs calculated based on distance zones from current location
- Residence bonuses only apply when in residence region
- `_token` must be a valid CSRF token obtained from the page
- Region conquests shown via `isConquered`, `isConqueredFrom`, `isConqueredBy` fields
- Free travel available to: residence location, battles (if applicable), ally countries (sometimes)
- Distance zones (A, B1, B2, B3, etc.) determine base travel cost multiplier

---

## Travel to Region

**Method:** POST
**URL:** `/en/main/travel`
**Auth Required:** Yes

### Description

Executes the actual travel action to move the citizen to a different region. This endpoint performs the travel after the user has selected a destination, using either currency or travel tickets as payment based on the `travelMethod` parameter.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| check | string | Yes | Action type, always `moveAction` for travel |
| _token | string | Yes | CSRF token for the request |
| travelMethod | string | Yes | Payment method: `preferCurrency` or `preferTicket` |
| inRegionId | number | Yes | Destination region ID to travel to |
| toCountryId | number | Yes | Destination country ID (country containing the region) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/travel' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'check=moveAction&_token=YOUR_CSRF_TOKEN&travelMethod=preferCurrency&inRegionId=664&toCountryId=72'
```

### Example Response

**Success:**
```json
{
  "error": 0,
  "message": "success"
}
```

**Error (insufficient funds/tickets):**
```json
{
  "error": 1,
  "message": "You don't have enough currency/tickets to travel"
}
```

### Notes

- This endpoint actually performs the travel and deducts payment (currency or tickets)
- Call `travelData` endpoint first to get region IDs, costs, and validate travel is possible
- `travelMethod` determines payment preference:
  - `preferCurrency` - Use local currency if available, fallback to tickets
  - `preferTicket` - Use travel tickets if available, fallback to currency
- Travel cost depends on distance zones (calculated in `travelData` endpoint)
- Free travel scenarios: to residence, during battles (if applicable), ally countries (sometimes)
- The `check=moveAction` parameter is required for the server to process the travel
- After successful travel, citizen's current location is updated server-side
- May fail if citizen is in a battle cooldown or has other travel restrictions

---

## Get City Overview Data

**Method:** GET
**URL:** `/en/main/city-data/{cityId}/overview`
**Auth Required:** Yes

### Description

Retrieves comprehensive information about a specific city including citizen residence status, city statistics (population, budget, GDP), region details, and available widgets for the city interface. This endpoint provides the data for the city overview page, showing residence bonuses, city government, and regional information.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| cityId | number | Yes | The ID of the city to retrieve data for (path parameter) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/city-data/710/overview' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

```json
{
  "citizenInfo": {
    "isLoggedIn": 1,
    "residenceCityId": 0,
    "isResident": 1,
    "isLocatedInResidence": 1,
    "residentialBonuses": {
      "hasResidence": 1,
      "isInResidence": 1,
      "validUntil": 0,
      "timeRemaining": 0,
      "numHouses": 1,
      "energyBonus": 50,
      "recoveryBonus": 2,
      "description": "Residence bonuses available"
    }
  },
  "cityInfo": {
    "id": 710,
    "name": "Klaipeda",
    "permalink": "Klaipeda",
    "regionId": 663,
    "cityTypeId": 2,
    "regionName": "Lithuania Minor",
    "regionPermalink": "Lithuania-Minor",
    "countryId": 72,
    "countryName": "Lithuania",
    "countryPermalink": "Lithuania",
    "avatar": "//www.erepublik.net/images/modules/cities/128px/Klaipeda.png",
    "countryCapital": 1,
    "countryHistoricalCapital": 0,
    "citySize": "Large Town",
    "numResidents": 54,
    "numVoters": 0,
    "buildingTax": 2,
    "buildingBonus": 10,
    "numVisitors": 68,
    "cityBudget": 20852463,
    "grossDomesticProduct": 2230462,
    "cityCouncilSeats": 0,
    "regionUrl": "//www.erepublik.com/en/main/region/Lithuania-Minor",
    "countryUrl": "//www.erepublik.com/en/country/society/Lithuania",
    "nationalRank": 1,
    "globalRank": 42
  },
  "panelId": "overview",
  "panelUrl": "//www.erepublik.com/en/city/710/overview",
  "widgets": {
    "searchCity": {
      "actionUrl": "//www.erepublik.com/en/main/city-search",
      "action": "searchCity",
      "widgetId": "searchCity"
    },
    "regionInfo": {
      "regionId": 663,
      "regionName": "Lithuania Minor",
      "regionPermalink": "Lithuania-Minor",
      "regionUrl": "//www.erepublik.com/en/main/region/Lithuania-Minor",
      "regionOf": {
        "countryId": 72,
        "countryName": "Lithuania",
        "countryPermalink": "Lithuania"
      },
      "underOccupation": 0,
      "underOccupationBy": {
        "countryId": null,
        "countryName": null,
        "countryPermalink": null
      },
      "underOccupationSince": "Day 5,627",
      "liberatedSince": "Day 5,630",
      "resources": [
        {
          "id": 6,
          "regionId": 663,
          "name": "Iron",
          "mtype": "weapon",
          "rarity": "very common",
          "bonus": 10
        },
        {
          "id": 8,
          "regionId": 663,
          "name": "Aluminum",
          "mtype": "weapon",
          "rarity": "common",
          "bonus": 15
        },
        {
          "id": 9,
          "regionId": 663,
          "name": "Oil",
          "mtype": "weapon",
          "rarity": "uncommon",
          "bonus": 20
        }
      ],
      "productivityBonus": {
        "byToken": {
          "FOOD": 0,
          "WEAPON": 45,
          "HOUSE": 0,
          "AIRCRAFT": 0
        },
        "byId": {
          "1": 0,
          "2": 45,
          "4": 0,
          "23": 0
        }
      },
      "buildingTax": "2%",
      "residentialTax": "200 LTL",
      "gdp": "2,230,462 LTL"
    },
    "residence": {
      "isResident": 1,
      "canEstablishResidence": 1,
      "citizenResidenceCityId": 0,
      "establishResidenceCost": {
        "residentialTax": 200,
        "propertiesTransfer": {
          "houses": {
            "1": 658.84
          },
          "total": 658.84
        },
        "total": 858.84
      },
      "establishResidenceConfirmation": "Are you sure you want to pay 858.84 LTL in local taxes and establish your residence in Klaipeda?",
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
      "widgetUrl": "//www.erepublik.com/en/main/city-data/710/overview/residence",
      "widgetId": "residence"
    },
    "government": {
      "mayor": [],
      "numCityCouncilors": "-",
      "widgetUrl": "//www.erepublik.com/en/main/city-data/710/overview/government",
      "widgetId": "government"
    }
  }
}
```

### Response Fields

#### citizenInfo Object
- `isLoggedIn` - Whether the user is logged in (1 = yes, 0 = no)
- `residenceCityId` - ID of the city where citizen has residence (0 if none)
- `isResident` - Whether citizen is a resident of this city
- `isLocatedInResidence` - Whether citizen is currently in their residence city
- `residentialBonuses` - Object containing residence bonus details:
  - `hasResidence` - Whether citizen has a residence established
  - `isInResidence` - Whether citizen is currently in residence city
  - `validUntil` - Unix timestamp when bonuses expire (0 if permanent/active)
  - `timeRemaining` - Seconds remaining on bonus (0 if in residence)
  - `numHouses` - Number of houses owned
  - `energyBonus` - Energy pool bonus amount
  - `recoveryBonus` - Energy recovery bonus per 6 minutes
  - `description` - Human-readable status description

#### cityInfo Object
- `id` - City ID
- `name` - City display name
- `permalink` - URL-friendly city name
- `regionId` - ID of the region containing this city
- `cityTypeId` - Type of city (1 = small, 2 = large, etc.)
- `regionName` / `regionPermalink` - Region name and URL slug
- `countryId` / `countryName` / `countryPermalink` - Country information
- `avatar` - URL to city image/icon
- `countryCapital` - Whether this is the current capital (1 = yes)
- `countryHistoricalCapital` - Whether this was a historical capital
- `citySize` - Human-readable size description (e.g., "Large Town")
- `numResidents` - Number of citizens with residence in this city
- `numVoters` - Number of voters in city elections
- `buildingTax` - Tax percentage for building companies
- `buildingBonus` - Production bonus percentage
- `numVisitors` - Number of current visitors
- `cityBudget` - Total city budget in local currency
- `grossDomesticProduct` - City GDP in local currency
- `cityCouncilSeats` - Number of city council seats (0 if no council)
- `nationalRank` - Rank among cities in the country
- `globalRank` - Global city rank

#### widgets Object
Contains configuration and data for various city page widgets:

**searchCity Widget**
- Provides city search functionality

**regionInfo Widget**
- `resources` - Array of natural resources in the region with rarity and production bonuses
- `productivityBonus` - Production bonuses by product type (FOOD, WEAPON, HOUSE, AIRCRAFT)
- `underOccupation` - Whether region is occupied (0 = no, 1 = yes)
- `underOccupationBy` - Occupying country information if applicable
- `buildingTax` / `residentialTax` - Local tax rates
- `gdp` - Regional GDP

**residence Widget**
- `canEstablishResidence` - Whether citizen can establish residence here
- `establishResidenceCost` - Breakdown of costs to establish residence:
  - `residentialTax` - One-time tax payment
  - `propertiesTransfer` - Cost to transfer properties (houses, etc.)
  - `total` - Total cost
- `bonuses` - Available residence bonuses (energy pool, recovery, house durability)
- `actionUrl` - Endpoint to establish residence

**government Widget**
- `mayor` - Array of mayor information (empty if no mayor)
- `numCityCouncilors` - Number of city council members

### Notes

- This endpoint provides the complete data for rendering the city overview page
- Citizen must be logged in to see personalized information (residence status, bonuses)
- City statistics (budget, GDP, residents) are real-time values
- Resources and productivity bonuses are crucial for company/factory placement decisions
- Residence bonuses apply only when citizen is physically in their residence city
- `cityTypeId` affects available building slots and city mechanics
- Capital cities (countryCapital = 1) may have special bonuses or restrictions
- The response includes widget configuration URLs for lazy-loading additional data
- City rankings (national/global) are based on multiple factors including GDP, population, activity

---

## Get City Residents List

**Method:** GET
**URL:** `/en/main/city-data/{cityId}/residents`
**Auth Required:** Yes

### Description

Retrieves a paginated list of citizens who have established residence in a specific city. This endpoint provides detailed information about each resident including their profile, holdings (companies), houses owned, government roles (mayor, council member), and citizenship status. The data is used to display the city's residents page.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| cityId | number | Yes | The ID of the city to retrieve residents for (path parameter) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/city-data/710/residents' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

```json
{
  "citizenInfo": {
    "isLoggedIn": 1,
    "residenceCityId": 0,
    "isResident": 1,
    "isLocatedInResidence": 1,
    "residentialBonuses": {
      "hasResidence": 1,
      "isInResidence": 1,
      "validUntil": 0,
      "timeRemaining": 0,
      "numHouses": 1,
      "energyBonus": 50,
      "recoveryBonus": 2,
      "description": "Residence bonuses available"
    }
  },
  "cityInfo": {
    "id": 710,
    "name": "Klaipeda",
    "permalink": "Klaipeda",
    "regionId": 663,
    "cityTypeId": 2,
    "regionName": "Lithuania Minor",
    "regionPermalink": "Lithuania-Minor",
    "countryId": 72,
    "countryName": "Lithuania",
    "countryPermalink": "Lithuania",
    "avatar": "//www.erepublik.net/images/modules/cities/128px/Klaipeda.png",
    "countryCapital": 1,
    "countryHistoricalCapital": 0,
    "citySize": "Large Town",
    "numResidents": 54,
    "numVoters": 0,
    "buildingTax": 2,
    "buildingBonus": 10,
    "numVisitors": 68,
    "cityBudget": 20852463,
    "grossDomesticProduct": 2230462,
    "cityCouncilSeats": 0,
    "regionUrl": "//www.erepublik.com/en/main/region/Lithuania-Minor",
    "countryUrl": "//www.erepublik.com/en/country/society/Lithuania",
    "nationalRank": 1,
    "globalRank": 42
  },
  "panelId": "residents",
  "panelUrl": "//www.erepublik.com/en/city/710/residents",
  "widgets": {
    "searchCity": {
      "actionUrl": "//www.erepublik.com/en/main/city-search",
      "action": "searchCity",
      "widgetId": "searchCity"
    },
    "residents": {
      "residents": [
        {
          "citizenId": 4222252,
          "citizenName": "resetas1",
          "hasAvatar": 1,
          "residentSince": "Day 4,016",
          "isMayor": 0,
          "isCouncilMember": 0,
          "isRegisteredVoter": 0,
          "countryId": 72,
          "countryName": "Lithuania",
          "countryPermalink": "Lithuania",
          "hasHolding": 1,
          "holdingName": "Resetas LLC",
          "numFactories": 378,
          "numHouses": 5,
          "activeHouses": ["q1", "q2", "q3", "q4", "q5"],
          "roleId": 1,
          "roleName": "Resident",
          "avatar": "https://cdnt.erepublik.net/.../avatar.jpg",
          "holdingAvatar": null
        }
      ],
      "numResults": 274,
      "searchParam": "search",
      "pageParam": "currentPage"
    }
  }
}
```

### Response Fields

#### citizenInfo Object
Same structure as in "Get City Overview Data" endpoint - contains current user's residence status and bonuses.

#### cityInfo Object
Same structure as in "Get City Overview Data" endpoint - contains basic city information and statistics.

#### widgets.residents Object
- `residents` - Array of resident citizen objects (see below)
- `numResults` - Total number of residents in this city (for pagination)
- `searchParam` - Parameter name for search functionality
- `pageParam` - Parameter name for pagination

#### Resident Object (in residents array)
- `citizenId` - Unique citizen ID
- `citizenName` - Display name of the citizen
- `hasAvatar` - Whether citizen has a custom avatar (1 = yes, 0 = no)
- `residentSince` - Game day when residence was established (e.g., "Day 4,016")
- `isMayor` - Whether this citizen is the city's mayor (1 = yes, 0 = no)
- `isCouncilMember` - Whether citizen is on city council (1 = yes, 0 = no)
- `isRegisteredVoter` - Whether citizen is registered to vote in this city (1 = yes, 0 = no)
- `countryId` / `countryName` / `countryPermalink` - Citizen's citizenship country
- `hasHolding` - Whether citizen has a holding company (1 = yes, 0 = no)
- `holdingName` - Name of the citizen's holding company (if any)
- `numFactories` - Number of factories owned in this city
- `numHouses` - Number of houses owned in this city
- `activeHouses` - Array of house quality levels owned (e.g., ["q1", "q2", "q3"])
- `roleId` / `roleName` - Role in the city (1 = "Resident", possibly other values for mayor/council)
- `avatar` - URL to citizen's avatar image
- `holdingAvatar` - URL to holding company avatar (if applicable)

### Notes

- Response includes the first page of residents (default pagination, typically 10-20 citizens per page)
- Total resident count available in `numResults` field (useful for pagination UI)
- Residents can have different citizenship than the city's country (foreign residents)
- `residentSince` uses in-game day format, not real-world dates
- Citizens with holdings typically have significant economic presence in the city
- `activeHouses` quality levels: q1 (basic) through q5 (luxury)
- Government roles (mayor, council member) are highlighted with boolean flags
- This endpoint likely supports query parameters for pagination and search (e.g., `?currentPage=2&search=username`), though not shown in this example
- The `numFactories` count includes all company buildings owned by the citizen in this city
- Large cities with many residents will require pagination to view all residents

---

## Get City Properties

**Method:** GET
**URL:** `/en/main/city-data/{cityId}/properties`
**Auth Required:** Yes

### Description

Retrieves information about the logged-in citizen's properties in a specific city, including houses owned (with quality levels, energy bonuses, activation status, and costs) and holding company details (factories owned and their types). This endpoint provides the data for the city properties management page.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| cityId | number | Yes | The ID of the city to retrieve properties for (path parameter) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/city-data/710/properties' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Example Response

```json
{
  "citizenInfo": {
    "isLoggedIn": 1,
    "residenceCityId": 0,
    "isResident": 1,
    "isLocatedInResidence": 1,
    "residentialBonuses": {
      "hasResidence": 1,
      "isInResidence": 1,
      "validUntil": 0,
      "timeRemaining": 0,
      "numHouses": 1,
      "energyBonus": 50,
      "recoveryBonus": 2,
      "description": "Residence bonuses available"
    }
  },
  "cityInfo": {
    "id": 710,
    "name": "Klaipeda",
    "permalink": "Klaipeda",
    "regionId": 663,
    "cityTypeId": 2,
    "regionName": "Lithuania Minor",
    "countryId": 72,
    "countryName": "Lithuania",
    "avatar": "//www.erepublik.net/images/modules/cities/128px/Klaipeda.png",
    "citySize": "Large Town",
    "numResidents": 54,
    "buildingTax": 2,
    "buildingBonus": 10,
    "cityBudget": 20852463,
    "grossDomesticProduct": 2230462
  },
  "panelId": "properties",
  "panelUrl": "//www.erepublik.com/en/city/710/properties",
  "widgets": {
    "searchCity": {
      "actionUrl": "//www.erepublik.com/en/main/city-search",
      "action": "searchCity",
      "widgetId": "searchCity"
    },
    "houses": {
      "list": [
        {
          "isDefault": 1,
          "energyBonus": 100,
          "energyBooster": 2,
          "effectsActive": 1,
          "name": "Central Park"
        },
        {
          "quality": 1,
          "energyBonus": 100,
          "energyBooster": 2,
          "overtimePoints": 1,
          "effectsActive": 1,
          "activeTimeRemaining": 699368,
          "inventory": 26,
          "marketUrl": "//www.erepublik.com/en/economy/marketplace#72/4/1",
          "buttonText": "Extend",
          "activationMessage": "Extension successful",
          "activationData": {
            "params": {
              "action": "activate",
              "quality": 1,
              "cityId": 710,
              "cityWidget": "L2VuL21haW4vY2l0eS1kYXRhLzcxMC9wcm9wZXJ0aWVzL2hvdXNlcw=="
            },
            "url": "/en/economy/activateHouse"
          },
          "confirmationMessage": "The city of Klaipeda charges 628.9 LTL for this action. Continue?"
        },
        {
          "quality": 2,
          "energyBonus": 160,
          "energyBooster": 2,
          "overtimePoints": 1,
          "effectsActive": 0,
          "activeTimeRemaining": 0,
          "inventory": 0,
          "marketUrl": "//www.erepublik.com/en/economy/marketplace#72/4/2",
          "buttonText": "Buy",
          "activationMessage": ""
        }
      ],
      "currency": 6274326,
      "widgetUrl": "/en/main/city-data/710/properties/houses",
      "widgetId": "houses"
    },
    "holdingCompany": {
      "list": {
        "ownerId": 4690052,
        "ownerEntityType": "citizen",
        "regionId": 663,
        "name": "Hail Lithuania",
        "createdAt": 1675322000,
        "numFactories": 224,
        "avatar": "https://www.erepublik.net/images/default_avatars/holding_100x100.png",
        "buttonText": "Manage",
        "buttonUrl": "//www.erepublik.com/en/economy/myCompanies"
      },
      "factoriesList": [
        {
          "industryId": 2,
          "quality": 7,
          "name": "Weapons Q7",
          "isRaw": 0,
          "numFactories": 24,
          "maxEmployees": 240,
          "icon": "//www.erepublik.net/images/modules/manager/factory_buildings/2_7.png"
        },
        {
          "industryId": 16,
          "quality": 1,
          "name": "Rubber",
          "isRaw": 1,
          "numFactories": 200,
          "maxEmployees": 800,
          "icon": "//www.erepublik.net/images/modules/manager/factory_buildings/16.png"
        }
      ],
      "widgetUrl": "/en/main/city-data/710/properties/holdingCompany",
      "widgetId": "holdingCompany"
    }
  },
  "hasPropertiesInCity": 1
}
```

### Response Fields

#### citizenInfo Object
Same structure as in "Get City Overview Data" endpoint - contains current user's residence status and bonuses.

#### cityInfo Object
Same structure as in "Get City Overview Data" endpoint - contains basic city information and statistics.

#### hasPropertiesInCity
- Boolean (1 = yes, 0 = no) indicating whether the citizen has any properties in this city

#### widgets.houses Object
- `list` - Array of house objects (see below)
- `currency` - Citizen's balance in the city's local currency
- `widgetUrl` / `widgetId` - Widget configuration

#### House Object (in houses.list array)
**Default/Central Park (free energy source):**
- `isDefault` - Indicates this is the default Central Park (1 = yes)
- `name` - Name of the free energy source (e.g., "Central Park")
- `energyBonus` - Energy pool bonus provided
- `energyBooster` - Energy recovery per 6 minutes
- `effectsActive` - Whether the bonus is currently active (1 = yes, 0 = no)

**Regular Houses (Q1-Q5):**
- `quality` - House quality level (1-5)
- `energyBonus` - Energy pool bonus (100 for Q1, up to 400 for Q5)
- `energyBooster` - Energy recovery per 6 minutes (typically 2)
- `overtimePoints` - Overtime points granted (typically 1)
- `effectsActive` - Whether house is currently active (1 = yes, 0 = no)
- `activeTimeRemaining` - Seconds until house expires (if active)
- `inventory` - Number of houses in inventory
- `marketUrl` - URL to marketplace for buying more houses
- `buttonText` - Action button label ("Extend", "Buy", "Use")
- `activationMessage` - Message shown after activation
- `activationData` - Object containing activation endpoint and parameters:
  - `params.action` - Action type (e.g., "activate")
  - `params.quality` - House quality being activated
  - `params.cityId` - City ID
  - `params.cityWidget` - Base64-encoded widget URL
  - `url` - Activation endpoint (`/en/economy/activateHouse`)
- `confirmationMessage` - Message shown before activation (includes cost)

#### widgets.holdingCompany Object
- `list` - Holding company information object:
  - `ownerId` - Citizen ID of the holding owner
  - `ownerEntityType` - Entity type (typically "citizen")
  - `regionId` - Region where holding is based
  - `name` - Holding company name
  - `createdAt` - Unix timestamp of creation
  - `numFactories` - Total number of factories in this city
  - `avatar` - URL to holding company logo
  - `buttonText` / `buttonUrl` - Management button configuration
- `factoriesList` - Array of factory type objects:
  - `industryId` - Industry type ID
  - `quality` - Factory quality level (1-7)
  - `name` - Factory type name (e.g., "Weapons Q7", "Rubber")
  - `isRaw` - Whether this is a raw material factory (1 = yes, 0 = no)
  - `numFactories` - Number of factories of this type
  - `maxEmployees` - Maximum employees across all factories of this type
  - `icon` - URL to factory icon image

### Notes

- This endpoint returns **only the logged-in citizen's properties** in the specified city
- Central Park is always available in every city (free default energy source)
- House quality levels (Q1-Q5) provide increasing energy bonuses:
  - Q1: +100 energy pool
  - Q2: +160 energy pool
  - Q3: +200 energy pool
  - Q4: +300 energy pool
  - Q5: +400 energy pool
- Houses must be activated to provide bonuses, with activation costs paid in local currency
- `activeTimeRemaining` shows seconds until house expires (typically 30 days duration)
- Only one house quality can be active at a time in a city
- `inventory` count shows how many houses of that quality the citizen owns
- Button text indicates available action: "Use" (from inventory), "Buy" (need to purchase), "Extend" (renew active house)
- Holding company data shows factory distribution in the city
- `isRaw` factories produce raw materials (oil, rubber, iron, etc.), others produce finished goods
- `maxEmployees` = `numFactories` × workers per factory (varies by industry and quality)
- The `activationData.params.cityWidget` is a base64-encoded callback URL for refreshing the widget after activation
- Activation costs include city's residential tax

---

## Endpoint Documentation Template

Use this template when adding new endpoints:

```markdown
## Endpoint Name

**Method:** GET/POST
**URL:** `/en/path/to/endpoint`
**Auth Required:** Yes/No

### Description
### Parameters (table)
### Headers (table)
### Example Request (cURL)
### Example Response
### Notes
```

---

**Tags:** #erepublik #api #world #travel #regions #countries #geography
