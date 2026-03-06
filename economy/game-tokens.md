# Game Tokens Market

#erepublik #api #economy #gametokens

[< Back to Economy Overview](README.md)

Game Tokens market data retrieval -- browsing offers, checking balances, and daily purchase limits.

**Related:** [Exchange Market](exchange.md) | [Inventory](inventory.md)

---

## Game Tokens Market Retrieve

**Method:** POST
**URL:** `/en/economy/gameTokensMarketAjax`
**Auth Required:** Yes

### Description

Retrieves game tokens market data for a specific country, showing top market offers, citizen's token balance, active sell offers, daily purchase limits, and market statistics. Game Tokens are a special currency used for premium features and can be bought/sold on the market using country currency.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| action | string | Yes | Must be set to `retrieve` to fetch market data |
| countryId | number | Yes | Country ID where the market is located (e.g., 72 for Lithuania) |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/economy/gameTokensMarketAjax' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'action=retrieve&countryId=72&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "topOffers": [
    {
      "sellers": "1 Seller(s)",
      "amount": 179,
      "price": 30999,
      "id": "offer_1",
      "canBuy": true,
      "sellerCountries": [34]
    },
    {
      "sellers": "2 Seller(s)",
      "amount": 1302,
      "price": 31000,
      "id": "offer_2",
      "canBuy": false,
      "sellerCountries": [38, 65]
    },
    {
      "sellers": "1 Seller(s)",
      "amount": 9,
      "price": 31148,
      "id": "offer_3",
      "canBuy": false,
      "sellerCountries": [13]
    },
    {
      "sellers": "1 Seller(s)",
      "amount": 5,
      "price": 31149,
      "id": "offer_4",
      "canBuy": false,
      "sellerCountries": [53]
    },
    {
      "sellers": "1 Seller(s)",
      "amount": 938,
      "price": 31150,
      "id": "offer_5",
      "canBuy": false,
      "sellerCountries": [29]
    }
  ],
  "citizenData": {
    "availableTokens": 249,
    "myOffers": [],
    "offersLimit": 3
  },
  "histogram": [],
  "stats": "There are 4591 items for sale, available from 31 sellers. Only the Top 5 offers are shown.",
  "dailyLimit": "You acquire a maximum of 148 Game Tokens per day.",
  "dailyLimitRemaining": 148
}
```

</details>

### Notes

- **Top Offers:**
  - Only top 5 offers are displayed (sorted by price, lowest first)
  - `sellers`: Count of sellers offering at this price point
  - `amount`: Total quantity available at this price
  - `price`: Price per game token in country currency (displayed in hundredths, divide by 100)
  - `canBuy`: `true` if citizen can purchase from this offer (depends on citizenship, restrictions)
  - `sellerCountries`: Array of country IDs where sellers are from
  - `id`: Offer identifier (e.g., "offer_1", "offer_2")

- **Citizen Data:**
  - `availableTokens`: Current game tokens owned by the citizen
  - `myOffers`: Array of citizen's active sell offers (empty if none)
  - `offersLimit`: Maximum number of sell offers allowed (typically 3)

- **Market Statistics:**
  - `stats`: Human-readable summary of total market supply and sellers
  - `histogram`: Price distribution data (typically empty in basic retrieve action)
  - Total market size shown in stats (e.g., "4591 items for sale from 31 sellers")

- **Daily Purchase Limits:**
  - `dailyLimit`: Maximum game tokens that can be purchased per day (displayed as text)
  - `dailyLimitRemaining`: Remaining tokens available for purchase today (resets at eRepublik day change)
  - Prevents market manipulation by limiting daily purchases
  - Limit varies by citizen level/VIP status

- **Country IDs (Common):**
  - 13: Spain
  - 29: Bosnia and Herzegovina
  - 34: China
  - 38: Croatia
  - 53: Indonesia
  - 65: Poland
  - 72: Lithuania
  - (See other endpoints for more country IDs)

- **Game Tokens Usage:**
  - Premium currency used for VIP subscription, special items, and services
  - Tradeable between citizens via the market
  - Cannot be converted back to Gold
  - Prices fluctuate based on supply/demand

- **Related Actions:**
  - `action=buy`: Purchase game tokens from market offers
  - `action=sell`: Create a sell offer for game tokens
  - `action=cancel`: Cancel an existing sell offer

- The CSRF `_token` can be obtained from the game tokens market page source
- Response time is typically fast (<100ms) due to caching
- Market data updates in real-time as transactions occur
- Only citizens with citizenship in the specified country can buy/sell in that market
