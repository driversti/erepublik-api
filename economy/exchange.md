# Exchange Market (Monetary Market)

#erepublik #api #economy #exchange #monetary

[< Back to Economy Overview](README.md)

Gold/Country Currency exchange -- browsing offers, purchasing, and managing your own exchange offers.

**Related:** [Inventory](inventory.md) | [Game Tokens](game-tokens.md)

---

## Exchange Market Page

**Method:** GET
**URL:** `/en/economy/exchange-market/`
**Auth Required:** Yes

### Description

The Monetary Market page where citizens can buy and sell Gold <-> Country Currency (CC). Returns an HTML page containing embedded JavaScript variables with exchange rate data, current offers table, and form elements for trading. This is a **page endpoint** (returns HTML, not JSON) -- structured data is embedded in `<script>` tags.

### Parameters

No URL parameters. The page defaults to showing offers to buy Gold for the citizen's country currency (CC).

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Accept | `text/html,application/xhtml+xml` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/exchange-market/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

</details>

### Page Structure

The HTML page contains these key sections:

1. **Exchange Bar** -- Currency switcher (Buy GOLD / Sell CC or vice versa)
2. **Create Form** -- Hidden form to create a new exchange offer (amount + exchange rate)
3. **Offers Table** -- `<table class="exchange_offers">` with 10 offers per page
4. **Pagination** -- `<ul class="pager">` with page links

### Embedded JavaScript Variables

#### `EXCHANGE` -- Exchange Configuration

<details>
<summary>EXCHANGE variable</summary>

```javascript
var EXCHANGE = {
    culture: 'en',
    update_offer_confirmation_text: "Are you sure you want to update this offer?",
    remove_offer_confirmation_text: "Are you sure you want to remove this offer ?",
    invalid_decimal_value_text: "Please enter a valid amount value with at most 2 decimals.",
    invalid_decimal_value3_text: "Please enter a valid exchange rate value with at most 3 decimals.",
    invalid_amount_value_text: "Invalid amount value.",
    invalid_exchange_rate_text: "Invalid exchange rate.",
    lowestBuyGoldExchangeRate: 1082.000,
    lowestBuyCurrencyExchangeRate: 0.001
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `culture` | string | Language code (e.g., `en`) |
| `lowestBuyGoldExchangeRate` | number | Best rate to buy Gold (cheapest Gold price in CC) |
| `lowestBuyCurrencyExchangeRate` | number | Best rate to buy CC (cheapest CC price in Gold) |

#### `sell_currency_data` -- The Currency Being Sold

<details>
<summary>sell_currency_data variable</summary>

```javascript
var sell_currency_data = {
    "id": 1,
    "name": "LTL",
    "icon": "//www.erepublik.net/images/flags_png/M/Lithuania.png",
    "small_icon": "//www.erepublik.net/images/flags_png/S/Lithuania.png",
    "large_icon": "//www.erepublik.net/images/flags_png/L/Lithuania.png"
};
```

</details>

#### `buy_currency_data` -- The Currency Being Bought

<details>
<summary>buy_currency_data variable</summary>

```javascript
var buy_currency_data = {
    "id": 62,
    "name": "GOLD",
    "icon": "//www.erepublik.net/images/modules/_icons/gold_24.png",
    "small_icon": "//www.erepublik.net/images/modules/_icons/gold_icon.png",
    "large_icon": "//www.erepublik.net/images/modules/_icons/gold_24.png"
};
```

</details>

### Hidden Form Fields

| Field | ID | Example Value | Description |
|-------|----|---------------|-------------|
| Buy Currency | `buy_currency` | `62` | Currency ID being bought (62 = GOLD) |
| Sell Currency | `sell_currency` | `1` | Currency ID being sold (1 = LTL) |
| CSRF Token | `_token` | `99e749d8...` | Required for all POST actions |

### Offers Table Structure

Each row in `<table class="exchange_offers">` contains:

| Column | CSS Class | Data | Example |
|--------|-----------|------|---------|
| Citizen | `ex_citizen` | Seller profile link + avatar | `<a href='//...citizen/profile/9736962'>andyherts</a>` |
| Amount | `ex_amount` | Amount of currency for sale | `1.00 GOLD` |
| Rate | `ex_rate` | Exchange rate with currency icons | `1 GOLD = 1082.000 LTL` |
| Buy | `ex_buy` | Input field + buy button | `<button id='purchase_6806828' data-price='1082.000' data-max='1.00'>` |

The buy button's `data-` attributes expose key offer data:
- `id="purchase_{offerId}"` -- Offer ID (e.g., `6806828`)
- `data-price` -- Exchange rate
- `data-max` -- Maximum amount available to buy
- `data-currency` -- Currency name

### Stylesheets & Scripts

| Script | Purpose |
|--------|---------|
| `exchange.{timestamp}.js` | Exchange market logic (offers, pagination, CRUD operations) |

### Notes

- **Default View:** Shows "Buy GOLD, Sell CC" for the citizen's country currency
- **Currency Swap:** The "Swap" button switches between buying GOLD for CC vs buying CC for GOLD
- **Pagination:** 10 offers per page, paginated via AJAX (see Exchange Retrieve Offers endpoint below)
- **Currency IDs:** `62` = GOLD; country currency IDs vary (e.g., `1` = LTL, see other endpoints for mapping)
- **CSRF Token:** The `_token` hidden field is required for all exchange POST operations (purchase, create, update, delete)
- **All exchange AJAX endpoints** are documented below -- they are called by `exchange.js` for all trading operations

---

## Exchange Retrieve Offers

**Method:** POST
**URL:** `/{lang}/economy/exchange/retrieve/`
**Auth Required:** Yes

### Description

Retrieves a page of exchange offers for a given currency. Used for pagination (navigating pages) and for switching between "buy Gold" and "buy CC" views. Returns HTML fragments that replace the offers table on the page.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| currencyId | number | Yes | Currency ID to show offers for (e.g., `62` for GOLD, `1` for LTL) |
| page | number | Yes | Page number (0-indexed) |
| personalOffers | number | No | `1` to show only the citizen's own offers, `0` or omit for all offers |
| show_create_form | number | No | `1` to include the create offer form in the response |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/retrieve/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&currencyId=62&page=0&personalOffers=0'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "buy_mode": "<table>...HTML offers table...</table><ul class='pager'>...</ul>",
  "essentials": "<input id='buy_currency' ... /><input id='sell_currency' ... />",
  "currencyId": "62",
  "page": "0"
}
```

