# Marketplace

#erepublik #api #economy #marketplace

[< Back to Economy Overview](README.md)

Product marketplace -- browsing offers, filtering by country/industry/quality, and purchasing goods.

**Related:** [My Offers](my-offers.md) | [Inventory](inventory.md) | [Companies](companies.md)

---

## Marketplace Page

**Method:** GET
**URL:** `/en/economy/marketplace`
**Auth Required:** Yes

### Description

The main marketplace HTML page where citizens browse and purchase products. Built with AngularJS (`MarketplaceController`), the page renders a product browser with country/industry/quality filters and an offer table that updates dynamically via the `marketplaceAjax` AJAX endpoint. The page also embeds critical JavaScript variables (`filters`, `industryJSON`, `conditionedIndustries`) that define marketplace configuration.

### Parameters

No URL parameters. The page uses **URL hash routing** to set the initial market view:

| Pattern | Example | Description |
|---------|---------|-------------|
| `#{countryId}/{quality}` | `marketplace#72/1` | Opens market for country 72 (Lithuania) at quality 1 |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/marketplace' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

</details>

### Page Structure

```
body#marketplace_zone
+-- div#marketplace (ng-controller="MarketplaceController")
    +-- h1 "Marketplace"
    +-- div.country-select
    |   +-- select#countryId (74 countries, current country pre-selected)
    +-- div#filters_expanded
    |   +-- h4 "Select product"
    |   +-- div.product_selector
    |   |   +-- ul (9 industry filter links + "Companies" link)
    |   |       +-- li > a.industrySelect#1  ng-click="setIndustry(1)"  -- Food
    |   |       +-- li > a.industrySelect#2  ng-click="setIndustry(2)"  -- Weapons
    |   |       +-- li > a.industrySelect#23 ng-click="setIndustry(23)" -- Aircraft Weapons
    |   |       +-- li > a.industrySelect#3  ng-click="setIndustry(3)"  -- Moving Tickets
    |   |       +-- li > a.industrySelect#4  ng-click="setIndustry(4)"  -- Houses
    |   |       +-- li > a.industrySelect#7  ng-click="setIndustry(7)"  -- Food Raw Materials
    |   |       +-- li > a.industrySelect#12 ng-click="setIndustry(12)" -- Weapon Raw Materials
    |   |       +-- li > a.industrySelect#24 ng-click="setIndustry(24)" -- Aircraft Weapons Raw Materials
    |   |       +-- li > a.industrySelect#17 ng-click="setIndustry(17)" -- House Raw Materials
    |   |       +-- li.last > a (link to /en/economy/buy-company/{countryId}) -- Companies
    |   +-- div#minReq (initially hidden, shown when industry is selected)
    |       +-- div.product_quality_selector.q7
    |           +-- a[quality=1] ng-click="getOffers(1, 1)" -- Q1 (always visible)
    |           +-- a[quality=2] ng-click="getOffers(2, 1)" -- Q2 (hidden by default)
    |           +-- a[quality=3..7] -- Q3-Q7 (hidden by default)
    |           +-- div#product_tip (quality tooltip with effect/durability info)
    +-- div.listing_container
    |   +-- div.default_loading_style (loading spinner)
    |   +-- div.list_products
    |   |   +-- table
    |   |       +-- thead > tr.m_mainh (Product, Seller, Supply, Price, Quantity, Buy, Share)
    |   |       +-- tbody > tr (ng-repeat="item in marketplace track by $index")
    |   |           +-- td.m_product (product icon + import badge)
    |   |           +-- td.m_provider (seller name with hovercard link)
    |   |           +-- td.m_stock (available quantity)
    |   |           +-- td.m_price (price with currency, bestValue tooltip for food/weapons)
    |   |           +-- td.m_quantity (quantity input with +/-/max buttons)
    |   |           +-- td.m_buy (Buy button -> ng-click="buyOffer(...)")
    |   |           +-- td.m_share (copy link button)
    |   +-- ul.pager (pagination, shown when totalPages > 1)
    |       +-- li > a.first (first page)
    |       +-- li > a.prev (previous page)
    |       +-- li (ng-repeat="page in settings.listPagination | limitTo: 5")
    |       +-- li > a.next (next page)
    |       +-- li > a.last (last page)
    +-- [inline scripts with filters, industryJSON, attributeIconPath]
```

