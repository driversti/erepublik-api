# Marketplace and own offers

[API docs](../README.md) › Economy › Marketplace

Buying goods on a country's market, and selling your own from the [Storage](inventory.md) screen.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Marketplace page](#marketplace-page) | GET | `/en/economy/marketplace` | ✅ Verified 2026-11-27 |
| [Search offers](#search-offers) | POST | `/en/economy/marketplaceAjax` | ✅ Verified 2026-11-27 |
| [Public price snapshot](#public-price-snapshot) | GET | `/en/economy/marketpicture/{countryId}` | ✅ Verified 2026-11-27 |
| [Buy](#buy) | POST | `/en/economy/marketplaceActions` | 🔍 From code |
| [My offers](#my-offers) | GET | `/en/economy/myMarketOffers` | ✅ Verified 2026-11-27 |
| [Post or change an offer](#post-or-change-an-offer) | POST | `/en/economy/marketplaceActions` | 🔍 From code |
| [Remove an offer](#remove-an-offer) | POST | `/en/economy/marketplaceActions` | 🔍 From code |
| [Buy a market license](#buy-a-market-license) | POST | `/en/economy/marketplaceActions` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Country IDs: [reference](../basics/id-tables.md).

---

## Marketplace page

`GET /en/economy/marketplace` · Auth required · ✅ Verified 2026-11-27

AngularJS page (`MarketplaceController`). The hash sets the view and offers can be shared as links:

`/en/economy/marketplace#{countryId}/{industryId}/{quality}`

```bash
curl 'https://www.erepublik.com/en/economy/marketplace' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Variable | Description |
|----------|-------------|
| `filters` | `countryId`, `industryId`, `quality`, `entityType` (`citizen`), `companyId`, `orderBy`, `page` |
| `industryJSON` | [Industry definitions](#industries) |
| `conditionedIndustries` | `{"1": 2, "2": 5}`: minimum quality the interface warns about |
| `sharedOffer` | `false`, or the shared offer (`sharedOffer`, `error`, `message`, `can_buy`) |
| `citizenshipCountry`, `isOrganization` | Your citizenship; organization flag |
| `foodIndustryId`, `weaponIndustryId`, `houseIndustryId` | 1, 2, 4 |
| `csrfToken`, `linkForBuy`, `food_remaining`, `attributeIconPath` | Token, default link, food limit, icon base path |
| `empires`, `textForBuy`, `smallestFood` | In earlier notes: empire flags per country, button text, smallest food |

<details>
<summary>Page structure (earlier notes)</summary>

- `div#marketplace`: country `select#countryId` (all countries, yours preselected).
- Industry filters for 1, 2, 23, 3, 4, 7, 12, 24, 17, and a **Companies** link to the [company market](companies.md#company-market).
- Quality selector `div#minReq` (Q1–Q7, hidden until an industry is chosen) with an effect tooltip.
- Offer table columns: Product (import badge), Seller, Supply, Price (food: currency per energy; weapons: per use), Quantity, Buy, Share.
- Pager with up to five page numbers.
- Scripts and styles: `marketplace`, `selector`, `select2`.

</details>

### Industries

The nine IDs the market accepts. Raw materials are sold under the first resource of each group.

| ID | Industry | Qualities on the market | `marketpicture` key |
|----|----------|-------------------------|---------------------|
| 1 | Food | 1–7 | `food` |
| 2 | Weapons | 1–7 | `weapons` |
| 3 | Moving Tickets | 1–5 | `tickets` |
| 4 | Houses | 1–5 | `houses` |
| 23 | Aircraft weapons | 1–5 | `aircraft` |
| 7 | Food raw materials | 1 | `frm` |
| 12 | Weapon raw materials | 1 | `wrm` |
| 17 | House raw materials | 1 | `hrm` |
| 24 | Aircraft raw materials | 1 | `arm` |

<details>
<summary>All 29 entries of <code>industryJSON</code> (earlier notes)</summary>

| ID | Name | Token | Group |
|----|------|-------|-------|
| 1 | Food (Q0–Q7, special Q10–Q18) | `FOOD` | grass |
| 2 | Weapons (Q0–Q8, special 10, 11, 21, 22, 51, 52, 100, 211–216) | `WEAPON` | stone |
| 3 | Moving Tickets (Q1–Q5) | `TICKET` | |
| 4 | House (Q1–Q5; Q6–Q7 defined with effect 0) | `HOUSE` | wood |
| 5, 6 | Hospital, Defense System (legacy) | `HOSPITAL`, `DEFENSE` | |
| 7–11 | Grain, Fruits, Fish, Cattle, Deer | `GRAIN`, `FRUITS`, … | grass |
| 12–16 | Iron, Oil, Aluminum, Saltpeter, Rubber | `IRON`, `OIL`, … | stone |
| 17–19, 21, 22 | Sand, Clay, Wood, Limestone, Granite | `SAND`, `CLAY`, … | wood |
| 20 | Gift (Q1, Q11, Q12) | `GIFT` | |
| 23 | Aircraft Weapons (Q1–Q7; 10 AIM-92 Stinger, 51 AIM-7 Grid-Lock) | `AIRCRAFT` | composites |
| 24–28 | Magnesium, Titanium, Wolfram, Cobalt, Neodymium | `MAGNESIUM`, … | composites |
| 100 | Booster | `BOOSTER` | |

</details>

### Product effects

Earlier notes, from `industryJSON`:

| Quality | Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 |
|---------|----|----|----|----|----|----|----|
| Food energy | 2 | 4 | 6 | 8 | 10 | 12 | 20 |
| Weapon power / uses | 20/1 | 40/2 | 60/3 | 80/4 | 100/5 | 120/6 | 200/10 |
| House pool / hours / per 6 min | 100/168/+2 | 160/168/+2 | 200/168/+2 | 300/168/+2 | 400/168/+2 | | |
| Air weapon power / uses | 20/1 | 40/2 | 60/3 | 80/4 | 100/5 | | |
| Ticket energy / distance | −4/1000 | −3/2000 | −2/3000 | −1/4000 | 0/5000 | | |

**Source:** real request, 2026-11-27; page structure and some variables from earlier notes.

---

## Search offers

`POST /en/economy/marketplaceAjax` · Auth required · ✅ Verified 2026-11-27

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `countryId` | number | Yes | Market |
| `industryId` | number | Yes | [Industry](#industries) |
| `quality` | number | Yes | Quality |
| `orderBy` | string | Yes | `price_asc`, `price_desc`; earlier notes add `amount_asc`, `amount_desc` |
| `currentPage` | number | Yes | Starts at 1 |
| `ajaxMarket` | number | Yes | `1` |

```bash
curl 'https://www.erepublik.com/en/economy/marketplaceAjax' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'countryId=35&industryId=1&quality=1&orderBy=price_asc&currentPage=1&ajaxMarket=1&_token=YOUR_CSRF_TOKEN'
```

### Response

Shortened to one of ten offers:

```json
{
  "offers": [
    {
      "id": 79000001, "country_id": 35, "industry_id": 1, "citizen_id": 1234567,
      "amount": 50, "price": 1.346535, "customization_level": 1,
      "name": "SellerName", "is_for_export": 0, "priceWithTaxes": 1.36
    }
  ],
  "currency": "PLN",
  "can_buy": false,
  "pages": 2
}
```

| Field | Type | Description |
|-------|------|-------------|
| `offers[].id` | number | Offer ID |
| `offers[].citizen_id`, `name` | | Seller |
| `offers[].amount` | number | Quantity available |
| `offers[].price`, `priceWithTaxes` | number | Per unit, before and after import tax and VAT |
| `offers[].customization_level` | number | Quality |
| `offers[].is_for_export` | number | `0`, or the ID of the exporting country: the client looks it up as a country (earlier notes call it an export tax percentage) |
| `currency` | string | Market currency |
| `can_buy` | boolean | Whether you may buy in this market |
| `pages` | number | Page count |

A truthy `error` carries `message`.

**Source:** real request, 2026-11-27; `MarketplaceController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Public price snapshot

`GET /en/economy/marketpicture/{countryId}` · No auth · ✅ Verified 2026-11-27

The cheapest offers of every industry in one country. Needs no cookie, token or special header, so it suits price monitors.

```bash
curl 'https://www.erepublik.com/en/economy/marketpicture/35'
```

### Response

Shortened:

```json
{
  "food": {
    "q1": [ { "amount": 50, "net": 1.3465, "gross": 1.36, "import": 99, "vat": 1, "id": 79000001 } ],
    "q2": []
  },
  "weapons": {}, "tickets": {}, "houses": {}, "frm": {}, "wrm": {}, "hrm": {}, "aircraft": {}, "arm": {}
}
```

| Field | Description |
|-------|-------------|
| Top keys | See [Industries](#industries) |
| `q{n}` | Up to 5 offers, cheapest `gross` first |
| `id` | Offer ID, usable as `offerId` in [Buy](#buy) |
| `amount` | Quantity |
| `net`, `gross` | Unit price without and with VAT, in the market currency (not named in the answer) |
| `vat`, `import` | VAT and import tax in percent |

A quality without offers is left out, so iterate over the keys present. Seller and `is_for_export` are not included.

**Source:** real request without cookies, 2026-11-27.

---

## Buy

`POST /en/economy/marketplaceActions` · Auth required · 🔍 From code

> [!WARNING]
> Spends currency.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `offerId` | number | Yes | Offer |
| `amount` | number | Yes | Quantity |
| `buyAction` | number | Yes | `1` |
| `orderBy` | string | Yes | Sort of the returned list |
| `currentPage` | number | Yes | Page of the returned list |

```bash
curl 'https://www.erepublik.com/en/economy/marketplaceActions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'offerId=79000001&amount=1&orderBy=price_asc&currentPage=1&buyAction=1&_token=YOUR_CSRF_TOKEN'
```

### Response

Earlier notes:

```json
{
  "error": false,
  "message": "You have successfully bought 1 product for 0.87 LTL.",
  "offerUpdate": { "offers": [], "currency": "LTL", "can_buy": true, "pages": 1 },
  "currency": 0,
  "gold": 0
}
```

`offerUpdate` is a fresh [search result](#search-offers). `currency` and `gold` are the balances afterwards, as decimals and not hundredths (earlier notes, verified 2026-02-28).

### Errors

`error: true` with `message`; earlier notes: `You don't have enough money`, `Insufficient stock available`, `Offer not found`, `Your storage is full`. A bad token gives HTTP 403 or a token error. Earlier notes: no documented rate limit, but rapid purchases may trigger anti-bot checks.

**Source:** `MarketplaceFactory.buyOffer` (version 1795779904); response from earlier notes.

---

## My offers

`GET /en/economy/myMarketOffers` · Auth required · ✅ Verified 2026-11-27

Your active offers, all in one array; `[]` when there are none (the case observed).

```bash
curl 'https://www.erepublik.com/en/economy/myMarketOffers' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

With offers (earlier notes, sample values):

```json
[
  {
    "id": 79000002, "countryId": 35, "industryId": 1, "quality": 1,
    "amount": 100, "price": 1.5
  }
]
```

The client reads `countryId`, `industryId` and `price`, and adds `priceWithTaxes` itself. Earlier notes list snake_case fields instead:

<details>
<summary>Fields from earlier notes</summary>

`id`, `country_id`, `industry_id`, `quality` (null for raw materials), `amount` (remaining), `price`, `stock` (original), `sold`, `created_at`, `updated_at`, `currency`, `product_name`.

</details>

**Source:** real request, 2026-11-27; `SellItemsController.initData` (version 1795779904).

---

## Sell form data

The [inventory page](inventory.md#inventory-page) sets `SERVER_DATA.sellItems`:

| Field | Description |
|-------|-------------|
| `recommendedPrices` | `{countryId}{industryId}{quality}` → price prefilled in the form |
| `currencies` | Currency names |
| `industryIdsForMarket` | `[1,2,23,3,4,7,12,24,17]` |
| `maxQualities` | `{"1":7,"2":7,"3":5,"4":7,"23":5,"7":1,"12":1,"17":1,"24":1}` |
| `rawIndustries` | Raw material icons |
| `licenseCost`, `freeLicensesRemaining`, `licenseCountryId` | Market license price (`20` observed), free licenses left, preselected country |

You need a market license for a country to sell there; the client warns when you are at war with it or it has an embargo.

**Source:** inventory page, 2026-11-27; `SellItemsController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Post or change an offer

`POST /en/economy/marketplaceActions` · Auth required · 🔍 From code

> [!WARNING]
> Puts goods from your storage on sale.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `sellAction` | string | Yes | `postOffer` |
| `countryId` | number | Yes | Market |
| `industryId`, `quality` | number | Yes | Product |
| `amount` | number | Yes | Quantity |
| `price` | number | Yes | Unit price |

The answer has `error`, `message` and `offer`. When `offer.oldOfferId` matches an existing offer, the client replaces it.

**Source:** `SellItemsController.addOffer` (version 1795779904).

---

## Remove an offer

`POST /en/economy/marketplaceActions` · Auth required · 🔍 From code

> [!WARNING]
> Removes your offer.

Parameters: `_token`, `sellAction=deleteOffer`, `offerId`. Answer: `error`, `message`.

**Source:** `SellItemsController.deleteOffer`.

---

## Buy a market license

`POST /en/economy/marketplaceActions` · Auth required · 🔍 From code

> [!WARNING]
> Spends the license price or a free license.

Parameters: `_token`, `buyLicense=1`, `countryId`. Answer: `error`, `message`, `recommendedPrices` (keyed by country).

**Source:** `SellItemsController.buyLicense`.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Hash format | `#{countryId}/{quality}` | Client reads country, industry, quality | Code |
| `is_for_export` | Export tax percent | Used as a country ID | Code |
| House qualities on sale | Up to Q5 | `maxQualities` gives 7 for houses | Not settled |
| Changing an offer | Price and stock can be updated; several offers per product | One `postOffer` that may replace an old offer | Not settled |
| My offers fields | snake_case | camelCase read by the client | Code |
| Storage capacity | Default 5000, expandable | [Status](inventory.md#item-fields) gives the real value | — |
