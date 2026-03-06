# Travel

#erepublik #api #world #travel

Travel endpoints for retrieving travel data, filtering by country, and executing travel actions.

See also: [Cities](cities.md) | [Map & Regions](map.md) | [Overview](README.md)

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

<details>
<summary>cURL Example</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/travelData' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&holdingId=0&battleId=0&regionId=663'
```

</details>

### Example Response

<details>
<summary>JSON Response</summary>

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

</details>

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

<details>
<summary>cURL Example</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/travelData' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'check=getCountryRegions&countryId=72&_token=YOUR_CSRF_TOKEN&holdingId=0&regionId=0'
```

</details>

### Example Response

<details>
<summary>JSON Response</summary>

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

</details>

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

<details>
<summary>cURL Example</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/travel' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'check=moveAction&_token=YOUR_CSRF_TOKEN&travelMethod=preferCurrency&inRegionId=664&toCountryId=72'
```

</details>

### Example Response

<details>
<summary>JSON Response (Success)</summary>

```json
{
  "error": 0,
  "message": "success"
}
```

</details>

<details>
<summary>JSON Response (Error - insufficient funds/tickets)</summary>

```json
{
  "error": 1,
  "message": "You don't have enough currency/tickets to travel"
}
```

</details>

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

**Tags:** #erepublik #api #world #travel #regions #countries #geography