### Embedded JavaScript Variables

#### `filters` -- Initial Marketplace State

```json
{
  "countryId": 72,
  "industryId": 1,
  "quality": 1,
  "entityType": "citizen",
  "companyId": 0,
  "orderBy": "price_asc",
  "page": 1
}
```

Used by `MarketplaceController` to initialize the AJAX request to `marketplaceAjax`.

#### `industryJSON` -- Complete Industry Definitions (29 entries)

Defines all 29 industry types with their quality levels, effects, durability, and tokens. This is the master reference for product attributes. Summarized:

| ID | Name | Raw? | Token | Type | Quality Levels |
|----|------|------|-------|------|----------------|
| 1 | Food | No | `FOOD` | grass | Q0-Q7, Q10 (Energy Bar), Q11 (Double Energy Bar), Q12 (Treat), Q13-Q18 (event foods) |
| 2 | Weapons | No | `WEAPON` | stone | Q0-Q7, Q8, Q10 (Bazooka), Q11, Q21-Q22 (Bombs), Q51-Q52, Q100, Q211-Q216 |
| 3 | Moving Tickets | No | `TICKET` | -- | Q1-Q5 |
| 4 | House | No | `HOUSE` | wood | Q1-Q5 (Q6-Q7 defined but effect=0) |
| 5 | Hospital | No | `HOSPITAL` | -- | Q1-Q5 (legacy, not on marketplace) |
| 6 | Defense System | No | `DEFENSE` | -- | Q1-Q5 (legacy, not on marketplace) |
| 7 | Grain | Yes | `GRAIN` | grass | -- |
| 8 | Fruits | Yes | `FRUITS` | grass | -- |
| 9 | Fish | Yes | `FISH` | grass | -- |
| 10 | Cattle | Yes | `CATTLE` | grass | -- |
| 11 | Deer | Yes | `DEER` | grass | -- |
| 12 | Iron | Yes | `IRON` | stone | -- |
| 13 | Oil | Yes | `OIL` | stone | -- |
| 14 | Aluminum | Yes | `ALUMINUM` | stone | -- |
| 15 | Saltpeter | Yes | `SALTPETER` | stone | -- |
| 16 | Rubber | Yes | `RUBBER` | stone | -- |
| 17 | Sand | Yes | `SAND` | wood | -- |
| 18 | Clay | Yes | `CLAY` | wood | -- |
| 19 | Wood | Yes | `WOOD` | wood | -- |
| 20 | Gift | No | `GIFT` | -- | Q1, Q11, Q12 |
| 21 | Limestone | Yes | `LIMESTONE` | wood | -- |
| 22 | Granite | Yes | `GRANITE` | wood | -- |
| 23 | Aircraft Weapons | No | `AIRCRAFT` | composites | Q1-Q7, Q10 (AIM-92 Stinger), Q51 (AIM-7 Grid-Lock) |
| 24 | Magnesium | Yes | `MAGNESIUM` | composites | -- |
| 25 | Titanium | Yes | `TITANIUM` | composites | -- |
| 26 | Wolfram | Yes | `WOLFRAM` | composites | -- |
| 27 | Cobalt | Yes | `COBALT` | composites | -- |
| 28 | Neodymium | Yes | `NEODYMIUM` | composites | -- |
| 100 | Booster | No | `BOOSTER` | -- | Q1 |

> **Important:** `industryJSON` defines theoretical quality levels (e.g., Aircraft Weapons Q1-Q7), but the **marketplace only sells** up to Q5 for Aircraft Weapons, Moving Tickets, and Houses. Higher qualities (Q6+) are special items obtained through other means.

#### `conditionedIndustries` -- Minimum Quality Requirements

```json
{"1": 2, "2": 5}
```