</details>

### Notes

- **Response contains HTML fragments** (`buy_mode`, `essentials`) that replace DOM elements on the page
- **Client-side caching:** The `exchange.js` caches responses in a JS object keyed by `currency_{currencyId}_{page}`, so repeated requests for the same page are served from memory
- **Pages are 0-indexed** in the API but displayed as 1-indexed in the UI
- `personalOffers: 1` switches to "My offers" view (showing only the authenticated citizen's offers)
- On error, returns `{ "error": true, "message": "..." }`

---

## Exchange Purchase

**Method:** POST
**URL:** `/{lang}/economy/exchange/purchase/`
**Auth Required:** Yes

### Description

Purchases currency from an existing exchange offer. The citizen buys from another citizen's offer at the listed exchange rate. Requires the offer ID, amount to buy, and the current page/currency context.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| offerId | number | Yes | The offer ID to buy from (from `purchase_{offerId}` button) |
| amount | number | Yes | Amount to buy (max 2 decimal places, cannot exceed `data-max`) |
| page | number | Yes | Current page number (0-indexed) |
| currencyId | number | Yes | Current currency view ID (e.g., `62` for GOLD) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/purchase/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&offerId=6806828&amount=1.00&page=0&currencyId=62'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "error": false,
  "message": "Exchange successful!",
  "gold": { "value": "123.45" },
  "ecash": { "value": "67890.12" },
  "buy_mode": "<table>...updated offers table...</table>",
  "essentials": "<input .../>",
  "hideOffer": false
}
```

</details>

<details>
<summary>Example Response (Error)</summary>

```json
{
  "error": true,
  "message": "Insufficient funds."
}
```

</details>

### Notes

- **Amount validation:** Max 2 decimal places, must be positive, cannot exceed offer's `data-max`
- **Balance update:** Response includes updated `gold.value` and `ecash.value` for the citizen's account
- **`hideOffer`:** `true` if the purchased offer is now fully consumed and should be removed from the table
- **Exchange rate** is determined by the offer, not passed as a parameter -- the citizen pays at the listed rate
- The confirmation dialog format: `"Buy {amount} {currency} for {total} {otherCurrency}?"`

---

## Exchange Create Offer

**Method:** POST
**URL:** `/{lang}/economy/exchange/create/`
**Auth Required:** Yes

### Description

Creates a new exchange offer on the monetary market. The citizen lists an amount of currency to sell at a specified exchange rate.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| sellCurrencyId | number | Yes | Currency ID being sold (e.g., `1` for LTL) |
| buyCurrencyId | number | Yes | Currency ID being bought (e.g., `62` for GOLD) |
| amount | number | Yes | Amount of sell currency to offer (max 2 decimal places) |
| at_exchange_rate | number | Yes | Exchange rate (max 3 decimal places) |
| page | number | Yes | Current page (always `0` on create) |
| personalOffers | number | Yes | Always `1` (switches to "My offers" view after creation) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/create/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&sellCurrencyId=1&buyCurrencyId=62&amount=100.00&at_exchange_rate=1082.000&page=0&personalOffers=1'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "error": false,
  "message": "Offer created successfully!",
  "gold": { "value": "123.45" },
  "ecash": { "value": "67790.12" },
  "edit_mode": "<div>...my offers HTML...</div>",
  "buy_mode": "<table>...offers table...</table>",
  "essentials": "<input .../>"
}
```

