# Economy Module

#erepublik #api #economy

[< Back to Table of Contents](../API_TOC.md) | [Authentication](../API_AUTH.md)

---

## Overview

Economy endpoints cover marketplace, work mechanics, companies, currencies (Gold/CC), and financial transactions.

> For Gold earning methods and cost reference data, see [[Gold]].

---

## Sub-Files

| File | Description | Endpoints |
|------|-------------|-----------|
| [work.md](work.md) | Employment and daily work actions | Get Job Data, Work as Employee, Work Overtime, Work as Manager (WAM) |
| [job-market.md](job-market.md) | Job market browsing and management | Get Job Market Listings, Resign from Job, Apply for Job, Manage Employees Data |
| [training.md](training.md) | Training grounds and strength | Get Training Grounds Status, Train in Training Grounds |
| [shop.md](shop.md) | Gold Shop and VIP Shop items | Get Shop Items |
| [marketplace.md](marketplace.md) | Product marketplace (buy/sell goods) | Marketplace Page, Get Marketplace Offers, Buy from Marketplace |
| [my-offers.md](my-offers.md) | Citizen's own marketplace listings | Get My Market Offers |
| [inventory.md](inventory.md) | Full inventory snapshot | Get Inventory |
| [exchange.md](exchange.md) | Monetary market (Gold/CC exchange) | Exchange Market Page, Retrieve Offers, Purchase, Create/Update/Delete Offer |
| [companies.md](companies.md) | Company management | My Companies Page |
| [boosters.md](boosters.md) | Booster and house activation | Activate Booster, Activate House |
| [game-tokens.md](game-tokens.md) | Game Tokens market | Game Tokens Market Retrieve |

---

## Endpoints Quick Reference

| Endpoint | Method | URL | File |
|----------|--------|-----|------|
| Get Job Data | GET | `/en/main/job-data` | [work.md](work.md) |
| Work as Employee | POST | `/en/economy/work` | [work.md](work.md) |
| Work Overtime | POST | `/en/economy/workOvertime` | [work.md](work.md) |
| Work as Manager (WAM) | POST | `/en/economy/work` | [work.md](work.md) |
| Get Job Market Listings | GET | `/en/economy/job-market-json/{countryId}/{page}/{sortOrder}` | [job-market.md](job-market.md) |
| Resign from Job | POST | `/en/economy/resign` | [job-market.md](job-market.md) |
| Apply for Job | POST | `/en/economy/job-market-apply` | [job-market.md](job-market.md) |
| Manage Employees Data | GET | `/en/economy/manage-employees-json` | [job-market.md](job-market.md) |
| Get Training Grounds Status | GET | `/en/main/training-grounds-json` | [training.md](training.md) |
| Train in Training Grounds | POST | `/en/economy/train` | [training.md](training.md) |
| Get Shop Items | GET | `/en/main/shopItemsJson` | [shop.md](shop.md) |
| Marketplace Page | GET | `/en/economy/marketplace` | [marketplace.md](marketplace.md) |
| Get Marketplace Offers | POST | `/en/economy/marketplaceAjax` | [marketplace.md](marketplace.md) |
| Buy from Marketplace | POST | `/en/economy/marketplaceActions` | [marketplace.md](marketplace.md) |
| Get My Market Offers | GET | `/en/economy/myMarketOffers` | [my-offers.md](my-offers.md) |
| Get Inventory | GET | `/en/economy/inventory-json` | [inventory.md](inventory.md) |
| Exchange Market Page | GET | `/en/economy/exchange-market/` | [exchange.md](exchange.md) |
| Exchange Retrieve Offers | POST | `/{lang}/economy/exchange/retrieve/` | [exchange.md](exchange.md) |
| Exchange Purchase | POST | `/{lang}/economy/exchange/purchase/` | [exchange.md](exchange.md) |
| Exchange Create Offer | POST | `/{lang}/economy/exchange/create/` | [exchange.md](exchange.md) |
| Exchange Update Offer | POST | `/{lang}/economy/exchange/update/` | [exchange.md](exchange.md) |
| Exchange Delete Offer | POST | `/{lang}/economy/exchange/delete/` | [exchange.md](exchange.md) |
| My Companies Page | GET | `/en/economy/myCompanies` | [companies.md](companies.md) |
| Activate Booster | POST | `/en/main/activateBooster` | [boosters.md](boosters.md) |
| Activate House | POST | `/en/economy/activateHouse` | [boosters.md](boosters.md) |
| Game Tokens Market Retrieve | POST | `/en/economy/gameTokensMarketAjax` | [game-tokens.md](game-tokens.md) |

---

## Template

Use this template when documenting new endpoints:

```markdown
## Endpoint Name

**Method:** GET/POST
**URL:** `/en/path/to/endpoint`
**Auth Required:** Yes/No

### Description

Brief explanation of what this endpoint does.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| param1 | string | Yes | Description |

### Headers

| Header | Value | Description |
|--------|-------|-------------|
| Content-Type | application/json | ... |

### Example Request (cURL)

\`\`\`bash
curl -X GET "https://www.erepublik.com/en/..." \
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
- [Authentication](../API_AUTH.md)
- [Military API](../API_MILITARY.md)
- [Social API](../API_SOCIAL.md)
- [[VIP_Shop]] -- VIP levels, daily gift schedule, VIP points from purchases, weekly inventory items
- [[Loyalty_Program]] -- 5-level spending program (Bronze-Diamond), benefits, LP bonus on level-up
- [[Gold]] -- Gold Store items and pricing, Gold Bonus (referral rewards) mechanics
