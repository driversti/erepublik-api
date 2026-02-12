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

## Get Travel Data (Filter by Country)

**Method:** POST
**URL:** `/en/main/travelData`
**Auth Required:** Yes

### Description

Retrieves travel data filtered by a specific country, showing all regions belonging to or occupied by that country. This variant uses the `check=getCountryRegions` parameter to filter results by `countryId`, returning a subset of regions instead of the full world map. Useful for building country-specific travel interfaces or displaying regions within a particular nation.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| check | string | Yes | Action type, must be `getCountryRegions` for this variant |
| countryId | number | Yes | Country ID to filter regions by |
| _token | string | Yes | CSRF token for the request |
| holdingId | number | No | Holding company ID (use 0 if not applicable) |
| regionId | number | No | Current/reference region ID (use 0 if not applicable) |

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
  --data-raw 'check=getCountryRegions&countryId=72&_token=YOUR_CSRF_TOKEN&holdingId=0&regionId=0'
```

### Example Response

```json
{
  "countries": {
    "49": {
      "id": 49,
      "name": "Indonesia",
      "permalink": "Indonesia",
      "hasRegionsFromOthers": true,
      "hasRegionsOccupiedByOthers": false,
      "regions": [667]
    },
    "72": {
      "id": 72,
      "name": "Lithuania",
      "permalink": "Lithuania",
      "hasRegionsFromOthers": true,
      "hasRegionsOccupiedByOthers": true,
      "regions": [667, 664, 665, 478, 387, 372, 64, 43, 763, 120, 692, 333, 666]
    },
    "56": {
      "id": 56,
      "name": "Iran",
      "permalink": "Iran",
      "hasRegionsFromOthers": false,
      "hasRegionsOccupiedByOthers": true,
      "regions": [478]
    }
  },
  "regions": {
    "667": {
      "id": 667,
      "name": "Sudovia",
      "permalink": "Sudovia",
      "zoneName": "A3",
      "distanceInZones": 1,
      "distanceInKm": 190,
      "isConquered": true,
      "isConqueredFrom": 72,
      "isConqueredBy": 49,
      "countryId": 49,
      "cityId": 714,
      "cityName": "Marijampole",
      "cost": 20,
      "ticket": 5,
      "ticketAmount": 1,
      "canMove": true,
      "isAlly": false,
      "isDiscounted": false,
      "isFree": false
    },
    "664": {
      "id": 664,
      "name": "Samogitia",
      "permalink": "Samogitia",
      "zoneName": "A3",
      "distanceInZones": 1,
      "distanceInKm": 77,
      "isConquered": false,
      "isConqueredFrom": 0,
      "isConqueredBy": 0,
      "countryId": 72,
      "cityId": 711,
      "cityName": "Telsiai",
      "cost": 20,
      "ticket": 5,
      "ticketAmount": 1,
      "canMove": true,
      "isAlly": false,
      "isDiscounted": false,
      "isFree": false
    }
  },
  "preselectCountryId": 72,
  "preselectRegionId": false,
  "alreadyInRegion": "",
  "battleInfo": {
    "battleInfoText": "",
    "travelRequired": false,
    "travelData": false
  },
  "citizen": {
    "country": {
      "id": 72,
      "name": "Lithuania",
      "permalink": "Lithuania"
    },
    "region": {
      "id": 663,
      "name": "Lithuania Minor",
      "permalink": "Lithuania-Minor"
    },
    "city": {
      "id": 710,
      "name": "Klaipeda"
    },
    "residence": {
      "hasResidence": 1,
      "cityId": 710,
      "regionId": 663,
      "countryId": 72,
      "bonuses": {
        "hasResidence": 1,
        "isInResidence": 1,
        "validUntil": 0,
        "timeRemaining": 0,
        "numHouses": 1,
        "energyBonus": 100,
        "recoveryBonus": 4,
        "description": "Residence bonuses available"
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
    },
    "travelMethod": "preferCurrency"
  }
}
```

### Response Fields

#### Top Level (Filtered Response)
- `countries` - Object mapping country IDs to country data (only countries related to the specified `countryId`)
- `regions` - Object mapping region IDs to region data (only regions owned/occupied by the specified country)
- `preselectCountryId` - The requested country ID (echoed back)
- `preselectRegionId` - Region to preselect (false if not specified)
- `alreadyInRegion` - Empty string or region ID if citizen is already in target
- `battleInfo` - Battle-related travel requirements (if applicable)
- `citizen` - Current citizen information including country, region, city, and residence

#### Country Object (Filtered View)
- `hasRegionsFromOthers` - Whether this country has conquered regions from other nations
- `hasRegionsOccupiedByOthers` - Whether this country has regions occupied by other nations
- `regions` - Array of region IDs belonging to (or occupied by) this country

#### Region Object
Same structure as "Get Travel Data" endpoint - includes distance, cost, conquest status, etc.

#### Citizen Object
- `country` - Citizen's citizenship country (id, name, permalink)
- `region` - Current region location
- `city` - Current city location
- `residence` - Residence details including bonuses and city info
- `travelMethod` - Preferred payment method for travel ("preferCurrency" or "preferTicket")

### Notes

- This variant filters results to show only regions related to the specified `countryId`
- Returns regions **owned by** the country (original regions)
- Returns regions **occupied by** the country (conquered from others)
- Returns regions **occupied from** the country (original regions now held by enemies)
- Useful for building country-specific travel dropdowns or region selectors
- Response is significantly smaller than the full world map variant (fewer countries/regions)
- The `check=getCountryRegions` parameter is required to trigger this filtering behavior
- `preselectRegionId` is `false` instead of a number when no specific region is targeted
- All travel costs, distances, and conquest information are still included per region
- Example shows Lithuania (country 72) with 13 regions including occupied territories across multiple continents

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
          "activeTimeRemaining": 486880,
          "inventory": 24,
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
          "confirmationMessage": "The city of Klaipeda charges 681.11 LTL for this action. Continue?"
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
        },
        {
          "quality": 3,
          "energyBonus": 200,
          "energyBooster": 2,
          "overtimePoints": 1,
          "effectsActive": 0,
          "activeTimeRemaining": 0,
          "inventory": 0,
          "marketUrl": "//www.erepublik.com/en/economy/marketplace#72/4/3",
          "buttonText": "Buy",
          "activationMessage": ""
        },
        {
          "quality": 4,
          "energyBonus": 300,
          "energyBooster": 2,
          "overtimePoints": 1,
          "effectsActive": 0,
          "activeTimeRemaining": 0,
          "inventory": 0,
          "marketUrl": "//www.erepublik.com/en/economy/marketplace#72/4/4",
          "buttonText": "Buy",
          "activationMessage": ""
        },
        {
          "quality": 5,
          "energyBonus": 400,
          "energyBooster": 2,
          "overtimePoints": 1,
          "effectsActive": 0,
          "activeTimeRemaining": 0,
          "inventory": 0,
          "marketUrl": "//www.erepublik.com/en/economy/marketplace#72/4/5",
          "buttonText": "Use",
          "activationMessage": "You now have a house in Klaipeda",
          "activationData": {
            "params": {
              "action": "activate",
              "quality": 5,
              "cityId": 710,
              "cityWidget": "L2VuL21haW4vY2l0eS1kYXRhLzcxMC9wcm9wZXJ0aWVzL2hvdXNlcw=="
            },
            "url": "/en/economy/activateHouse"
          },
          "confirmationMessage": "The city of Klaipeda charges 0 LTL for this action. Continue?"
        }
      ],
      "currency": 7120147,
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
- `activeTimeRemaining` shows seconds until house expires (typically 7 days duration)
- Only one house quality can be active at a time in a city
- `inventory` count shows how many houses of that quality the citizen owns
- The list always includes all 5 quality levels plus Central Park, regardless of ownership
- Button text indicates available action: "Use" (activate from inventory or reward), "Buy" (need to purchase from marketplace), "Extend" (renew currently active house)
- Houses with `buttonText: "Use"` at 0 cost may come from weekly challenges or other reward sources
- Holding company data shows factory distribution in the city
- `isRaw` factories produce raw materials (oil, rubber, iron, etc.), others produce finished goods
- `maxEmployees` = `numFactories` × workers per factory (varies by industry and quality)
- The `activationData.params.cityWidget` is a base64-encoded callback URL for refreshing the widget after activation
- Activation costs include city's residential tax

---

## Search Cities

**Method:** GET
**URL:** `/en/main/city-search?search={query}`
**Auth Required:** No

### Description

Searches for cities by name, region name, or country name. This is the autocomplete/search endpoint used by the city search widget on city pages. It performs a **case-insensitive substring match** across city name, region name, and country name fields.

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| search | string | Yes | Search query (minimum 2 characters) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| X-Requested-With | `XMLHttpRequest` | Yes |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/city-search?search=War' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Example Response

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
    },
    {
      "id": 500,
      "name": "Bhubaneswar",
      "permalink": "Bhubaneswar",
      "regionName": "Orissa",
      "countryName": "India",
      "avatar": "//www.erepublik.net/images/modules/cities/128px/Bhubaneswar.png",
      "url": "//www.erepublik.com/en/main/city/Bhubaneswar"
    },
    {
      "id": 537,
      "name": "Peshawar",
      "permalink": "Peshawar",
      "regionName": "North-West Frontier Province",
      "countryName": "Pakistan",
      "avatar": "//www.erepublik.net/images/modules/cities/128px/Peshawar.png",
      "url": "//www.erepublik.com/en/main/city/Peshawar"
    }
  ],
  "error": false
}
```

### Example Response (No Results)

```json
{
  "status": true,
  "data": [],
  "error": false
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| status | boolean | Always `true` for valid requests |
| data | array | Array of matching city objects |
| error | boolean | Always `false` for valid requests |

#### City Object (in data array)

| Field | Type | Description |
|-------|------|-------------|
| id | number | City ID |
| name | string | City display name |
| permalink | string | URL-friendly city name |
| regionName | string | Region containing the city |
| countryName | string | Country currently controlling the region |
| avatar | string | URL to city image/icon |
| url | string | URL to the city page |

### Notes

- **No authentication required** — works without session cookie
- **Minimum 2 characters** — queries with 0 or 1 character return an empty `data` array
- **Case-insensitive** — "bu" and "Bu" return the same results
- **Substring matching** — matches anywhere in city name, region name, or country name (e.g., "bu" matches "Bucharest", "Hamburg", "Istanbul", "Burgundy")
- **No pagination** — all matching results are returned in a single response
- Search for a country name (e.g., "Lithuania") returns all cities in that country — useful for listing a country's cities
- The `countryName` reflects the **current controller** of the region, not the original owner (e.g., Budapest may show "Bulgaria" as country if Bulgaria conquered Central Hungary)

---

## City Overview Widget Sub-endpoints

The city overview endpoint returns widget data inline, but each widget also has its own lazy-loading URL that returns just the widget portion. These are useful for refreshing individual widgets without reloading the full overview.

### Get Residence Widget

**Method:** GET
**URL:** `/en/main/city-data/{cityId}/overview/residence`
**Auth Required:** Yes

Returns just the `widgets.residence` object from the [City Overview](#get-city-overview-data) response.

```bash
curl 'https://www.erepublik.com/en/main/city-data/710/overview/residence' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

```json
{
  "isResident": 1,
  "canEstablishResidence": 1,
  "citizenResidenceCityId": 0,
  "establishResidenceCost": {
    "residentialTax": 200,
    "propertiesTransfer": {
      "houses": { "1": 498.67 },
      "total": 498.67
    },
    "total": 698.67
  },
  "establishResidenceConfirmation": "Are you sure you want to pay 698.67 LTL in local taxes and establish your residence in Klaipeda?",
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
}
```

### Get Government Widget

**Method:** GET
**URL:** `/en/main/city-data/{cityId}/overview/government`
**Auth Required:** Yes

Returns just the `widgets.government` object from the [City Overview](#get-city-overview-data) response.

```bash
curl 'https://www.erepublik.com/en/main/city-data/710/overview/government' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

```json
{
  "mayor": [],
  "numCityCouncilors": "-",
  "widgetUrl": "//www.erepublik.com/en/main/city-data/710/overview/government",
  "widgetId": "government"
}
```

### Notes

- These are the same data objects returned inline within the full overview response
- Useful for refreshing a single widget without re-fetching the entire city overview
- The `widgetUrl` in each widget's response points back to itself (self-referential)
- The properties panel has similar sub-endpoints: `/en/main/city-data/{cityId}/properties/houses` and `/en/main/city-data/{cityId}/properties/holdingCompany`

---

## Establish Residence (City Actions)

**Method:** POST
**URL:** `/en/main/city-actions`
**Auth Required:** Yes

### Description

Performs city-related actions such as establishing residence. This is the action endpoint referenced by the residence widget's `actionUrl`. Establishing residence in a city costs local currency (residential tax + property transfer fees).

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| action | string | Yes | Action type (e.g., `establishResidence`) |
| cityId | number | Yes | Target city ID |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/city-actions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'action=establishResidence&cityId=710&_token=YOUR_CSRF_TOKEN'
```

### Example Response (Error - Invalid City)

```json
{
  "status": false,
  "message": "Invalid City",
  "error": true
}
```

### Example Response (Error - CSRF)

```
"CSRF attack detected."
```

### Notes

- **Modifying action** — this endpoint changes game state (establishes residence, costs currency)
- Requires valid CSRF token obtained from the page context
- The total cost is shown in the residence widget's `establishResidenceCost.total` field
- Without `_token`, returns "CSRF attack detected" error
- Without valid `cityId`, returns "Invalid City" error
- Cost breakdown: residential tax (fixed per city) + property transfer fees (depends on houses owned)
- The residence widget's `establishResidenceConfirmation` field provides a pre-formatted confirmation message

---

## Get World Map Data

**Method:** GET
**URL:** `/en/main/map-data?updated_at={timestamp}`
**Auth Required:** Yes

### Description

Retrieves world map data for all regions (574 total), including current/original country ownership, natural resources, city information, geographic coordinates (bounding boxes, centroids), and active/past battle information. Supports incremental updates via the `updated_at` timestamp parameter, returning only regions modified since that time.

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| updated_at | string | Yes | ISO 8601 timestamp (URL-encoded). Returns regions updated since this time. Example: `2026-02-01T23%3A22%3A47-08%3A00` |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/javascript, */*; q=0.01` | Recommended |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/map-data?updated_at=2026-02-01T23%3A22%3A47-08%3A00' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01'
```

### Example Response

```json
{
  "1": {
    "resources": [
      {"id": 11, "name": "Fish", "mtype": "food", "rarity": "common", "bonus": 15},
      {"id": 1, "name": "Grain", "mtype": "food", "rarity": "very common", "bonus": 10}
    ],
    "original_country": {"id": 1, "name": "Romania", "code": "ro"},
    "current_country": {"id": 1, "name": "Romania", "code": "ro"},
    "city": {
      "id": 36,
      "name": "Constanta",
      "permalink": "Constanta",
      "population": 20,
      "lnglat": [28.652, 44.173]
    },
    "region": {
      "id": 1,
      "name": "Dobrogea",
      "permalink": "Dobrogea",
      "bbox": [27.45, 43.68, 29.7, 45.22],
      "center": [28.575, 44.45]
    }
  },
  "2": {
    "resources": [
      {"id": 3, "name": "Cattle", "mtype": "food", "rarity": "uncommon", "bonus": 20}
    ],
    "original_country": {"id": 1, "name": "Romania", "code": "ro"},
    "current_country": {"id": 1, "name": "Romania", "code": "ro"},
    "city": {
      "id": 35,
      "name": "Bucharest",
      "permalink": "Bucharest",
      "population": 228,
      "lnglat": [26.103, 44.426]
    },
    "region": {
      "id": 2,
      "name": "Muntenia",
      "permalink": "Muntenia",
      "bbox": [24.45, 43.66, 27.45, 45.0],
      "center": [25.95, 44.33]
    },
    "active_battle_info": {
      "battle_id": 561895,
      "region_permalink": "Muntenia",
      "attacker_id": 45,
      "attacker_code": "ua",
      "defender_id": 1,
      "defender_code": "ro",
      "type": "resistance"
    }
  }
}
```

### Response Structure

- Response is an object keyed by region ID (string)
- Each region contains: `resources`, `original_country`, `current_country`, `city`, `region`
- Optional fields per region: `active_battle_info`, `past_battle_info`

### Response Fields (per region)

#### resources (array)

| Field | Type | Description |
|-------|------|-------------|
| id | number | Resource ID |
| name | string | Resource display name (e.g., "Fish", "Iron", "Oil") |
| mtype | string | Material type: `food` or `weapon` |
| rarity | string | Rarity level: `very common`, `common`, `uncommon`, `rare` |
| bonus | number | Production bonus percentage (10-25%) |

#### original_country / current_country (object)

| Field | Type | Description |
|-------|------|-------------|
| id | number | Country ID |
| name | string | Country name |
| code | string | Two-letter country code (lowercase) |

#### city (object)

| Field | Type | Description |
|-------|------|-------------|
| id | number | City ID |
| name | string | City name |
| permalink | string | URL-friendly city name |
| population | number | Number of residents in the city |
| lnglat | array | Geographic coordinates `[longitude, latitude]` |

#### region (object)

| Field | Type | Description |
|-------|------|-------------|
| id | number | Region ID |
| name | string | Region name |
| permalink | string | URL-friendly region name |
| bbox | array | Bounding box `[minLng, minLat, maxLng, maxLat]` |
| center | array | Centroid coordinates `[longitude, latitude]` |

#### active_battle_info (object, optional)

| Field | Type | Description |
|-------|------|-------------|
| battle_id | number | Battle ID |
| region_permalink | string | URL-friendly region name |
| attacker_id | number | Attacking country ID |
| attacker_code | string | Attacker two-letter country code |
| defender_id | number | Defending country ID |
| defender_code | string | Defender two-letter country code |
| type | string | Battle type: `resistance`, `direct`, `airstrike` |

#### past_battle_info (object, optional)

Same structure as `active_battle_info`. Present when a battle recently ended in the region.

### Notes

- **Incremental Updates:** Use the `updated_at` parameter to fetch only regions that changed since the last request. This is essential for real-time map tracking applications to minimize bandwidth.
- **Response Size:** Full response is approximately 620KB (574 regions). Using incremental updates significantly reduces payload size.
- **Geographic Data:** The `bbox` and `center` coordinates are suitable for map rendering libraries (Leaflet, Mapbox, etc.).
- **Battle Tracking:** Regions with active battles include `active_battle_info`. Recently concluded battles may show in `past_battle_info`.
- **Occupation Status:** Compare `original_country` vs `current_country` to determine if a region is occupied.
- **Resources:** Each region can have multiple natural resources providing production bonuses.
- **Timestamp Format:** The `updated_at` parameter must be URL-encoded. Example: `2026-02-01T23:22:47-08:00` becomes `2026-02-01T23%3A22%3A47-08%3A00`.

---

## Get Region Page

**Method:** GET
**URL:** `/en/main/region/{RegionPermalink}`
**Auth Required:** No

### Description

Retrieves a **server-rendered HTML page** containing comprehensive information about a specific region. Unlike most API endpoints that return JSON, this page embeds structured data within HTML elements (`data-` attributes, CSS classes) and inline JavaScript variables. The page does not require authentication and can be accessed with a simple GET request.

The region permalink is the URL-friendly name of the region (e.g., `Lithuania-Minor`, `Rhineland-Westphalia`).

### URL Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| RegionPermalink | string | Yes | URL-friendly region name (e.g., `Lithuania-Minor`) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

### Example Request

```bash
curl -s 'https://www.erepublik.com/en/main/region/Lithuania-Minor'
```

### Response Format

The response is an HTML page (~38KB). Data is embedded in various HTML structures and JavaScript variables described below.

### Embedded Data Sections

#### 1. Country Info

Found in the page header area and country dropdown.

| Selector / Source | Data |
|-------------------|------|
| `#countrySelectorDropdown option[selected]` | Country ID (`value`), permalink (`data-permalink`) |
| `a.country_rank` | Country rank (e.g., "Rank 39") |
| `a.alliance_name` | Alliance name and link (e.g., `/en/main/alliances/6` for "WOLF"). Only present if the country belongs to an alliance. |
| `span.dictatorship_flag_icon_l` | Dictatorship indicator icon. Present before the country flag image only when the country is under a dictatorship government form. Absent for democracies. |
| Country flag image | Flag URL from `<img>` element near country name |

Example HTML (democracy with alliance):
```html
<option value="48" data-permalink="India" selected>India</option>
<a class="alliance_name" href="//www.erepublik.com/en/main/alliances/6" title="World Organization of Loyalty and Freedom"><span>WOLF</span></a>
```

Example HTML (dictatorship without alliance):
```html
<span class="dictatorship_flag_icon_l"></span>
<img src="//www.erepublik.net/images/flags/XL/Venezuela.gif" />
```

#### 2. City & Population

Found in `div.holder.city` elements with ID pattern `cityId_{id}`.

| Selector | Data |
|----------|------|
| `div#cityId_{id}` | Container for city data; ID is the city numeric ID |
| `a.cityLink` | City page URL and name (e.g., `/en/main/city/Klaipeda`) |
| `img.cityEmblem` | City emblem image URL |
| `h2.cityName small` | City role (e.g., "Capital City of Lithuania Minor") |
| `span.cityResidents` | Number of residents (e.g., "274 residents") |
| `span.cityVisitors` | Number of visitors (e.g., "54 visitors") |
| `span.cityVoters small` | Voter registration info. Appears alongside both residents and visitors. Common values: "No registered voters", "No residents with the citizenship of this country". |

Example HTML:
```html
<div class="holder city" id="cityId_714">
    <h2 class="section">Population</h2>
    <a href="/en/main/city/Marijampole" class="cityLink">
        <img src="//www.erepublik.net/images/modules/cities/128px/Marijampole.png"
             class="cityEmblem goleft" alt="City Emblem" />
    </a>
    <h2 class="largepadded special cityName">
        <a class="cityLink" href="/en/main/city/Marijampole">Marijampole</a>
        <br>
        <small>(Capital City of Sudovia)</small>
    </h2>
    <p class="special">
        <span class="cityResidents">226 residents</span>
        <span class="cityVoters">
            <small>(No registered voters)</small>
        </span>
    </p>
    <p class="special">
        <span class="cityVisitors">15 visitors</span>
        <span class="cityVoters">
            <small>(No residents with the citizenship of this country)</small>
        </span>
    </p>
</div>
```

#### 3. Constructions (Building Slots)

Found in `.construction_listing` divs. Each region has **15 building slots**.

| Selector / Attribute | Data |
|----------------------|------|
| `.construction_listing strong` | Slot number (1–15) |
| `span.product_tooltip` | Building in slot |
| `span[industry]` | Industry type ID |
| `span[quality]` | Building quality level |
| `span[id]` | Pattern: `building_{slot}{quality}` |

Industry type IDs:

| ID | Industry |
|----|----------|
| 1 | Food |
| 2 | Weapons |
| 4 | House |
| 6 | Defense Systems |
| 23 | Aircraft Weapons |

Example HTML (Defense System in slot 3):
```html
<span id="building_31" class="product_tooltip" industry="6" quality="1">
    <img src="https://www.erepublik.net/images/icons/industry/6/q1.png" />
</span>
```

#### 4. Determination

Found in inline `<script>` blocks as JavaScript arrays.

| Variable | Description |
|----------|-------------|
| `regionDeterminations` | Array of active resistance war determinations |
| `defensiveDeterminations` | Array of active defensive determinations |

Both arrays are empty (`[]`) when the region has not been recently conquered.

Each determination object has the following fields:

| Field | Type | Description |
|-------|------|-------------|
| countryId | number | Country ID |
| countryName | string | Country display name |
| permalink | string | URL-friendly country name |
| determination | number | Influence multiplier (e.g., `3.68` means x3.68 influence). **Note:** Values may have floating-point precision artifacts (e.g., `3.8300000000000001` instead of `3.83`) — round to 2 decimal places when displaying. |
| active | boolean | `true` if this country is the current occupier |

Additional determination text can appear in the HTML:
- "This region has not been conquered in the past 250 days."
- "Liberated: 1023 days ago"
- "This region has no determination when attacked."

The HTML section uses CSS class `hasDetermination` or `noDetermination` on `.indent.productivity.regionDetermination` to indicate state. Active occupier items have the CSS class `active` on `.item`.

Example (empty — no recent conquests):
```javascript
var regionDeterminations = [];
var defensiveDeterminations = [];
```

Example (populated — Sudovia occupied by Indonesia):
```javascript
var regionDeterminations = [
    {
        "countryId": 49,
        "countryName": "Indonesia",
        "permalink": "Indonesia",
        "determination": 3.68,
        "active": true
    },
    {
        "countryId": 166,
        "countryName": "United Arab Emirates",
        "permalink": "United-Arab-Emirates",
        "determination": 3.68,
        "active": false
    }
];
var defensiveDeterminations = [];
```

Example (liberated region — determinations against multiple past occupiers, none currently active):
```javascript
var regionDeterminations = [
    {
        "countryId": 24,
        "countryName": "USA",
        "permalink": "USA",
        "determination": 1.49,
        "active": false
    },
    {
        "countryId": 53,
        "countryName": "Portugal",
        "permalink": "Portugal",
        "determination": 1.22,
        "active": false
    }
];
var defensiveDeterminations = [];
```

> **Note:** A region can have determination entries against multiple countries even when none of them is the current occupier (`active: false` for all). This occurs when a region has been liberated but retains stored determination from past conquests. Different countries may have different determination multipliers based on how long/often they occupied the region.

#### 5. Productivity (Country Bonuses & Region Resources)

**Country bonuses** are found in `.indent.productivity .area.bonus .item`:

| Attribute | Data |
|-----------|------|
| `data-industryId` | Industry type ID |
| `data-bonus` | Bonus percentage (can be decimal, e.g., `22.5`) |
| CSS class `full-bonus` / `partial-bonus` / `noBonus` | Bonus level: `full-bonus` = 100%, `partial-bonus` = 1–99%, `noBonus` = 0% (text shows "No bonus") |

Example HTML (Lithuania Minor — has bonuses in all industries):
```html
<p data-industryId="1" data-bonus="100" class="amount full-bonus">+100%</p>
<p data-industryId="2" data-bonus="100" class="amount full-bonus">+100%</p>
<p data-industryId="4" data-bonus="5" class="amount partial-bonus">+5%</p>
<p data-industryId="23" data-bonus="25" class="amount partial-bonus">+25%</p>
```

Example HTML (Sudovia — some industries have no bonus):
```html
<p data-industryId="1" data-bonus="100" class="amount full-bonus">+100%</p>
<p data-industryId="2" data-bonus="0" class="amount noBonus">No bonus</p>
<p data-industryId="4" data-bonus="0" class="amount noBonus">No bonus</p>
<p data-industryId="23" data-bonus="85" class="amount partial-bonus">+85%</p>
```

**Region resources** are found in `.area.resource .item`:

| Attribute | Data |
|-----------|------|
| `data-resourceId` | Resource ID |
| `data-bonus` | Bonus percentage from this resource |
| CSS class `type-{industry}` | Which industry the resource benefits |
| CSS class `rarity-{level}` | Resource rarity (see table below) |
| `p.name` | Resource name (e.g., "Grain", "Iron", "Sand", "Neodymium") |
| `div.icon.{name}` | Resource icon element (e.g., `div.icon.grain`, `div.icon.iron`, `div.icon.sand`, `div.icon.neodymium`) |
| `title` attribute | Human-readable bonus description |

Known resource rarities and their bonus percentages:

| Rarity Class | Bonus | Example Resources |
|--------------|-------|-------------------|
| `rarity-verycommon` | +2% | Fish, Iron, Sand, Magnesium |
| `rarity-common` | +3% | Fruits, Aluminum, Wood, Titanium |
| `rarity-uncommon` | +4% | Cattle, Oil, Clay, Wolfram |
| `rarity-rare` | +5% | Grain, Saltpeter, Cobalt, Limestone |
| `rarity-veryrare` | +6% | Deer, Granite, Neodymium |
| `rarity-epic` | — | (not yet observed) |
| `rarity-legendary` | — | (not yet observed) |

Known resource industry types on region pages: `type-food`, `type-weapon`, `type-house`, `type-aircraft`.

Known resource IDs (from region page `data-resourceId` attributes):

| ID | Name | Industry | Rarity | Bonus |
|----|------|----------|--------|-------|
| 1 | Grain | Food | rare | +5% |
| 2 | Fish (*) | Food | very common | +2% |
| 3 | Fruits | Food | common | +3% |
| 4 | Cattle | Food | uncommon | +4% |
| 5 | Deer | Food | very rare | +6% |
| 6 | Iron | Weapon | very common | +2% |
| 7 | Saltpeter | Weapon | uncommon | +4% |
| 8 | Aluminum | Weapon | common | +3% |
| 9 | Oil | Weapon | uncommon | +4% |
| 10 | Rubber | Weapon | rare | +5% |
| 11 | Sand | House | very common | +2% |
| 12 | Clay | House | uncommon | +4% |
| 13 | Wood | House | common | +3% |
| 14 | Limestone | House | rare | +5% |
| 15 | Granite | House | very rare | +6% |
| 16 | Neodymium | Aircraft | very rare | +6% |
| 17 | Magnesium | Aircraft | very common | +2% |
| 18 | Cobalt | Aircraft | rare | +5% |
| 19 | Titanium | Aircraft | common | +3% |
| 20 | Wolfram | Aircraft | uncommon | +4% |

> (*) Fish (id=2) is inferred — not yet directly observed on a region page. It fills the only missing food ID slot and is assigned `rarity-verycommon` (+2%) to complete the one-resource-per-rarity pattern seen in all other industries.
>
> **Note on food resource ID discrepancy:** The region page and the [map-data endpoint](#get-world-map-data) use **different resource ID schemas for food**. On region pages: Grain=1, Fish=2, Fruits=3, Cattle=4, Deer=5. In map-data: Grain=1, Cattle=3, Fish=11. IDs 1 match (Grain), but other food resources have different IDs across the two endpoints. Non-food resource IDs (6–20) appear consistent between both endpoints.
>
> **Note on bonus values:** The map-data endpoint uses a different bonus scale than region pages. For example, Iron shows `bonus: 10` in map-data but `data-bonus="2"` on region pages. Both refer to the same resource (id=6). The map-data bonus appears to represent a percentage of base production, while the region page bonus represents the resource's contribution to the country's total industry bonus.

Example HTML (Donbas — food resources):
```html
<div data-resourceId="1" data-bonus="5" class="item type-food rarity-rare"
     title="+5% to Food production">
    <p class="name">Grain</p>
    <div class="icon grain" title="+5% to Food production"></div>
    <p class="amount">+5%</p>
</div>
<div data-resourceId="3" data-bonus="3" class="item type-food rarity-common"
     title="+3% to Food production">
    <p class="name">Fruits</p>
    <div class="icon fruits" title="+3% to Food production"></div>
    <p class="amount">+3%</p>
</div>
<div data-resourceId="4" data-bonus="4" class="item type-food rarity-uncommon"
     title="+4% to Food production">
    <p class="name">Cattle</p>
    <div class="icon cattle" title="+4% to Food production"></div>
    <p class="amount">+4%</p>
</div>
<div data-resourceId="5" data-bonus="6" class="item type-food rarity-veryrare"
     title="+6% to Food production">
    <p class="name">Deer</p>
    <div class="icon deer" title="+6% to Food production"></div>
    <p class="amount">+6%</p>
</div>
```

Example HTML (Lithuania Minor — weapon resources):
```html
<div data-resourceId="6" data-bonus="2" class="item type-weapon rarity-verycommon"
     title="+2% to Weapon production">
    <p class="name">Iron</p>
</div>
<div data-resourceId="8" data-bonus="3" class="item type-weapon rarity-common"
     title="+3% to Weapon production">
    <p class="name">Aluminum</p>
</div>
```

Example HTML (Mazovia — house resources):
```html
<div data-resourceId="11" data-bonus="2" class="item type-house rarity-verycommon"
     title="+2% to House production">
    <p class="name">Sand</p>
    <div class="icon sand" title="+2% to House production"></div>
    <p class="amount">+2%</p>
</div>
<div data-resourceId="15" data-bonus="6" class="item type-house rarity-veryrare"
     title="+6% to House production">
    <p class="name">Granite</p>
    <div class="icon granite" title="+6% to House production"></div>
    <p class="amount">+6%</p>
</div>
```

Example HTML (Maharashtra — aircraft resources):
```html
<div data-resourceId="16" data-bonus="6" class="item type-aircraft rarity-veryrare"
     title="+6% to Aircraft production">
    <p class="name">Neodymium</p>
    <div class="icon neodymium" title="+6% to Aircraft production"></div>
    <p class="amount">+6%</p>
</div>
<div data-resourceId="18" data-bonus="5" class="item type-aircraft rarity-rare"
     title="+5% to Aircraft production">
    <p class="name">Cobalt</p>
    <div class="icon cobalt" title="+5% to Aircraft production"></div>
    <p class="amount">+5%</p>
</div>
<div data-resourceId="19" data-bonus="3" class="item type-aircraft rarity-common"
     title="+3% to Aircraft production">
    <p class="name">Titanium</p>
    <div class="icon titanium" title="+3% to Aircraft production"></div>
    <p class="amount">+3%</p>
</div>
<div data-resourceId="20" data-bonus="4" class="item type-aircraft rarity-uncommon"
     title="+4% to Aircraft production">
    <p class="name">Wolfram</p>
    <div class="icon wolfram" title="+4% to Aircraft production"></div>
    <p class="amount">+4%</p>
</div>
```

> **Note:** Some regions have no resources at all (e.g., Sudovia). In that case, the `.area.resource .row` div is empty — only containing a `<div class="clear"></div>`.

#### 6. Pollution

Found in the `regionPollutionDetails` JavaScript variable. This is a JSON object keyed by industry ID, where each value is an array of 8 entries (indices 0–7 corresponding to raw/q1–q7).

Industry IDs:

| ID | Industry |
|----|----------|
| 1 | Food |
| 2 | Weapons |
| 4 | House |
| 23 | Aircraft Weapons |

Each entry has:

| Field | Type | Description |
|-------|------|-------------|
| has_pollution | boolean \| 0 | Whether pollution data exists; `0` means N/A for that quality |
| pollution | string | Pollution percentage (e.g., `"5.50%"`, `"0%"`, `"N/A"`) |

Example (Lithuania Minor):
```javascript
var regionPollutionDetails = {
    "1": [
        {"has_pollution": true, "pollution": "1.83%"},
        {"has_pollution": true, "pollution": "5.50%"},
        {"has_pollution": true, "pollution": "7.96%"},
        {"has_pollution": true, "pollution": "1.97%"},
        {"has_pollution": true, "pollution": "0.73%"},
        {"has_pollution": true, "pollution": "5.30%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "0.18%"}
    ],
    "2": [
        {"has_pollution": true, "pollution": "10.08%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "25.00%"}
    ],
    "4": [
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": 0, "pollution": "N/A"},
        {"has_pollution": 0, "pollution": "N/A"}
    ],
    "23": [
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": 0, "pollution": "N/A"},
        {"has_pollution": 0, "pollution": "N/A"}
    ]
};
```

The HTML also contains a `table.pollution` with CSS classes indicating pollution severity:
- `pollution-status-high` — significant pollution
- `pollution-status-none` — no pollution
- `industry-{id}` and `quality-{q}` — identify the cell

#### 7. Neighbors

Found in `table.neighbours`. Each row contains a neighboring region with its current owner country.

| Selector | Data |
|----------|------|
| Row `<a>` href | Link to neighbor region page |
| Row text | Region name |
| Country flag `<img>` | Owner country flag |
| Country name text | Owner country name |

Example neighbors for Lithuania Minor: Samogitia (Lithuania), Kaliningrad (Lithuania), Sudovia (Indonesia), Scania (Sweden).

### Other Embedded JavaScript Variables

| Variable | Description |
|----------|-------------|
| `SERVER_DATA.csrfToken` | CSRF token (useful if making subsequent POST requests) |
| `SERVER_DATA.serverTime` | Server time with RFC, dateTime, timezone, unix timestamp |
| `SERVER_DATA.isLoggedIn` | Whether the current session is authenticated |
| `erepublik.settings.eDay` | Current eRepublik day number |
| `erepublik.settings.culture` | Language code (e.g., `"en"`) |
| `countrySelector` | Country dropdown configuration |

Example `SERVER_DATA.serverTime`:
```json
{
    "rfc": "Fri, 06 Feb 2026 22:04:47 -0800",
    "dateTime": "2026-02-06 22:04:47",
    "timezone": "PST",
    "offset": -800,
    "hour": 22,
    "unix": 1770444287,
    "unixMs": 1770444287000
}
```

### Notes

- **HTML Response:** Unlike most endpoints in this documentation, this is a full HTML page, not a JSON API. You need an HTML parser (e.g., Jsoup for Java, BeautifulSoup for Python, Cheerio for Node.js) to extract data.
- **No Authentication Required:** The page is publicly accessible. However, authenticated requests may show additional UI elements.
- **Permalink Format:** Region permalinks use hyphens for spaces (e.g., `Lithuania-Minor`, `Rhineland-Westphalia`). Invalid permalinks return a 404 page.
- **Pollution Array Indexing:** Index 0 = raw materials, indices 1–7 = quality levels q1–q7. Houses and Aircraft only go up to q5; indices 6–7 have `has_pollution: 0` and `pollution: "N/A"`.
- **Dynamic Content:** Some sections (like determination arrays) may be populated during wartime. Empty arrays indicate no active resistance or defensive determinations. See the Sudovia example for populated determination data.
- **Dictatorship Indicator:** Countries under dictatorship government display a `<span class="dictatorship_flag_icon_l">` element before the country flag image. This can be used to determine the country's government type.
- **Occupied Regions:** When a region is occupied by a foreign country, the country dropdown shows the occupier as `selected`.
- **Voter Registration:** The `span.cityVoters` element appears on all region pages (not just occupied ones). It shows messages like "No registered voters" alongside residents and "No residents with the citizenship of this country" alongside visitors.
- **Building Slots:** All regions have exactly 15 construction slots. Empty slots have no `<span>` with building data inside.
- **No Resources:** Some regions have no natural resources. The `.area.resource .row` will be empty in this case.

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
