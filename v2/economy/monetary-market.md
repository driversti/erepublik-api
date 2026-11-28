# Monetary market

[API docs](../README.md) › Economy › Monetary market

Exchanging Gold and country currency (CC). There is **one shared offer book**: every citizen sees the same offers whatever their own currency, and each side is settled in the citizen's own currency.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Market page](#market-page) | GET | `/en/economy/exchange-market/` | ✅ Verified 2026-11-27 |
| [List offers](#list-offers) | POST | `/en/economy/exchange/retrieve/` | ✅ Verified 2026-11-27 |
| [Buy from an offer](#buy-from-an-offer) | POST | `/en/economy/exchange/purchase/` | 🔍 From code |
| [Create an offer](#create-an-offer) | POST | `/en/economy/exchange/create/` | 🔍 From code |
| [Update an offer](#update-an-offer) | POST | `/en/economy/exchange/update/` | 🔍 From code |
| [Delete an offer](#delete-an-offer) | POST | `/en/economy/exchange/delete/` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). The paths end with a slash.

---

## Market page

`GET /en/economy/exchange-market/` · Auth required · ✅ Verified 2026-11-27

HTML page; the default view is buying Gold for your CC. It holds a currency switch bar, a hidden create form, a table of 10 offers and a pager. Logic: `cmp/exchange.js`.

```bash
curl 'https://www.erepublik.com/en/economy/exchange-market/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Variable | Description |
|----------|-------------|
| `EXCHANGE` | `culture`, `lowestBuyGoldExchangeRate`, `lowestBuyCurrencyExchangeRate` and message texts |
| `sell_currency_data` | Currency you pay: `id`, `name`, `icon`, `small_icon`, `large_icon` |
| `buy_currency_data` | Currency you get, same fields |

| Hidden field | Description |
|--------------|-------------|
| `buy_currency`, `sell_currency` | Currency IDs; `62` is Gold, country currencies differ (`1` in the examples) |
| `_token` | [CSRF token](../guides/requests.md#csrf-token) |
| `page` | Current page, from 0 |
| `show_create_form` | Sent when switching currency |
| `eCash`, `golden` | Your balances |
| `buy_confirmation_text`, `sell_confirmation_text` | Dialog texts |

### Offer row

`<table class="exchange_offers">`:

| Cell | Contents |
|------|----------|
| `ex_citizen` | Seller link and avatar |
| `ex_amount` | Amount for sale |
| `ex_rate` | Rate; the number is in `#exchange_rate_{offerId} span` |
| `ex_buy` | Input `#amount_to_buy_{offerId}` and button `#purchase_{offerId}` with `data-price`, `data-max`, `data-currency` |

Offers are sorted by rate, so the first row with `data-max` ≥ your amount is the cheapest deal.

**Source:** real request, 2026-11-27; earlier notes.

---

## List offers

`POST /en/economy/exchange/retrieve/` · Auth required · ✅ Verified 2026-11-27

Paging and switching between "buy Gold" and "buy CC". Read-only.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `currencyId` | number | Yes | Currency to buy |
| `page` | number | Yes | From 0 |
| `personalOffers` | number | No | `1` only your offers |
| `show_create_form` | string | No | Sent when switching currency, to get the form |

```bash
curl 'https://www.erepublik.com/en/economy/exchange/retrieve/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&currencyId=62&page=0&personalOffers=0'
```

### Response

```json
{
  "error": false,
  "message": "",
  "currencyId": 62,
  "page": 0,
  "ecash": { "id": 1, "citizen_id": 1000001, "currencyId": 1, "currency": "LTL", "currency_icon": "…", "value": 0 },
  "gold":  { "id": 2, "citizen_id": 1000001, "currencyId": 62, "currency": "GOLD", "currency_icon": "…", "value": 0 },
  "buy_mode": "<table class=\"exchange_offers\">…</table><ul class=\"pager\">…</ul>",
  "essentials": "<input id='buy_currency' … />…",
  "create_form": "…"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `buy_mode` | string | HTML: [offer table](#offer-row) and pager (`rel="page_{n}"`, from 1) |
| `essentials` | string | HTML: hidden fields and currency variables (includes token and balances) |
| `create_form` | string | HTML of the create form |
| `ecash`, `gold` | object | Your balances |
| `currencyId`, `page` | number | Echo of the request |

The client caches pages by `currency_{currencyId}_{page}`. On failure: `error: true`, `message`.

**Source:** real request, 2026-11-27; `cmp/exchange.js` (version 1795779905).

---

## Buy from an offer

`POST /en/economy/exchange/purchase/` · Auth required · 🔍 From code

> [!WARNING]
> Spends your currency or Gold.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `offerId` | number | Yes | Offer |
| `amount` | number | Yes | Up to 2 decimals, at most `data-max` |
| `page` | number | No | Sent by the web client |
| `currencyId` | number | No | Sent by the web client |
| `buyAction` | number | See below | Send `1`: earlier notes require it. The current web script does not send it, and a request without it is untested |

The rate comes from the offer; it is not a parameter.

```bash
curl 'https://www.erepublik.com/en/economy/exchange/purchase/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&offerId=6900001&amount=1.00&page=0&currencyId=62&buyAction=1'
```

### Response

Earlier notes:

```json
{
  "error": false,
  "message": "Exchange successful!",
  "gold": { "value": 0 },
  "ecash": { "value": 0 },
  "buy_mode": "…",
  "essentials": "…",
  "hideOffer": false
}
```

`gold.value` and `ecash.value` are the balances after the trade, `ecash` in your own currency. `hideOffer` means the offer is used up.

### Errors

`{ "error": true, "message": "Insufficient funds." }`. Gold purchases are capped at 10 Gold per citizen per game day; the message then contains `maximum limit`, which automation can match case-insensitively.

### Observed with real purchases (earlier notes, 2026-07-17)

- `amount=0.01` is accepted.
- The request worked with only `_token`, `offerId`, `amount`, `buyAction=1`, after opening the market page.
- The same offer and rate apply to buyers of any currency.
- No fee or tax: the buyer pays amount × rate (confirmed by a player). The 5% tax of the [Game Tokens market](game-tokens-market.md) does not apply here.
- Rates move a lot (about 1820 CC per Gold then, 1667 on 2026-11-27); do not budget from examples.

**Source:** `cmp/exchange.js` `purchaseFromOffer` (version 1795779905); response and observations from earlier notes.

---

## Create an offer

`POST /en/economy/exchange/create/` · Auth required · 🔍 From code

> [!WARNING]
> Puts your currency or Gold on sale; the amount is held.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `sellCurrencyId` | number | Yes | Currency you sell |
| `buyCurrencyId` | number | Yes | Currency you want |
| `amount` | number | Yes | Up to 2 decimals |
| `at_exchange_rate` | number | Yes | Up to 3 decimals |
| `page` | number | Yes | `0` |
| `personalOffers` | number | Yes | `1` |

```bash
curl 'https://www.erepublik.com/en/economy/exchange/create/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&sellCurrencyId=1&buyCurrencyId=62&amount=100.00&at_exchange_rate=1700.000&page=0&personalOffers=1'
```

> [!WARNING]
> The rate direction for offers selling country currency is not verified. Earlier notes also state the format "1 {sell currency} = rate {buy currency}", under which this example would ask 1700 Gold for 1 CC. Check the direction (for example in the game's confirmation text) before sending.

The answer has `error`, `message`, `gold`, `ecash`, `edit_mode` (HTML of your offers), `buy_mode`, `essentials`. Earlier notes: the rate reads "1 Gold = N CC" in their example, and the number of offers is limited (counter `my_offers_count`).

**Source:** `cmp/exchange.js` `createOffer`.

---

## Update an offer

`POST /en/economy/exchange/update/` · Auth required · 🔍 From code

> [!WARNING]
> Changes your offer; the balance is adjusted by the difference.

Parameters: `_token`, `offerId`, `amount` (2 decimals), `at_exchange_rate` (3 decimals), `currencyId`.

The answer has `error`, `message`, `offer_id`, `gold`, `ecash`, `edit_mode`, `buy_mode`, `essentials`.

```bash
curl 'https://www.erepublik.com/en/economy/exchange/update/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&offerId=6900001&amount=50.00&at_exchange_rate=1701.000&currencyId=62'
```

**Source:** `cmp/exchange.js` `updateOffer`.

---

## Delete an offer

`POST /en/economy/exchange/delete/` · Auth required · 🔍 From code

> [!WARNING]
> Removes your offer and returns the held amount.

Parameters: `_token`, `offerId`, `page`, `currencyId`.

The answer has `error`, `message`, `gold`, `ecash`, `buy_mode`, `essentials`, and `edit_mode` only while you still have offers.

```bash
curl 'https://www.erepublik.com/en/economy/exchange/delete/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&offerId=6900001&page=0&currencyId=62'
```

**Source:** `cmp/exchange.js` `deleteOffer`.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| `buyAction` on purchase | Required (ePlus, erepublik-agent send it) | Web script omits it | Both listed |
| `currencyId` type in answers | String | Number | Live response |
| Rate direction for offers selling CC | "1 {sell} = rate {buy}" | Not checked | — |
