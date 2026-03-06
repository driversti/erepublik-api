# Reference Data

Static reference tables for countries and regions in eRepublik. This data was extracted from the [`/en/main/travelData`](../API_WORLD.md) endpoint response.

#erepublik #api #reference #countries #regions

[< Back to Table of Contents](../API_TOC.md)

---

## Overview

> **Data source:** `POST /en/main/travelData` (authenticated). Region original owners are determined from the `isConqueredFrom` field (for occupied regions) and `countryId` (for non-occupied regions).

## Contents

| File | Description |
|------|-------------|
| [countries.md](countries.md) | Complete list of 74 countries with IDs and permalinks |
| [regions.md](regions.md) | Complete list of 574 regions with IDs, permalinks, and original owners; includes regions-by-country summary |

---

## Related

- [API Table of Contents](../API_TOC.md)
- [World & Travel API](../API_WORLD.md) -- travelData endpoint documentation
- [Country API](../API_COUNTRY.md) -- country page endpoints