Maps industry ID -> minimum quality the citizen should buy. Used by the UI to show warnings/recommendations:
- Food (1): minimum Q2 recommended
- Weapons (2): minimum Q5 recommended

#### `empires` -- Empire Countries

```json
{"35": 1}
```

Maps country ID -> empire status. Empire countries (e.g., Poland=35) may have special marketplace rules or tax structures.

#### Other Variables

| Variable | Type | Description |
|----------|------|-------------|
| `csrfToken` | string | CSRF token for POST requests (same as `_token` parameter) |
| `linkForBuy` | string | Default buy link with hash routing: `/en/economy/marketplace#72/1` |
| `textForBuy` | string | "Buy food" -- CTA label |
| `food_remaining` | number | Remaining food consumption limit for the day |
| `smallestFood` | object | `{"q":1,"use":2}` -- lowest quality food and its energy restore |
| `isOrganization` | number | 0 = citizen, 1 = organization account |
| `foodIndustryId` | number | 1 |
| `weaponIndustryId` | number | 2 |
| `houseIndustryId` | number | 4 |
| `citizenshipCountry` | number | Citizen's citizenship country ID |
| `sharedOffer` | boolean | `false` -- whether page was loaded via a shared offer link |
| `attributeIconPath` | string | `//www.erepublik.net/images/icons/industry` -- base path for product icons |

### Raw Material Resource Groups

The `type` field in `industryJSON` attributes groups raw materials by their parent finished product:

| Type | Finished Product | Raw Material IDs |
|------|-----------------|------------------|
| `grass` | Food (1) | 7 (Grain), 8 (Fruits), 9 (Fish), 10 (Cattle), 11 (Deer) |
| `stone` | Weapons (2) | 12 (Iron), 13 (Oil), 14 (Aluminum), 15 (Saltpeter), 16 (Rubber) |
| `wood` | Houses (4) | 17 (Sand), 18 (Clay), 19 (Wood), 21 (Limestone), 22 (Granite) |
| `composites` | Aircraft (23) | 24 (Magnesium), 25 (Titanium), 26 (Wolfram), 27 (Cobalt), 28 (Neodymium) |

On the marketplace, each group is sold under a **single aggregate ID** (the first resource in each group: 7, 12, 17, 24).

### Product Quality Effects

Key product attribute data from `industryJSON`:

**Food (energy restored per use):**
| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 |
|----|----|----|----|----|----|----|
| 2 | 4 | 6 | 8 | 10 | 12 | 20 |

**Weapons (fire power / durability in uses):**
| Q1 | Q2 | Q3 | Q4 | Q5 | Q6 | Q7 |
|----|----|----|----|----|----|----|
| 20/1 | 40/2 | 60/3 | 80/4 | 100/5 | 120/6 | 200/10 |

**Houses (energy pool / durability in hours / recovery per 6 min):**
| Q1 | Q2 | Q3 | Q4 | Q5 |
|----|----|----|----|----|
| 100/168h/+2 | 160/168h/+2 | 200/168h/+2 | 300/168h/+2 | 400/168h/+2 |

**Aircraft Weapons (air fire power / durability in uses):**
| Q1 | Q2 | Q3 | Q4 | Q5 |
|----|----|----|----|----|
| 20/1 | 40/2 | 60/3 | 80/4 | 100/5 |

**Moving Tickets (energy cost / moving distance in zones):**
| Q1 | Q2 | Q3 | Q4 | Q5 |
|----|----|----|----|----|
| -4/1000 | -3/2000 | -2/3000 | -1/4000 | 0/5000 |

### Stylesheets & Scripts

| Resource | Purpose |
|----------|---------|
| `marketplace.{buildId}.css` | Marketplace-specific styles |
| `marketplace.{buildId}.js` | MarketplaceController and marketplace logic |
| `selector.{buildId}.css` | Country/quality selector styles |
| `select2.{buildId}.js` | Enhanced dropdown (country select) |

### Notes

- **AngularJS Framework:** The page uses AngularJS 1.8.2 (`ng-app="ErpkApp"`). The marketplace functionality is in the `MarketplaceController` which manages state, AJAX calls, and DOM updates.