</details>

### Notes

- **Rate format:** "1 {sellCurrency} = {at_exchange_rate} {buyCurrency}" -- e.g., selling 100 LTL at rate 1082.000 means 1 GOLD = 1082 LTL
- **Amount validation:** Max 2 decimal places, must be positive
- **Exchange rate validation:** Max 3 decimal places, must be positive
- **After creation:** Response updates the citizen's balance (funds are deducted/held) and switches to "My offers" view
- **Confirmation dialog:** `"Sell {amount} {sellCurrency} for {total} {buyCurrency}?"`
- **Offer limit:** Citizens can have a limited number of offers (counter shown as `my_offers_count`)

---

## Exchange Update Offer

**Method:** POST
**URL:** `/{lang}/economy/exchange/update/`
**Auth Required:** Yes

### Description

Updates an existing exchange offer owned by the authenticated citizen. Allows changing the amount and/or exchange rate of the offer.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| offerId | number | Yes | The offer ID to update |
| amount | number | Yes | New amount (max 2 decimal places) |
| at_exchange_rate | number | Yes | New exchange rate (max 3 decimal places) |
| currencyId | number | Yes | Current currency view ID |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/update/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&offerId=6806828&amount=50.00&at_exchange_rate=1083.000&currencyId=62'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "error": false,
  "message": "Offer updated!",
  "offer_id": 6806828,
  "gold": { "value": "123.45" },
  "ecash": { "value": "67890.12" },
  "edit_mode": "<div>...updated my offers HTML...</div>",
  "buy_mode": "<table>...offers table...</table>",
  "essentials": "<input .../>"
}
```

</details>

### Notes

- **Only own offers:** Citizens can only update their own offers
- **Shows confirmation dialog** before sending the request
- **Balance adjusts** if amount changes (difference is refunded or charged)
- `offer_id` in response confirms which offer was updated

---

## Exchange Delete Offer

**Method:** POST
**URL:** `/{lang}/economy/exchange/delete/`
**Auth Required:** Yes

### Description

Removes an existing exchange offer owned by the authenticated citizen from the monetary market. The held funds are returned to the citizen's account.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the page |
| offerId | number | Yes | The offer ID to delete |
| page | number | Yes | Current page number (0-indexed) |
| currencyId | number | Yes | Current currency view ID |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/economy/exchange/delete/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&offerId=6806828&page=0&currencyId=62'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "error": false,
  "message": "Offer removed!",
  "gold": { "value": "124.45" },
  "ecash": { "value": "67890.12" },
  "buy_mode": "<table>...updated offers table...</table>",
  "edit_mode": "<div>...my offers HTML...</div>",
  "essentials": "<input .../>"
}
```

</details>

### Notes

- **Only own offers:** Citizens can only delete their own offers
- **Shows confirmation dialog:** "Are you sure you want to remove this offer?"
- **Funds returned:** The held currency is credited back to the citizen's account (reflected in `gold.value` / `ecash.value`)
- **View switch:** If no more personal offers remain, the response may omit `edit_mode` and the UI switches back to "All offers" view
- **Offer counter:** The "My offers" counter in the UI decreases by 1
