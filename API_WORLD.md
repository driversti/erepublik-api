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