- **Country Dropdown:** The `<select#countryId>` contains all 74 countries with flag image paths as `title` attributes. The citizen's current country is pre-selected with `selected="selected"`. Changing the country triggers `erepublik.marketplace.redirectToMarket()`.

- **Industry Icons:** Each industry filter displays an icon from `https://www.erepublik.net/images/icons/industry/{id}/default.png` with a tooltip describing the product's purpose.

- **Quality Selector:** Initially hidden (`div#minReq style="display:none"`). Shown when an industry is selected. Quality links Q2-Q7 are hidden by default and revealed based on the selected industry's max quality. The quality tooltip (`div#product_tip`) shows effect, durability, and rest points.

- **Offer Table Columns:**
  - **Product:** Icon with quality indicator; imported products show origin country flag
  - **Seller:** Citizen name (hovercard-enabled link to profile)
  - **Supply:** Available quantity
  - **Price:** Price with taxes, with `bestValue` tooltip for Food (cost per energy) and Weapons (cost per use)
  - **Quantity:** Input field with decrease (-), increase (+), and maximum buttons
  - **Buy:** Blue button calling `buyOffer(item.id, item.valueToBuy, item.amount)`
  - **Share:** Copy offer link button

- **Price Display Logic:** Food (industry 1) shows a `bestValue` tooltip in "X currency / energy" format. Weapons (industry 2) show "X currency / use" format. Other industries show price without best-value calculation.

- **Pagination:** Shown only when `settings.totalPages > 1`. Displays up to 5 page numbers with first/prev/next/last navigation.

- **Data Flow:**
  1. Page loads with `filters` variable defining initial state
  2. `MarketplaceController` reads `filters` and calls `POST /en/economy/marketplaceAjax`
  3. Response populates `marketplace` array, rendered by `ng-repeat`
  4. User interactions (industry change, quality change, pagination) trigger new AJAX calls
  5. Buy actions call `POST /en/economy/marketplaceActions`

- **CSRF Token:** Available as `csrfToken` global variable and also in `SERVER_DATA.csrfToken`. Required for all POST requests.

- **Shared Offers:** The page supports deep-linking to specific offers via URL hash (`marketplace#{countryId}/{quality}`). The `sharedOffer` variable indicates if the page was loaded from a shared link.

---

## Get Marketplace Offers

**Method:** POST
**URL:** `/en/economy/marketplaceAjax`
**Auth Required:** Yes

### Description

