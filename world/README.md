# eRepublik API - World & Travel

#erepublik #api #world #travel #geography

This module covers world geography, travel mechanics, regions, cities, and countries.

---

## Sub-files

| File | Description | Endpoints |
|------|-------------|-----------|
| [travel.md](travel.md) | Travel data and movement | Get Travel Data, Get Travel Data (Filter by Country), Travel to Region |
| [cities.md](cities.md) | City information and management | Get City Overview Data, Get City Residents List, Get City Properties, Search Cities, City Overview Widget Sub-endpoints, Establish Residence |
| [map.md](map.md) | World map and region data | Get World Map Data, Get Region Page |

---

## Endpoints Overview

| Endpoint | Method | URL | Auth | File |
|----------|--------|-----|------|------|
| Get Travel Data | POST | `/en/main/travelData` | Yes | [travel.md](travel.md) |
| Get Travel Data (Filter by Country) | POST | `/en/main/travelData` | Yes | [travel.md](travel.md) |
| Travel to Region | POST | `/en/main/travel` | Yes | [travel.md](travel.md) |
| Get City Overview Data | GET | `/en/main/city-data/{cityId}/overview` | Yes | [cities.md](cities.md) |
| Get City Residents List | GET | `/en/main/city-data/{cityId}/residents` | Yes | [cities.md](cities.md) |
| Get City Properties | GET | `/en/main/city-data/{cityId}/properties` | Yes | [cities.md](cities.md) |
| Search Cities | GET | `/en/main/city-search?search={query}` | No | [cities.md](cities.md) |
| City Overview Widget Sub-endpoints | GET | `/en/main/city-data/{cityId}/overview/{widget}` | Yes | [cities.md](cities.md) |
| Establish Residence | POST | `/en/main/city-actions` | Yes | [cities.md](cities.md) |
| Get World Map Data | GET | `/en/main/map-data?updated_at={timestamp}` | Yes | [map.md](map.md) |
| Get Region Page | GET | `/en/main/region/{RegionPermalink}` | No | [map.md](map.md) |

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
