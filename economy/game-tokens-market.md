# Game Tokens market

[API docs](../README.md) › Economy › Game Tokens market

Trading Game Tokens for country currency between citizens. One URL serves reading, buying, selling and removing offers; `action` selects which. Paying [shop packs](shop.md#confirm-token-purchase) with tokens is separate. Earlier notes: tokens are a premium currency for VIP, special items and services.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Market page](#market-page) | GET | `/en/economy/game-tokens-market` | ✅ Verified 2026-11-27 |
| [Market data](#market-data) | POST | `/en/economy/gameTokensMarketAjax` | ✅ Verified 2026-11-27 |
| [Buy tokens](#buy-tokens) | POST | `/en/economy/gameTokensMarketAjax` | 🔍 From code |
| [Create a sell offer](#create-a-sell-offer) | POST | `/en/economy/gameTokensMarketAjax` | 🔍 From code |
| [Remove a sell offer](#remove-a-sell-offer) | POST | `/en/economy/gameTokensMarketAjax` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Country IDs: [reference](../basics/id-tables.md).

---

## Market page

`GET /en/economy/game-tokens-market` · Auth required · ✅ Verified 2026-11-27

HTML page (`GameTokensMarketController`). `SERVER_DATA.gameTokensMarket`:

| Field | Description |
|-------|-------------|
| `lockMarket` | `true` disables buying in the client |
| `taxAmount` | `0.05` (5%) |
| `allianceData[]` | `id`, `name`, `acronym`, `avatar` for alliance-only offers |

The first request uses your citizenship country.

**Source:** real request, 2026-11-27.

---

## Market data

`POST /en/economy/gameTokensMarketAjax` · Auth required · ✅ Verified 2026-11-27

Top offers of a country, your tokens and offers, and today's limit. Read-only.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `action` | string | Yes | `retrieve` |
| `countryId` | number | Yes | Market |
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |

```bash
curl 'https://www.erepublik.com/en/economy/gameTokensMarketAjax' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=retrieve&countryId=72&_token=YOUR_CSRF_TOKEN'
```

### Response

Shortened to two of five offers:

```json
{
  "error": false,
  "topOffers": [
    { "sellers": "1 Seller(s)", "amount": 161, "price": 38290, "id": "offer_1", "canBuy": true, "sellerCountries": [57] },
    { "sellers": "2 Seller(s)", "amount": 33, "price": 38798, "id": "offer_3", "canBuy": false, "sellerCountries": [29, 13] }
  ],
  "citizenData": { "availableTokens": 0, "myOffers": [], "offersLimit": 3 },
  "histogram": [],
  "stats": "There are 3300 items for sale, available from 35 sellers. Only the Top 5 offers are shown.",
  "dailyLimit": "You acquire a maximum of 150 Game Tokens per day.",
  "dailyLimitRemaining": 150
}
```

| Field | Type | Description |
|-------|------|-------------|
| `topOffers[]` | array | Five cheapest price points |
| `topOffers[].sellers` | string | Sellers at that price |
| `topOffers[].amount` | number | Tokens available |
| `topOffers[].price` | number | Price per token in country currency. Earlier notes: in hundredths (see [Open questions](#open-questions)) |
| `topOffers[].canBuy` | boolean | The client additionally allows buying only from the first offer |
| `topOffers[].sellerCountries` | array | Sellers' countries |
| `citizenData.availableTokens` | number | Your tokens |
| `citizenData.myOffers` | array | Your sell offers |
| `citizenData.offersLimit` | number | Maximum sell offers |
| `histogram` | array | Empty when observed |
| `stats` | string | Market summary |
| `dailyLimit`, `dailyLimitRemaining` | string, number | Daily purchase limit and what is left; it resets with the game day |

When `citizenData` is missing the client hides selling. Earlier notes: only citizens of the country trade in its market, and the daily limit varies by citizen level or VIP status.

**Source:** real request, 2026-11-27; `GameTokensMarketController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Buy tokens

`POST /en/economy/gameTokensMarketAjax` · Auth required · 🔍 From code

> [!WARNING]
> Spends currency.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `buyOffer` |
| `amount` | number | Yes | The client caps it at the offer amount and the daily limit |
| `price` | number | Yes | Price of the offer |
| `countryId` | number | Yes | Market |

```bash
curl 'https://www.erepublik.com/en/economy/gameTokensMarketAjax' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=buyOffer&amount=1&price=38290&countryId=72&_token=YOUR_CSRF_TOKEN'
```

On success the answer is [market data](#market-data) plus `message` and `currency` (new balance); on failure `error` and `message`.

---

## Create a sell offer

`POST /en/economy/gameTokensMarketAjax` · Auth required · 🔍 From code

> [!WARNING]
> Puts your tokens on sale.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action` | string | Yes | `createOffer` |
| `amount` | number | Yes | Tokens |
| `pricePerUnit` | number | Yes | Price per token |
| `availability` | string | Yes | `global`, `local` or `alliance` |
| `countryId` | number | Yes | Market; for `local`, the chosen country |
| `offerCountryId` | number | For `local` | Same country |
| `offerAllianceId` | number | For `alliance` | Alliance |

The answer is market data plus `message`.

---

## Remove a sell offer

`POST /en/economy/gameTokensMarketAjax` · Auth required · 🔍 From code

> [!WARNING]
> Removes your offer.

Parameters: `_token`, `action=deleteOffer`, `offerId`, `countryId`. The answer is market data plus `message`.

**Source** (all three actions): `GameTokensMarketFactory` (version 1795779904).

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Action names | `buy`, `sell`, `cancel` | `buyOffer`, `createOffer`, `deleteOffer` | Code |
| Price unit | Hundredths | Client multiplies `price × amount` for the confirmation without dividing | Not settled |
| Tokens back to Gold | Not possible | Not checked | — |