Retrieves filtered and sorted marketplace offers for a specific product type. Supports country filtering, product quality selection, pagination, and various sorting options. Returns active sell offers with seller details, pricing, and availability.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryId | number | Yes | Country ID where the market is located (e.g., 72 for Lithuania) |
| industryId | number | Yes | Product industry type (see Industry IDs table in Notes) |
| quality | number | Yes | Product quality level (see Quality Ranges table in Notes) |
| orderBy | string | Yes | Sort order: `price_asc`, `price_desc`, `amount_asc`, `amount_desc` |
| currentPage | number | Yes | Page number for pagination (starts at 1) |
| ajaxMarket | number | Yes | AJAX flag, must be set to `1` |
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
curl -X POST 'https://www.erepublik.com/en/economy/marketplaceAjax' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'countryId=72&industryId=1&quality=1&orderBy=price_asc&currentPage=1&ajaxMarket=1&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "offers": [
    {
      "id": 77924504,
      "country_id": 72,
      "industry_id": 1,
      "citizen_id": 4641359,
      "amount": 119998,
      "price": 0.861386,
      "customization_level": 1,
      "name": "Z1mo",
      "is_for_export": 0,
      "priceWithTaxes": 0.87
    },
    {
      "id": 77838777,
      "country_id": 72,
      "industry_id": 1,
      "citizen_id": 2475653,
      "amount": 4025,
      "price": 0.915094,
      "customization_level": 1,
      "name": "chudziaczek",
      "is_for_export": 35,
      "priceWithTaxes": 0.97
    },
    {
      "id": 77904984,
      "country_id": 72,
      "industry_id": 1,
      "citizen_id": 5498697,
      "amount": 135552,
      "price": 0.960396,
      "customization_level": 1,
      "name": "Tepliotojas",
      "is_for_export": 0,
      "priceWithTaxes": 0.97
    }
  ],
  "currency": "LTL",
  "can_buy": true,
  "pages": 1
}
```

</details>

### Notes

- **Industry IDs (9 tradeable industries):**

  | ID | Industry | Type | Quality Range | Token |
  |----|----------|------|---------------|-------|
  | 1 | Food | Finished product | Q1-Q7 | `FOOD` |
  | 2 | Weapons (Ammunition) | Finished product | Q1-Q7 | `WEAPON` |
  | 3 | Moving Tickets | Finished product | Q1-Q5 | `TICKET` |
  | 4 | Houses | Finished product | Q1-Q5 | `HOUSE` |
  | 23 | Aircraft Weapons | Finished product | Q1-Q5 | `AIRCRAFT` |
  | 7 | Food Raw Materials | Raw material | Q1 only | `GRAIN` |
  | 12 | Weapon Raw Materials | Raw material | Q1 only | `IRON` |
  | 17 | House Raw Materials | Raw material | Q1 only | `SAND` |
  | 24 | Aircraft Raw Materials | Raw material | Q1 only | `MAGNESIUM` |

  > IDs 5-6, 8-11, 13-16, 18-22, 25+ are **not valid** for the marketplace. The `industryJSON` variable on the marketplace page defines 29 industry entries total (including individual resource types like Grain, Fish, Iron, etc.), but only these 9 appear as marketplace filters.
  >
  > Raw material IDs use the **first resource** of each category as the aggregate marketplace ID (7=Grain->Food RM, 12=Iron->Weapon RM, 17=Sand->House RM, 24=Magnesium->Aircraft RM).

- **Quality Ranges:**
  - Food, Weapons: Q1-Q7
  - Moving Tickets, Houses, Aircraft Weapons: Q1-Q5
  - All raw materials: Q1 only (no quality tiers)
  - Special items exist at higher quality numbers (e.g., Q10=Energy Bar, Q10=Bazooka) but are **not sold** via this marketplace endpoint -- they appear in inventory as separate item types

- **Country Filtering:**
  - `countryId` determines which country's market to search
  - Each country has its own marketplace with local currency
  - Prices are displayed in the country's currency (e.g., LTL, USD, EUR, RON)

- **Sorting Options:**
  - `price_asc`: Lowest price first (most common for buyers)
  - `price_desc`: Highest price first
  - `amount_asc`: Lowest stock first
  - `amount_desc`: Highest stock first

- **Offer Fields:**
  - `id`: Unique marketplace offer ID
  - `citizen_id`: Seller's citizen ID
  - `name`: Seller's display name
  - `amount`: Available quantity in stock
  - `price`: Base price per unit (before taxes)
  - `priceWithTaxes`: Final price including import/VAT taxes
  - `is_for_export`: Export tax percentage (0 if no export tax, >0 indicates export offer)
  - `customization_level`: Product quality level (1-7)

- **Pagination:**
  - `pages`: Total number of pages available
  - `currentPage`: Current page being viewed
  - Typically 5-10 offers per page

- **Purchase Eligibility:**
  - `can_buy`: `true` if the citizen is eligible to make purchases
  - May be `false` if banned, restricted, or lacking required citizenship

- **Tax Calculations:**
  - Import tax applies when buying from foreign countries
  - VAT applies based on country settings
  - `priceWithTaxes` includes all applicable taxes
  - Export tax (`is_for_export`) is paid by the seller but affects buyer price

- The CSRF `_token` can be obtained from the main marketplace page source
- This endpoint is used by the marketplace UI for dynamic filtering without page reloads
- Response times are typically fast (<100ms) due to database indexing

---

## Buy from Marketplace

**Method:** POST
**URL:** `/en/economy/marketplaceActions`
**Auth Required:** Yes

### Description

Executes a purchase transaction on the marketplace. Buys a specified quantity of products from a specific offer, deducts currency from the citizen's account, adds products to storage, and returns updated marketplace offers and account balances.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| offerId | number | Yes | Unique ID of the marketplace offer to purchase from |
| amount | number | Yes | Quantity of products to buy (must not exceed offer's available amount) |
| orderBy | string | Yes | Sort order for refreshed offers: `price_asc`, `price_desc`, `amount_asc`, `amount_desc` |
| currentPage | number | Yes | Current page number for refreshed offer list |
| buyAction | number | Yes | Action flag, must be set to `1` for buy action |
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
curl -X POST 'https://www.erepublik.com/en/economy/marketplaceActions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'offerId=77924504&amount=1&orderBy=price_asc&currentPage=1&buyAction=1&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "message": "You have successfully bought 1 product for 0.87 LTL.",
  "offerUpdate": {
    "offers": [
      {
        "id": 77924504,
        "country_id": 72,
        "industry_id": 1,
        "citizen_id": 4641359,
        "amount": 119996,
        "price": 0.861386,
        "customization_level": 1,
        "name": "Z1mo",
        "is_for_export": 0,
        "priceWithTaxes": 0.87
      },
      {
        "id": 77838777,
        "country_id": 72,
        "industry_id": 1,
        "citizen_id": 2475653,
        "amount": 4025,
        "price": 0.915094,
        "customization_level": 1,
        "name": "chudziaczek",
        "is_for_export": 35,
        "priceWithTaxes": 0.97
      }
    ],
    "currency": "LTL",
    "can_buy": true,
    "pages": 1
  },
  "currency": 6274006,
  "gold": 72916
}
```

