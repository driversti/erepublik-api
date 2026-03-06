# eRepublik API - Country

#erepublik #api #country #politics #economy

[< Back to Table of Contents](../API_TOC.md)

---

## Overview

Country endpoints cover country-specific pages and data, including society statistics, political structure, economy, and military information for individual countries.

---

## Endpoints

| File | Endpoint | Method | URL | Auth |
|------|----------|--------|-----|------|
| [society.md](society.md) | Country Society Page | GET | `/en/country/society/{countryName}` | No |
| [economy.md](economy.md) | Country Economy Page | GET | `/en/country/economy/{countryName}` | No |
| [politics.md](politics.md) | Country Politics Page | GET | `/en/country/politics/{countryName}` | No |
| [military.md](military.md) | Country Military Page | GET | `/en/country/military/{countryName}` | No |
| [administration.md](administration.md) | Country Administration Page | GET | `/en/country-administration/{countryName}/{page}` | No |
| [congress.md](congress.md) | Propose Donation (Congressional) | POST | `/en/{countryName}/new-donation` | Yes (Congress) |
| [edge-cases.md](edge-cases.md) | Edge Cases and Empty States | -- | -- | -- |

---

## Global JavaScript Variables (All Country Pages)

These JavaScript variables are embedded in all country pages and provide useful metadata that is **not page-specific**. They appear in a `<script>` block in the `<head>` section.

### Dictatorships and Empires

Two global constants identify current dictatorship and empire countries:

<details>
<summary>JavaScript example</summary>

```javascript
const dictatorships = {"47":1,"67":1,"28":1,"26":1,"33":1,"69":1,"170":1,"168":1,"30":1},
      empires = {"35":1};

// Applied via:
erepublik.info.updateCountryInfo(countryId, {is_dictatorship: true});
erepublik.info.updateCountryInfo(countryId, {is_empire: true});
```

</details>

| Variable | Type | Description |
|----------|------|-------------|
| `dictatorships` | `Object` | Map of country IDs (string keys) that are currently dictatorships. Value is always `1` |
| `empires` | `Object` | Map of country IDs (string keys) that are currently empires. Value is always `1` |

Example mappings (eDay 6659):

| Country ID | Country | Type |
|------------|---------|------|
| 26 | Mexico | Dictatorship |
| 28 | Venezuela | Dictatorship |
| 30 | Switzerland | Dictatorship |
| 33 | Austria | Dictatorship |
| 47 | South Korea | Dictatorship |
| 67 | Philippines | Dictatorship |
| 69 | Bosnia and Herzegovina | Dictatorship |
| 168 | Georgia | Dictatorship |
| 170 | Nigeria | Dictatorship |
| 35 | Poland | Empire |

> **Note:** These values are **dynamic** -- they reflect the current game state at the time the page is loaded. Dictatorships and empires change as in-game political events occur (coups, elections, etc.).

### ErpkPvp Configuration

PvP-related configuration, present on all pages when logged in:

<details>
<summary>JavaScript example</summary>

```javascript
var ErpkPvp = (ErpkPvp || {});
ErpkPvp.bosh_service = 'unassigned2';
ErpkPvp.muc_host = 'unassigned1';
ErpkPvp.citizenId = 4690052;
```

</details>

### SERVER_DATA.countryId (Economy & Military Pages Only)

On Economy and Military pages, the country ID is injected into `SERVER_DATA` via inline script:

<details>
<summary>JavaScript example</summary>

```javascript
(SERVER_DATA || (SERVER_DATA = {})).countryId = 167;
```

</details>

This makes the viewed country's ID available to donation form JavaScript handlers.

---

## Template

Use this template when documenting new country endpoints:

```markdown
## Endpoint Name

**Method:** GET/POST
**URL:** `/en/country/path/{countryName}`
**Auth Required:** Yes/No

### Description

Brief explanation of what this endpoint does.

### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country name |

### Headers

| Header | Value | Description |
|--------|-------|-------------|
| Content-Type | application/json | ... |

### Example Request (cURL)

\`\`\`bash
curl -X GET "https://www.erepublik.com/en/country/..." \
  -H "Cookie: erpk=..."
\`\`\`

### Example Response

\`\`\`json
{
  "success": true,
  "data": {}
}
\`\`\`

### Notes

Any additional information, gotchas, or observations.
```

---

## Related

- [API Table of Contents](../API_TOC.md)
- [World & Travel API](../API_WORLD.md)
- [Social API](../API_SOCIAL.md)
- [Military API](../API_MILITARY.md)
- [Economy API](../API_ECONOMY.md)
