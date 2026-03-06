# My Market Offers

#erepublik #api #economy #marketplace #offers

[< Back to Economy Overview](README.md)

Retrieve the authenticated citizen's own active marketplace listings.

**Related:** [Marketplace](marketplace.md) | [Inventory](inventory.md)

---

## Get My Market Offers

**Method:** GET
**URL:** `/en/economy/myMarketOffers`
**Auth Required:** Yes

### Description

Retrieves all active marketplace offers (sell listings) created by the authenticated citizen. Returns an array of offers with product details, pricing, stock levels, and sales statistics. If the citizen has no active offers, returns an empty array.

### Parameters

No parameters required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/myMarketOffers' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

</details>

<details>
<summary>Example Response</summary>

**Empty offers (no active listings):**
```json
[]
```

**With active offers:**
```json
[
  {
    "id": 77924504,
    "country_id": 72,
    "industry_id": 1,
    "quality": 1,
    "amount": 119998,
    "price": 0.861386,
    "stock": 120000,
    "sold": 2,
    "created_at": "2026-01-24 10:30:00",
    "updated_at": "2026-01-25 08:15:00",
    "currency": "LTL",
    "product_name": "Food Q1"
  },
  {
    "id": 77838777,
    "country_id": 72,
    "industry_id": 2,
    "quality": 7,
    "amount": 4025,
    "price": 15.50,
    "stock": 5000,
    "sold": 975,
    "created_at": "2026-01-23 14:20:00",
    "updated_at": "2026-01-25 06:45:00",
    "currency": "LTL",
    "product_name": "Ammunition Q7"
  }
]
```

</details>

### Notes

- **Response Format:**
  - Returns an empty array `[]` if the citizen has no active marketplace offers
  - Returns an array of offer objects if the citizen has active listings

- **Offer Fields:**
  - `id`: Unique marketplace offer ID
  - `country_id`: Country where the offer is listed (marketplace location)
  - `industry_id`: Product industry type (1=Food, 2=Weapons, 23=Aircraft, 4=Houses)
  - `quality`: Product quality level (1-7 for most products, null for raw materials)
  - `amount`: Current available stock (decreases as items are purchased)
  - `price`: Price per unit (in country's local currency)
  - `stock`: Original stock when offer was created
  - `sold`: Number of items sold from this offer
  - `created_at`: Timestamp when offer was created
  - `updated_at`: Timestamp of last modification (price change, stock update)
  - `currency`: Currency code (LTL, USD, EUR, RON, etc.)
  - `product_name`: Human-readable product name

- **Use Cases:**
  - Check status of your active marketplace listings
  - Monitor sales and remaining stock
  - Identify which offers need restocking
  - Track pricing and sales performance

- **Marketplace Management:**
  - Offers remain active until stock is depleted or manually removed
  - Price can be updated without recreating the offer
  - Stock can be added to existing offers
  - Multiple offers for the same product type can exist simultaneously

- **Industry IDs:**
  - 1: Food (Q1-Q7)
  - 2: Weapons/Ammunition (Q1-Q7)
  - 3: Moving Tickets (Q1-Q5)
  - 4: Houses (Q1-Q5)
  - 23: Aircraft Weapons (Q1-Q5)
  - 7, 12, 17, 24: Raw Materials (Q1 only)

- **Related Endpoints:**
  - Use `marketplaceAjax` to browse offers from all sellers -- see [marketplace.md](marketplace.md)
  - Use `marketplaceActions` to create new offers or update existing ones -- see [marketplace.md](marketplace.md)
  - This endpoint is read-only (GET) - modifications require POST actions

- This endpoint is used by the "My Offers" section on the marketplace/inventory pages
- Response is typically very fast (<50ms) since it queries a single citizen's offers
- No pagination - all active offers are returned in a single response