</details>

### Notes

- **Transaction Processing:**
  - Deducts `amount x priceWithTaxes` from citizen's currency balance
  - Adds purchased products to citizen's storage
  - Updates the offer's available amount (decreased by purchase quantity)
  - If offer amount reaches 0, it's removed from marketplace

- **Response Fields:**
  - `error`: `false` on success, `true` on failure
  - `message`: Success message with purchase details (quantity, total cost, currency)
  - `offerUpdate`: Refreshed marketplace offers list (same format as `marketplaceAjax` endpoint)
  - `currency`: Updated citizen's local currency balance (actual value with decimals, e.g. `46261.07` = 46,261.07 PLN)
  - `gold`: Updated citizen's Gold balance (actual value with decimals, e.g. `0.01` = 0.01 Gold)

- **Currency Handling:**
  - Purchases are made in the market country's local currency
  - If citizen lacks sufficient currency, the transaction fails with an error
  - Currency and Gold balances are returned as actual values with decimals (e.g., `46261.07` = 46,261.07 PLN). Verified 2026-02-28 -- NOT in hundredths.

- **Offer Updates:**
  - The `offerUpdate.offers` array reflects the updated marketplace after purchase
  - The purchased offer's `amount` is decreased by the purchase quantity
  - Offers are re-sorted according to the `orderBy` parameter
  - This allows the UI to refresh without making a separate `marketplaceAjax` call

- **Error Cases:**
  - Insufficient currency: `"error": true, "message": "You don't have enough money"`
  - Insufficient stock: `"error": true, "message": "Insufficient stock available"`
  - Invalid offer ID: `"error": true, "message": "Offer not found"`
  - Storage full: `"error": true, "message": "Your storage is full"`
  - Invalid CSRF token: Returns HTTP 403 or token validation error

- **Storage Management:**
  - Purchased items are automatically added to citizen's storage
  - Storage has a capacity limit (default 5000 units, expandable via Shop)
  - Each product type (Food Q1, Weapons Q5, etc.) counts toward the limit
  - If storage is full, the purchase will fail

- **Rate Limiting:**
  - No explicit rate limit, but rapid purchases may trigger anti-bot checks
  - Recommended to add small delays between automated purchases

- The CSRF `_token` can be obtained from the marketplace page source
- This endpoint is typically called after selecting an offer from the `marketplaceAjax` results
- The `orderBy` and `currentPage` parameters ensure UI state is maintained after purchase
