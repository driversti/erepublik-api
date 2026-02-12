# eRepublik API - Country

#erepublik #api #country #politics #economy

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Country endpoints cover country-specific pages and data, including society statistics, political structure, economy, and military information for individual countries.

---

## Endpoints

### Country Society Page

**Method:** GET
**URL:** `/en/country/society/{countryName}`
**Auth Required:** No

#### Description

Retrieves the country society page as HTML. This is a **web page endpoint**, not a JSON API. The page displays citizen statistics (active citizens, new citizens, average level, online count) for the country compared to the world, and a full list of regions currently controlled by the country — including capital marker, determination multipliers for occupied regions, and transport route warnings.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Bosnia-Herzegovina`). Case-sensitive. Multi-word names use hyphens |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (avoids 403 from Cloudflare) |

#### Example Request (cURL)

```bash
curl -X GET 'https://www.erepublik.com/en/country/society/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

#### Example Response

Returns a full HTML page (`<body id="country">`) with the following sections:

```html
<!DOCTYPE html>
<html ng-app="ErpkApp" lang="en">
<head>
  <title>eRepublik Country Society | USA</title>
  <meta name="description" content="USA is a country in Erepublik. Find information about the social activities of USA" />
  ...
</head>
<body id="country" class="logged_out lowered">
  ...
</body>
</html>
```

#### Page Sections (parsed from HTML)

##### 1. Country Header

Displays the country flag, name, rank, and alliance membership.

```html
<div id="profileholder">
    <h1>USA</h1>
    <p class="padded">
        <a href="//wiki.erepublik.com/index.php/USA" target="_blank"><!-- Wiki link --></a>
        <a class="country_rank" href="//www.erepublik.com/en/main/rankings-countries/1/1">Rank 11</a>
        <a class="alliance_name" href="//www.erepublik.com/en/main/alliances/7" title="Asteria">
            <span>Asteria</span>
        </a>
    </p>
</div>
```

| Data | CSS Selector / Pattern | Example |
|------|----------------------|---------|
| Country name | `#profileholder h1` | `USA` |
| Country rank | `a.country_rank` | `Rank 11` |
| Alliance name | `a.alliance_name span` | `Asteria` |
| Alliance ID | `a.alliance_name` href → `/en/main/alliances/{allianceId}` | `7` |
| Wiki link | `a[href*="wiki.erepublik.com"]` | `//wiki.erepublik.com/index.php/USA` |
| Country flag | `#filters .bigcore img` src → `/images/flags/XL/{countryName}.gif` | `USA.gif` |

##### 2. Navigation Tabs

Five tabs linking to other country pages:

| Tab | URL Pattern |
|-----|-------------|
| **Society** (current) | `/en/country/society/{countryName}` |
| Economy | `/en/country/economy/{countryName}` |
| Politics | `/en/country/politics/{countryName}` |
| Military | `/en/country/military/{countryName}` |
| Administration | `/en/country-administration/{countryName}/1` |

> **Note:** The Administration tab uses a different URL pattern (`/en/country-administration/` instead of `/en/country/`).

##### 3. Citizens Statistics Table

A table comparing country stats to world stats. Parse from `table.citizens`:

```html
<table class="citizens largepadded">
    <tr>
        <th></th><th></th><th>USA</th><th>World</th>
    </tr>
    <tr>
        <td>...</td>
        <td><span class="fakeheight">Active citizens</span></td>
        <td><span class="special">980</span></td>
        <td><span class="special">32,764</span></td>
    </tr>
    ...
</table>
```

| Metric | CSS Class | Country Value | World Value |
|--------|-----------|---------------|-------------|
| Active citizens | `span.special` | 980 | 32,764 |
| New citizens today | `span.special` | 5 | 59 |
| Citizenship requests | Link to `/en/country/{countryName}/citizenship/applications/1` | — | — |
| Average citizen level | `span.bg-level-green` | 283 | 187 |
| Online now | `span.special` + link to online users page | 39 | 1,130 |

Related URL patterns:
- Online users (country): `/en/main/online-users/{countryName}/all/1`
- Online users (world): `/en/main/online-users/all/all/1`
- Citizenship applications: `/en/country/{countryName}/citizenship/applications/1`

##### 4. Regions Table

Lists all regions currently controlled by the country. Parse from `table.regions`. Total count shown in the `<h2>` header:

```html
<h2 class="section" id="currentRegions">Regions (31)</h2>
```

Each region row can contain:

**a) Standard region:**
```html
<td>
    <a href="//www.erepublik.com/en/main/region/Colorado" class="fakeheight">Colorado</a>
</td>
```

**b) Capital region** (marked with `span.icon-capital`):
```html
<td>
    <span class="goleft smallholder">
        <a href="//www.erepublik.com/en/main/region/District-of-Columbia" class="fakeheight goleft rightpadded">
            District of Columbia
        </a>
        <span class="icon-capital">Capital</span>
    </span>
</td>
```

**c) Occupied region with determination multiplier** (foreign territory):
```html
<td>
    <span class="goright influence_multiplier inactive"
          title="This region's Determination to be free. Soldiers will receive x3.78 Influence when fighting for its freedom.">
        3.78<span></span>
    </span>
    <span class="route_container">
        <strong class="route_warning">No direct transport route to the Capital</strong>
    </span>
    <a href="//www.erepublik.com/en/main/region/Guangdong" class="fakeheight">Guangdong</a>
</td>
```

**d) Region with no transport route to capital** (disconnected territory):
```html
<td>
    <span class="route_container">
        <strong class="route_warning">No direct transport route to the Capital</strong>
    </span>
    <a href="//www.erepublik.com/en/main/region/Alaska" class="fakeheight">Alaska</a>
</td>
```

| Element | CSS Selector | Description |
|---------|-------------|-------------|
| Region name | `a.fakeheight` | Region display name |
| Region link | `a.fakeheight` href → `/en/main/region/{regionPermalink}` | Link to region details |
| Capital marker | `span.icon-capital` | Present only on the capital region |
| Determination | `span.influence_multiplier` | Multiplier for occupied foreign regions (e.g., `3.78`) |
| No route warning | `strong.route_warning` | Present when region is disconnected from capital |

##### 5. Country Selector Dropdown

A `<select>` dropdown containing **all countries** with their IDs and permalink names. This is a valuable reference for country ID mapping:

```html
<select id="countrySelectorDropdown">
    <option value="167" data-permalink="Albania">Albania</option>
    <option value="27" data-permalink="Argentina">Argentina</option>
    ...
    <option value="24" data-permalink="USA" selected>USA</option>
    <option value="28" data-permalink="Venezuela">Venezuela</option>
</select>
```

Complete country ID reference (extracted from dropdown):

| ID | Permalink | Name |
|----|-----------|------|
| 1 | Romania | Romania |
| 9 | Brazil | Brazil |
| 10 | Italy | Italy |
| 11 | France | France |
| 12 | Germany | Germany |
| 13 | Hungary | Hungary |
| 14 | China | China |
| 15 | Spain | Spain |
| 23 | Canada | Canada |
| 24 | USA | USA |
| 26 | Mexico | Mexico |
| 27 | Argentina | Argentina |
| 28 | Venezuela | Venezuela |
| 29 | United-Kingdom | United Kingdom |
| 30 | Switzerland | Switzerland |
| 31 | Netherlands | Netherlands |
| 32 | Belgium | Belgium |
| 33 | Austria | Austria |
| 34 | Czech-Republic | Czech Republic |
| 35 | Poland | Poland |
| 36 | Slovakia | Slovakia |
| 37 | Norway | Norway |
| 38 | Sweden | Sweden |
| 39 | Finland | Finland |
| 40 | Ukraine | Ukraine |
| 41 | Russia | Russia |
| 42 | Bulgaria | Bulgaria |
| 43 | Turkey | Turkey |
| 44 | Greece | Greece |
| 45 | Japan | Japan |
| 47 | South-Korea | South Korea |
| 48 | India | India |
| 49 | Indonesia | Indonesia |
| 50 | Australia | Australia |
| 51 | South-Africa | South Africa |
| 52 | Republic-of-Moldova | Republic of Moldova |
| 53 | Portugal | Portugal |
| 54 | Ireland | Ireland |
| 55 | Denmark | Denmark |
| 56 | Iran | Iran |
| 57 | Pakistan | Pakistan |
| 58 | Israel | Israel |
| 59 | Thailand | Thailand |
| 61 | Slovenia | Slovenia |
| 63 | Croatia | Croatia |
| 64 | Chile | Chile |
| 65 | Serbia | Serbia |
| 66 | Malaysia | Malaysia |
| 67 | Philippines | Philippines |
| 68 | Singapore | Singapore |
| 69 | Bosnia-Herzegovina | Bosnia and Herzegovina |
| 70 | Estonia | Estonia |
| 71 | Latvia | Latvia |
| 72 | Lithuania | Lithuania |
| 73 | North-Korea | North Korea |
| 74 | Uruguay | Uruguay |
| 75 | Paraguay | Paraguay |
| 76 | Bolivia | Bolivia |
| 77 | Peru | Peru |
| 78 | Colombia | Colombia |
| 79 | North-Macedonia | North Macedonia |
| 80 | Montenegro | Montenegro |
| 81 | Republic-of-China-Taiwan | Republic of China (Taiwan) |
| 82 | Cyprus | Cyprus |
| 83 | Belarus | Belarus |
| 84 | New-Zealand | New Zealand |
| 164 | Saudi-Arabia | Saudi Arabia |
| 165 | Egypt | Egypt |
| 166 | United-Arab-Emirates | United Arab Emirates |
| 167 | Albania | Albania |
| 168 | Georgia | Georgia |
| 169 | Armenia | Armenia |
| 170 | Nigeria | Nigeria |
| 171 | Cuba | Cuba |

> **Note:** IDs are not sequential — gaps exist (e.g., no IDs 2-8, 16-22, 25, 46, 60, 62, 85-163). Total: **74 countries**. See [API_REFERENCE.md](API_REFERENCE.md) for the complete country and region reference tables.

#### JavaScript Variables in Response

The HTML page includes embedded JavaScript with useful data in a `<script>` block in `<head>`:

##### `erepublik` object

```javascript
var erepublik = {
  "settings": {
    "domainname": "erepublik.com",
    "hostname": "www.erepublik.com",
    "economyhostname": "economy.erepublik.com",
    "cookielessHost": "www.erepublik.net",
    "isLive": true,
    "culture": "en",
    "eDay": 6654,
    "isOnHomepage": false,
    "requestUri": "L2VuL2NvdW50cnkvc29jaWV0eS9VU0E"
  },
  "shortcuts": {
    "links": {
      "inventory": "/en/main/inventory",
      "myland": "//www.erepublik.com/en/economy/myCompanies",
      "campaigns": "//www.erepublik.com/en/military/campaigns",
      "top_news": "//www.erepublik.com/en/news/international",
      "new_message": "//www.erepublik.com/en/main/messages-compose/0",
      "world_map": "//www.erepublik.com/en/map",
      "military_unit": "//www.erepublik.com/en/main/group-home/military",
      "alerts": "//www.erepublik.com/en/main/notifications",
      "residence": "//www.erepublik.com/en/main/myResidence",
      "armory": "//www.erepublik.com/en/military/armory"
    }
  },
  "angularTemplates": { "...": "template paths" }
};
```

| Field | Description |
|-------|-------------|
| `settings.eDay` | Current game day (e.g., `6654`) |
| `settings.culture` | Language code (`en`) |
| `settings.requestUri` | Base64-encoded request URI |
| `shortcuts.links` | Navigation shortcut URLs (useful for discovering other endpoints) |

##### `SERVER_DATA` object

```javascript
SERVER_DATA = {
  "csrfToken": "201298b5a407cd9ab1c396f08b9dadc2",
  "isLoggedIn": false,
  "fbAppId": 300511377223,
  "extras": "winter treats",
  "serverTime": {
    "rfc": "Sat, 07 Feb 2026 04:20:21 -0800",
    "dateTime": "2026-02-07 04:20:21",
    "timezone": "PST",
    "offset": -800,
    "hour": 4,
    "unix": 1770466821,
    "unixMs": 1770466821000
  },
  "webDeployEnabled": true
};
```

##### `Environment` object

```javascript
Environment = {
  "facebookSession": 0,
  "isInIframe": false,
  "isOnFacebook": false,
  "isWebView": false,
  "isOnHomepage": false,
  "isSymfony": false,
  "logEnabled": false,
  "hasSessionCaptcha": false,
  "sessionId": "e4113d9387cc072d8550f001f460f1a0"
};
```

##### Other variables

```javascript
ErpkShop = {"promotions":[],"popupPromotions":[],"battlePromotions":[],"numberOfPurchasablePacks":0,"specialSalesEvent":false};
missionsJSON = [];
numberOfMissions = 0;
dotNotifications = [];

var countrySelector = {
    placeholderText: "Choose a country...",
    urlTab: "country/society"
};
```

#### Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/main/rankings-countries/1/1` | Country rankings page |
| `/en/main/alliances/{allianceId}` | Alliance details page |
| `/en/main/region/{regionPermalink}` | Region details page |
| `/en/main/online-users/{countryName}/all/1` | Online users for a country |
| `/en/main/online-users/all/all/1` | All online users (world) |
| `/en/country/{countryName}/citizenship/applications/1` | Citizenship applications |
| `/en/country-administration/{countryName}/1` | Country administration page |

#### Response Headers (notable)

| Header | Value | Notes |
|--------|-------|-------|
| `Set-Cookie` | `erpk=...` (15min expiry) | Session cookie set even for unauthenticated requests |
| `Set-Cookie` | `erpk_plang=en` (30 day expiry) | Language preference cookie |
| `Server` | Cloudflare (via Varnish 7.1) | May trigger challenges for automated requests |

#### Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible without authentication (but Cloudflare may challenge bot-like requests)
- **No AJAX sub-requests** — unlike some pages, all society data is rendered server-side in the HTML; no additional XHR calls needed
- The country selector dropdown is present on all country tab pages and contains the **complete country ID ↔ permalink mapping** — ideal for building a country reference table
- Regions listed include **foreign territories** the country currently occupies (e.g., USA holding "Guangdong", "Eastern Siberia") — these have determination multipliers
- Determination multiplier values (e.g., `3.78`) represent the influence bonus enemies get when fighting to liberate that region
- The `route_warning` ("No direct transport route to the Capital") appears on regions geographically disconnected from the capital — affects travel mechanics
- Country name in URL uses the **permalink format**: multi-word names use hyphens (e.g., `Bosnia-Herzegovina`, `United-Kingdom`, `Republic-of-China-Taiwan`)
- Page structure uses AngularJS 1.8.2 framework
- Cloudflare challenge platform script is injected at the bottom of the page (may affect scraping)

---

### Country Economy Page

**Method:** GET
**URL:** `/en/country/economy/{countryName}`
**Auth Required:** No

#### Description

Retrieves the country economy page as HTML. This is the **richest country page for scraping** — it contains three major JavaScript data variables (`chartDataJSON`, `gdpChartData`, `countryProductivityBonuses`) plus extensive HTML tables covering treasury, resources, taxes, embargoes, concessions, and salary data.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Bosnia-Herzegovina`). Case-sensitive, multi-word names use hyphens |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (avoids 403 from Cloudflare) |

#### Example Request (cURL)

```bash
curl -X GET 'https://www.erepublik.com/en/country/economy/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

#### Example Response

Returns a full HTML page (`<body id="country">`) wrapped in `<div id="economy">`:

```html
<!DOCTYPE html>
<html ng-app="ErpkApp" lang="en">
<head>
  <title>eRepublik Country Economy | USA</title>
  <meta name="description" content="USA is a country in Erepublik. Find information about the economical activities of USA" />
  ...
</head>
<body id="country" class="logged_out lowered">
  ...
</body>
</html>
```

#### Page Sections (parsed from HTML)

##### 1. Treasury

Displays current Gold, local currency, and Energy reserves. Parse from `table.donation_status_table`:

```html
<table class="donation_status_table">
    <tr>
        <td align="right">
            <span class="special">1,263</span>
            <sup>.42</sup>
        </td>
        <td class="c2">
            <img src="//www.erepublik.net/images/parts/icon-gold.gif" alt="Gold" />
            <span class="currency">GOLD</span>
        </td>
    </tr>
    <tr>
        <td align="right">
            <span class="special">1,448,093,816</span>
            <sup>.58</sup>
        </td>
        <td class="c2">
            <img src="//www.erepublik.net/images/flags/M/USA.gif" title="USD" alt="USD"/>
            <span class="currency">USD</span>
        </td>
    </tr>
    <tr>
        <td align="right">
            <span class="special">34,090</span>
        </td>
        <td class="c2">
            <img src="//www.erepublik.net/images/modules/sidebar/energy_bar_icon2.png" alt="Energy" />
            <span class="currency">ENERGY</span>
        </td>
    </tr>
</table>
```

| Resource | CSS Selector | Example Value |
|----------|-------------|---------------|
| Gold | `span.special` + `sup` (row 1) | 1,263.42 |
| Local Currency | `span.special` + `sup` (row 2) | 1,448,093,816.58 USD |
| Energy | `span.special` (row 3, no `sup`) | 34,090 |

> **Parsing note:** Gold and Currency have decimal parts in `<sup>` tags. Energy has no decimals. The currency code (e.g., `USD`, `LTL`) comes from the `span.currency` text and the flag image (`/images/flags/M/{countryName}.gif`).

##### 1b. Treasury Donation Form (Logged-in Only)

When authenticated (level 25+), a "Contribute" button appears below the treasury table. Clicking it reveals a donation form with three rows: Gold, Currency, and Food. The form also includes a food quality selector (q1-q7).

```html
<a class="donate_open std_global_btn mediumSize blueColor"
   href="javascript:" title="Contribute"><span>Contribute</span></a>

<table class="donate_table in_economy" cellpading="0" cellspacing="0">
    <tr>
        <!-- Gold donation -->
        <td align="right">
            <span class="special" id="currentGoldValue">26</span><sup>.1</sup>
        </td>
        <td><b class="golden">Gold:</b></td>
        <td><input type="text" id="gold_amount" name="gold_amount" value="1" /></td>
        <td>
            <img src="//www.erepublik.net/images/parts/icon-gold.gif" alt="Gold" />
            <span class="currency">GOLD</span>
        </td>
        <td>
            <a class="std_global_btn smallSize blueColor" href="javascript:"
               onclick="donateGold_onClick_eTab();" title="Contribute">
                <span>Contribute</span>
            </a>
        </td>
    </tr>
    <tr>
        <!-- Currency donation -->
        <td align="right">
            <span class="special" id="currentCurrencyValue">2,942,730</span><sup>.34</sup>
        </td>
        <td><b class="orange">Currency:</b></td>
        <td><input type="text" id="currency_amount" name="currency_amount" value="20" /></td>
        <td>
            <span class="currency_cl">
                <img src="//www.erepublik.net/images/flags_png/XL/Lithuania.png"
                     alt="" width="24" /><strong>LTL</strong>
            </span>
        </td>
        <td>
            <a class="std_global_btn smallSize blueColor" href="javascript:"
               onclick="donateCurrency_onClick_eTab();" title="Contribute">
                <span>Contribute</span>
            </a>
        </td>
    </tr>
    <tr>
        <!-- Food donation with quality selector -->
        <td align="right">
            <span class="special" id="currentFoodValue">35,790</span>
        </td>
        <td><b class="green">Food:</b></td>
        <td><input type="text" id="food_amount" name="food_amount" value="10" /></td>
        <td>
            <a class="prev_food" href="javascript:">Prev</a>
            <div class="food_type">
                <img src=".../q1_30x30.png" data-quality="1" alt="" />
                <!-- ... q2 through q7 ... -->
                <img src=".../q7_30x30.png" data-quality="7" alt="" />
                <input type="hidden" value="0" id="img_active" name="img_active" />
            </div>
            <a class="next_food" href="javascript:">Next</a>
        </td>
        <td>
            <a class="std_global_btn smallSize blueColor" href="javascript:"
               onclick="donateFood_onClick_eTab();" title="Contribute">
                <span>Contribute</span>
            </a>
        </td>
    </tr>
</table>
```

| Element | ID / Selector | Description |
|---------|--------------|-------------|
| Gold input | `input#gold_amount` | Amount of gold to donate |
| Currency input | `input#currency_amount` | Amount of local currency to donate |
| Food input | `input#food_amount` | Amount of food units to donate |
| Food quality | `input#img_active` (hidden) | Selected food quality (0-indexed display, `data-quality` 1-7) |
| Gold donate button | `onclick="donateGold_onClick_eTab()"` | JS function for gold donation |
| Currency donate button | `onclick="donateCurrency_onClick_eTab()"` | JS function for currency donation |
| Food donate button | `onclick="donateFood_onClick_eTab()"` | JS function for food donation |
| Current gold | `span#currentGoldValue` | Current treasury gold (updates after donation) |
| Current currency | `span#currentCurrencyValue` | Current treasury currency (updates after donation) |
| Current food/energy | `span#currentFoodValue` | Current treasury energy (updates after donation) |

> **Note:** The donation form uses `_eTab` suffix on function names (economy tab). The Military page's airstrike section uses the same form but without Gold and without the `_eTab` suffix (uses `donateFood_onClick()` and `donateCurrency_onClick()`). The currency flag shown is the **viewer's citizenship currency** flag (e.g., Lithuanian flag for LTL), not the viewed country's flag. Food quality icons follow pattern: `/images/icons/industry/1/q{N}_30x30.png` where N is 1-7. The `info_message` above the treasury explains that "To donate, you must be level 25 or above."

##### 2. Tax Revenue (60 days) — JavaScript Variables

The most valuable data on this page. Three JS variables contain 60 days of daily tax revenue:

**`chartDataJSON`** — Array of arrays for chart rendering. First row is column headers, subsequent rows are daily data:

```javascript
var chartDataJSON = [
  ["Day", "Work as Manager", "Work", "Value Added Tax", "Medals",
   "Import Tax", "Combat Orders", "Treasury Donations", "Cities Subsidy",
   "Paraguay", "Turkey", "South Africa", "Venezuela", "Bulgaria", "Russia", "China"],
  ["Day 6,594", 2365760.93, 114114.93, 121068.59, 73159.64, 3582.66, 2948.16, 0, -268063.49,
   18702.78, 44192.95, 32550.68, 19296.74, 317681.93, 0, 4840.41],
  ["Day 6,595", 2625536.01, 120430.81, 113537.5, 87488.07, ...],
  ...
];
```

**`tableDataJSON`** — Same data transposed for table view. Rows are revenue sources, columns are days. **Includes a "Totals" column** at the end:

```javascript
var tableDataJSON = [
  ["Revenue source", "Day 6,594", "Day 6,595", ..., "Totals", "Revenue source"],
  ["Work as Manager", 2365760.93, 2625536.01, ..., 142981353.98, "Work as Manager"],
  ["Work", 114114.93, 120430.81, ..., 7764237.16, "Work"],
  ...
];
```

**Revenue source columns (standard):**

| Column | Description |
|--------|-------------|
| Work as Manager | Tax from WAM (Work As Manager) operations |
| Work | Employee work tax revenue |
| Value Added Tax | VAT collected on marketplace sales |
| Medals | Medal reward-related revenue |
| Import Tax | Import duties on cross-border trade |
| Combat Orders | Combat order fees |
| Treasury Donations | Direct citizen/org donations to treasury |
| Cities Subsidy | City maintenance costs (**always negative**) |

> **Important:** The column order is **NOT fixed** — it varies between countries. For example:
> - USA: `Work as Manager, Work, Value Added Tax, Medals, Import Tax, Combat Orders, Treasury Donations, Cities Subsidy`
> - Albania: `Work as Manager, Value Added Tax, Import Tax, Medals, Work, Combat Orders, Cities Subsidy` (no Treasury Donations column)
>
> Columns that have had **zero activity** across the entire 60-day period may be **omitted entirely** (e.g., Albania has no `Treasury Donations` column). Always parse column headers from the first row rather than assuming a fixed order.

**Revenue source columns (dynamic — conquered regions):**

After the standard columns, additional columns appear for each country whose regions are currently occupied. These are named by country (e.g., `"Paraguay"`, `"Turkey"`, `"Croatia"`). A value of `0` on a given day means no revenue from that country's regions that day. Countries may appear/disappear as territories change hands — e.g., Albania's `"Croatia"` column shows revenue only for days 6,632-6,645 (when Albania held Croatian regions).

##### 3. Gross Domestic Product (60 days) — JavaScript Variable

**`gdpChartData`** — 60 days of daily production values by industry:

```javascript
var gdpChartData = [
  ["Day", "Food", "Weapons", "House", "Aircraft Weapons"],
  ["6,594", 5341423, 8676609, 840137, 377672],
  ["6,595", 5732159, 9128124, 945382, 418872],
  ...
];
```

| Column | Description |
|--------|-------------|
| Food | Total food production value |
| Weapons | Total weapons production value |
| House | Total house production value |
| Aircraft Weapons | Total aircraft weapons production value |

> **Note:** GDP day format uses just the number (e.g., `"6,594"`) while tax revenue uses `"Day 6,594"`.

##### 4. Productivity Information — JavaScript Variable

**`countryProductivityBonuses`** — Production bonuses available in two formats:

```javascript
var countryProductivityBonuses = {
  "byToken": {"FOOD": 100, "WEAPON": 100, "HOUSE": 0, "AIRCRAFT": 50},
  "byId": {"1": 100, "2": 100, "4": 0, "23": 50}
};
```

Also rendered in HTML with `data-*` attributes:

```html
<div class="item">
    <p class="name">Food</p>
    <img src="//www.erepublik.net/images/buildings/1.png" />
    <p data-industryId="1" data-bonus="100" class="amount full-bonus">+100%</p>
</div>
```

| Industry ID | Token | Icon Path | Bonus CSS Classes |
|-------------|-------|-----------|-------------------|
| 1 | FOOD | `/images/buildings/1.png` | `full-bonus` (100%), `partial-bonus` (1-99%), `noBonus` (0%) |
| 2 | WEAPON | `/images/buildings/2.png` | Same pattern |
| 4 | HOUSE | `/images/buildings/4.png` | Same pattern |
| 23 | AIRCRAFT | `/images/buildings/23.png` | Same pattern |

##### 5. Country Resources Table

Lists all 20 resource types with their production bonuses and which regions contain them. Parse from `table.resource_list`:

```html
<table class="resource_list">
    <thead>
        <tr>
            <th>Resource</th>
            <th>Regions with a Trade Route</th>
            <th>Regions without a Trade Route</th>
        </tr>
    </thead>
    <tr>
        <td title="+25% to Food production">
            <img src="//www.erepublik.net/images/resources/1.png" />
            <span>Grain</span>
            <span class="bonus_value">(+25%)</span>
        </td>
        <td><a href="//www.erepublik.com/en/main/region/Virginia">Virginia</a></td>
        <td><small>Not available</small></td>
    </tr>
    ...
</table>
```

Complete resource ID reference:

| ID | Resource | Industry | Bonus | Icon Path |
|----|----------|----------|-------|-----------|
| 1 | Grain | Food | +25% | `/images/resources/1.png` |
| 2 | Fish | Food | +10% | `/images/resources/2.png` |
| 3 | Fruits | Food | +15% | `/images/resources/3.png` |
| 4 | Cattle | Food | +20% | `/images/resources/4.png` |
| 5 | Deer | Food | +30% | `/images/resources/5.png` |
| 6 | Iron | Weapon | +10% | `/images/resources/6.png` |
| 7 | Saltpeter | Weapon | +25% | `/images/resources/7.png` |
| 8 | Aluminum | Weapon | +15% | `/images/resources/8.png` |
| 9 | Oil | Weapon | +20% | `/images/resources/9.png` |
| 10 | Rubber | Weapon | +30% | `/images/resources/10.png` |
| 11 | Sand | House | +0% | `/images/resources/11.png` |
| 12 | Clay | House | +0% | `/images/resources/12.png` |
| 13 | Wood | House | +0% | `/images/resources/13.png` |
| 14 | Limestone | House | +0% | `/images/resources/14.png` |
| 15 | Granite | House | +0% | `/images/resources/15.png` |
| 16 | Neodymium | Aircraft | +15% | `/images/resources/16.png` |
| 17 | Magnesium | Aircraft | +5% | `/images/resources/17.png` |
| 18 | Cobalt | Aircraft | +12.5% | `/images/resources/18.png` |
| 19 | Titanium | Aircraft | +7.5% | `/images/resources/19.png` |
| 20 | Wolfram | Aircraft | +10% | `/images/resources/20.png` |

> **Key distinction:** Resources in "Regions with a Trade Route" contribute to the country's production bonus. Resources in "Regions without a Trade Route" exist in disconnected territories and **do not contribute** to the bonus (see the society page's `route_warning` for the same regions).

##### 6. Resource Concessions

Two tables showing concessions in and out:

```html
<!-- Resources received via concession -->
<table id="resourcesInConcession">
    <tr><td colspan="5"><i>No resources in concession from other countries.</i></td></tr>
</table>

<!-- Resources conceded to others -->
<table id="conceededResources">
    <tr><td colspan="5"><i>No Resources conceded to other countries.</i></td></tr>
</table>
```

> **Note:** The typo `conceededResources` (double 'e') is in the original HTML.

##### 7. Trade Embargoes

Lists active embargoes with expiry countdown:

```html
<tr>
    <td>
        <img src="//www.erepublik.net/images/flags/L/Russia.gif" />
        <a href="//www.erepublik.com/en/country/society/Russia" class="dotted">Russia</a>
    </td>
    <td>
        <img src="//www.erepublik.net/images/parts/icon-pact.gif" />
        Expires in 25 days
    </td>
</tr>
```

##### 8. Taxes Table

Tax rates per industry. Parse from `table.citizens.largepadded` inside the Taxes section:

```html
<tr>
    <td><img src="https://www.erepublik.net/images/icons/industry/1/default.png" /></td>
    <td><span class="fakeheight">Food</span></td>
    <td><span class="special">1.00%</span></td>    <!-- Work Tax -->
    <td><span class="special">1</span>%</td>        <!-- Import Tax -->
    <td><span class="special">1</span>%</td>        <!-- VAT -->
</tr>
```

Industry icon URL pattern: `/images/icons/industry/{industryId}/default.png`

| Industry | ID | Work Tax | Import Tax | VAT |
|----------|-----|----------|------------|-----|
| Food | 1 | 1.00% | 1% | 1% |
| Weapons | 2 | 1.00% | 1% | 1% |
| Aircraft Weapons | 23 | 1.00% | 1% | 1% |
| Moving Tickets | 3 | 1.00% | 1% | 1% |
| House | 4 | 1.00% | 5% | 1% |
| Food Raw Materials | 7 | 1.00% | 1% | — |
| Weapon Raw Materials | 12 | 1.00% | 1% | — |
| Aircraft Weapons Raw Materials | 24 | 1.00% | 1% | — |
| House Raw Materials | 17 | 1.00% | 1% | — |

> **Note:** Raw material industries have **no VAT** (empty `<span class="special"></span>`). The industry IDs are extracted from the icon image URL.

##### 9. Salary Data

```html
<table class="citizens">
    <tr>
        <td><span class="fakeheight">Minimum</span></td>
        <td><span class="special">1.00</span> USD</td>
    </tr>
    <tr>
        <td><span class="fakeheight">Average</span></td>
        <td><span class="special">6636.52</span> USD</td>
    </tr>
</table>
```

| Metric | Example Value |
|--------|---------------|
| Minimum Salary | 1.00 USD |
| Average Salary | 6,636.52 USD |

#### JavaScript Variables Summary

| Variable | Type | Description |
|----------|------|-------------|
| `chartDataJSON` | `Array[][]` | 60 days of daily tax revenue by source (chart format) |
| `tableDataJSON` | `Array[][]` | Same data transposed with totals (table format) |
| `gdpChartData` | `Array[][]` | 60 days of daily GDP by industry |
| `countryProductivityBonuses` | `Object` | Production bonuses in `byToken` and `byId` formats |
| `countryChartDays` | `number` | Chart time window (default: `60`) |
| `countrySelector` | `Object` | Country selector config with `urlTab: "country/economy"` |

#### Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible without authentication (but Cloudflare may challenge bot-like requests)
- **Most data-rich country page** — contains 3 major JS data variables ideal for scraping economic data
- Tax revenue `chartDataJSON` columns are **dynamic** — both the order and presence of standard columns varies between countries, and conquered region columns appear/disappear as territories change. Always parse column headers from the first row
- The `tableDataJSON` includes a **Totals column** with 60-day aggregates — useful for quick summaries without summing yourself
- **Cities Subsidy** is always negative (it's an expense, not revenue)
- `chartDataJSON` and `tableDataJSON` contain the exact same data in different orientations (chart vs table)
- GDP day format differs from tax revenue: `"6,594"` vs `"Day 6,594"`
- Tax rates and salary data must be parsed from HTML (not in JS variables)
- Resources split into "with Trade Route" vs "without Trade Route" — only trade-route resources contribute to production bonus
- Google Charts library (`https://www.gstatic.com/charts/loader.js`) is loaded for chart rendering
- Industry IDs are consistent across all sections: `1`=Food, `2`=Weapons, `3`=Moving Tickets, `4`=House, `7`=Food RM, `12`=Weapon RM, `17`=House RM, `23`=Aircraft Weapons, `24`=Aircraft RM

---

### Country Politics Page

**Method:** GET
**URL:** `/en/country/politics/{countryName}`
**Auth Required:** No

#### Description

Retrieves the country politics page as HTML. This is a **web page endpoint**, not a JSON API. The page displays three sections: the current Country President, appointed political titles (cabinet), and congressional party distribution. Unlike the economy page, this page contains **no JavaScript data variables** — all data must be parsed from the HTML DOM.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Bosnia-Herzegovina`). Case-sensitive, multi-word names use hyphens |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (avoids 403 from Cloudflare) |

#### Example Request (cURL)

```bash
curl -X GET 'https://www.erepublik.com/en/country/politics/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

#### Example Response

Returns a full HTML page (`<body id="country">`) with three sections:

```html
<!DOCTYPE html>
<html ng-app="ErpkApp" lang="en">
<head>
  <title>eRepublik Country Politics | USA</title>
  <meta name="description" content="USA is a country in Erepublik. Find information about the political activities of USA" />
  ...
</head>
<body id="country" class="logged_out lowered">
  ...
</body>
</html>
```

#### Page Sections (parsed from HTML)

##### 0. Dictator (Dictatorship Countries Only)

In **dictatorship countries**, an additional section appears **before** the President section. Located under `<h2 class="section">Dictator</h2>`:

```html
<h2 class="section">Dictator</h2>
<div class="indent">
    <div class="infoholder">
        <div class="entity rightpadded">
            <div class="avatarholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9556415"
                   class="icon" title="xR0Nx">
                    <img src="https://cdnt.erepublik.net/.../avatar.jpg" />
                </a>
                <div class="xprank">162</div>
            </div>
            <div class="nameholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9556415"
                   class="dotted" title="xR0Nx">
                    xR0Nx
                </a>
            </div>
        </div>
    </div>
</div>
```

| Data | CSS Selector / Pattern | Example |
|------|----------------------|---------|
| Dictator name | `h2:contains("Dictator") ~ div .entity a.dotted` title attr | `xR0Nx` |
| Citizen ID | `.entity a.icon` href → `/en/citizen/profile/{citizenId}` | `9556415` |
| Level | `.entity .xprank` text | `162` |

> **Note:** The Dictator section does **not** have "Election results" or "Next elections" links — dictators are not elected. The `dictatorships` JavaScript variable (documented below in [Global JS Variables](#global-javascript-variables-all-country-pages)) lists all current dictatorship country IDs.

> **Dictatorship effects on other sections:** When a country is a dictatorship, the **Congress section** is empty — the table has headers only, with no party rows (Congress is dissolved under dictatorship).

##### 1. President

Displays the current Country President with avatar, name, level, and profile link. Located under `<h2 class="section">President</h2>`:

```html
<div class="indent">
    <div class="infoholder">
        <div class="entity rightpadded">
            <div class="avatarholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/8482827"
                   class="icon" title="Dominar Rygel XVI">
                    <img src="https://cdnt.erepublik.net/.../avatar.jpg" />
                </a>
                <div class="xprank">37</div>
            </div>
            <div class="nameholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/8482827"
                   class="dotted" title="Dominar Rygel XVI">
                    Dominar Rygel XVI
                </a>
            </div>
        </div>
        <div class="smallholder">
            <a href="//www.erepublik.com/en/main/presidential-elections/24/1770278400">
                <span class="std_global_btn mediumSize blueColor">Election results</span>
            </a>
            <a href="//www.erepublik.com/en/presidential-candidates/country-24">
                <span class="std_global_btn mediumSize blueColor">Next elections</span>
            </a>
        </div>
    </div>
</div>
```

| Data | CSS Selector / Pattern | Example |
|------|----------------------|---------|
| President name | `.infoholder .entity a.dotted` title attr | `Dominar Rygel XVI` |
| Citizen ID | `.entity a.icon` href → `/en/citizen/profile/{citizenId}` | `8482827` |
| Level | `.entity .xprank` text | `37` |
| Avatar | `.entity a.icon img` src | CDN URL |
| Election results URL | `.smallholder a` (1st) href | `/en/main/presidential-elections/{countryId}/{timestamp}` |
| Next elections URL | `.smallholder a` (2nd) href | `/en/presidential-candidates/country-{countryId}` |

> **Note:** The `hovercard=1` attribute on citizen links triggers AngularJS hovercard popups that use the `/en/main/citizen-profile-json-personal/{citizenId}` endpoint (documented in API_SOCIAL.md).

##### 2. Political Titles (Cabinet)

Lists appointed government positions. Located under `<h2 class="section">Political titles</h2>` in a `<div class="political-titles">` container:

```html
<div class="political-titles">
    <div class="entity entity-wrapper">
        <p class="main-title">Prime Minister</p>
        <div class="avatarholder">
            <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9474388"
               class="icon" title="Monsieur Manchukuo">
                <img src="https://cdnt.erepublik.net/.../avatar.png" />
            </a>
            <div class="xprank">43</div>
        </div>
        <div class="nameholder">
            <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9474388"
               class="dotted" title="Monsieur Manchukuo">
                Monsieur Manchukuo
            </a>
        </div>
    </div>
    <!-- More entity-wrapper divs for each title -->
</div>
```

| Data | CSS Selector | Example |
|------|-------------|---------|
| Title name | `.entity-wrapper p.main-title` | `Prime Minister` |
| Citizen name | `.entity-wrapper a.dotted` title attr | `Monsieur Manchukuo` |
| Citizen ID | `.entity-wrapper a.icon` href → `/en/citizen/profile/{citizenId}` | `9474388` |
| Level | `.entity-wrapper .xprank` text | `43` |

Example cabinet (USA, eDay 6654):

| Title | Citizen | ID | Level |
|-------|---------|-----|-------|
| Prime Minister | Monsieur Manchukuo | 9474388 | 43 |
| Minister of Defense | Herr Vootsman | 1714685 | 1357 |
| Minister of Foreign Affairs | svs1109 | 9375724 | 270 |
| Governor | Yui MHCP001 | 3404889 | 122 |
| Minister of Education | Hachiman Kokeshi | 9109594 | 61 |

> **Note:** Political titles are **appointed by the president** and vary by country — not all countries use the same titles. The number and names of titles are completely customizable.

##### 3. Congress

Displays top 5 party representation in Congress. Located under `<h2 class="section">Congress</h2>` in a `table.political.largepadded`:

```html
<table class="political largepadded">
    <tr>
        <th width="210px"></th>
        <th width="85px"></th>
        <th width="305px"></th>
    </tr>
    <tr>
        <td><span class="fakeheight">
            <img src="//cdnt.erepublik.net/.../party_avatar.png"
                 class="icon" width="55" height="55"/>
            <a href="//www.erepublik.com/en/party/we-the-people-19/1" class="dotted">
                We The People
            </a>
        </span></td>
        <td><span class="special">27</span> %</td>
        <td>
            <span class="emplevel" style="width: 55%">&nbsp;</span><br />
        </td>
    </tr>
    <!-- More party rows -->
</table>
<a href="//www.erepublik.com/en/main/congress-elections/24/0/1769328000">
    <span class="std_global_btn mediumSize blueColor">Election results</span>
</a>
```

| Data | CSS Selector | Example |
|------|-------------|---------|
| Party name | `table.political a.dotted` text | `We The People` |
| Party URL | `table.political a.dotted` href → `/en/party/{slug}-{partyId}/1` | `/en/party/we-the-people-19/1` |
| Party avatar | `table.political img.icon` src | CDN URL |
| Seats % | `table.political span.special` text | `27` |
| Bar width | `span.emplevel` style `width` (= percentage × 2) | `55%` for 27% seats |
| Election results | Last `<a>` in Congress section | `/en/main/congress-elections/{countryId}/0/{timestamp}` |

Example data (USA, eDay 6654):

| Party | Slug | Party ID | Seats % | Bar Width |
|-------|------|----------|---------|-----------|
| We The People | we-the-people | 19 | 27% | 55% |
| The Black Sheep Party | the-black-sheep-party | 2397 | 25% | 50% |
| Rough Riders | rough-riders | 6026 | 20% | 40% |
| Federalist Party | federalist-party | 2263 | 15% | 30% |
| Socialist Freedom Party | -socialist-freedom-party | 3653 | 12% | 25% |

> **Party URL pattern:** `/en/party/{partyNameSlug}-{partyId}/1` — the slug is lowercase with hyphens, and the party ID is appended after a hyphen. Note the leading hyphen in `"-socialist-freedom-party"` — this appears to be a data quirk (extra space in the original name).

#### Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/main/presidential-elections/{countryId}/{timestamp}` | Presidential election results |
| `/en/presidential-candidates/country-{countryId}` | Current presidential candidates |
| `/en/main/congress-elections/{countryId}/0/{timestamp}` | Congress election results |
| `/en/party/{partySlug}-{partyId}/1` | Party page (slug + ID) |
| `/en/citizen/profile/{citizenId}` | Citizen profile |

#### Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible without authentication (but Cloudflare may challenge bot-like requests)
- **No embedded JavaScript data variables** — all political data is rendered directly in HTML and must be parsed from the DOM
- The only JS variable of note is `countrySelector` with `urlTab: "country/politics"`
- The `hovercard=1` HTML attribute on citizen `<a>` tags is used by AngularJS to trigger profile hovercards
- Political titles are **appointed by the president** and vary by country — not all countries use the same titles or number of positions
- The Congress section shows only the **top 5 parties** — total may not add to 100% if there are additional smaller parties
- The bar chart width (`span.emplevel` style) is approximately `seats% × 2` (e.g., 27% → 55% width)
- Party names may be **truncated with `...`** in the display (e.g., "The Black Sheep ..." → full name: "The Black Sheep Party")
- Election timestamps in URLs are **Unix timestamps** (e.g., `1770278400` for presidential, `1769328000` for congress)
- Country ID appears in election URLs — for USA it's `24` (see Society page for full country ID reference)
- Congress seat percentages are displayed as **integers** (rounded)

---

### Country Military Page

**Method:** GET
**URL:** `/en/country/military/{countryName}`
**Auth Required:** No

#### Description

Retrieves the country military page as HTML. This is a **web page endpoint**, not a JSON API. The page displays military information for a country including natural enemy status, defence shield, airstrike readiness, active wars, active resistance wars, and mutual protection pacts.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Republic-of-Moldova`) |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (Cloudflare may challenge bot-like requests) |

#### Example Request (cURL)

```bash
curl -X GET 'https://www.erepublik.com/en/country/military/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

#### Example Response

Returns a full HTML page (`<title>eRepublik Country Military | USA</title>`) with six sections under `<h2 class="section">` headings.

#### Page Sections (parsed from HTML)

##### 1. Natural Enemy

Under `<h2 class="section">Natural Enemy</h2>`, preceded by an info message about +10% damage bonus:

```html
<table class="info_message">
    <tr>
        <td>The citizens of this country will be provided with a +10% damage bonus
            in the military campaigns against the Natural Enemy.
            (<a href="//wiki.erepublik.com/index.php/Natural_enemy" target="_blank">more info</a>)
        </td>
    </tr>
</table>

<div class="indent">
    <!-- When NO natural enemy: -->
    <div class="attacker" style="padding-top:0px;">
        <div class="nameholder" style="font-size:14px; margin-top:9px;">
            No current Natural Enemy
        </div>
    </div>

    <!-- When natural enemy IS set: -->
    <div class="attacker" style="padding-top:0px; width: 500px">
        <div class="flagholder">
            <a href="//www.erepublik.com/en/country/society/{enemyCountry}">
                <img src="//www.erepublik.net/images/flags_png/L/{enemyCountry}.png" />
            </a>
        </div>
        <div class="nameholder" style="font-size:14px; margin-top:9px;">
            <a class="dotted" href="//www.erepublik.com/en/country/society/{enemyCountry}">
                {Enemy Country Name}
            </a>
        </div>
        Since: Day 6,371, 11:40 (9 months ago)
    </div>
</div>
```

| Data | CSS Selector | Example (USA) | Example (Poland) |
|------|-------------|---------------|-------------------|
| Natural enemy status | `div.indent > div.attacker > div.nameholder` text | `No current Natural Enemy` | Enemy name (e.g., `Estonia`) |
| Enemy flag | `div.indent > div.attacker img` src | — | `//www.erepublik.net/images/flags_png/L/{country}.png` |
| Enemy link | `div.indent > div.attacker a` href | — | `/en/country/society/{country}` |
| Since timestamp | Text after `.nameholder` in `.attacker` div | — | `Since: Day 6,371, 11:40 (9 months ago)` |

> **Note:** When a natural enemy IS set, the section includes a "Since" timestamp showing when the natural enemy was declared, with both the eRepublik game day and a human-readable relative time (e.g., "9 months ago"). The flag image uses the `/flags_png/L/` (Large) size, not XL.

##### 2. Defence Shield

Under `<h2 class="section">Defence Shield</h2>`, with an info message explaining the mechanic:

```html
<table class="info_message">
    <tr>
        <td>The Defence Shield protects your country against attacks.<br />
            When a region is attacked, your country receives a damage bonus equal to
            the Shield Capacity divided by the number of regions owned.</td>
    </tr>
</table>
<div class="indent">
    <div class="attacker shield_system" style="padding-top:0px;">
        <div class="defence_shield">
            Defence Shield: 0 damage left
        </div>
    </div>
</div>
```

| Data | CSS Selector | Example (USA) |
|------|-------------|---------------|
| Shield capacity | `div.defence_shield` text | `Defence Shield: 0 damage left` |

> **Parsing:** Extract numeric value from `Defence Shield: {N} damage left` — when 0, the shield is depleted.

##### 3. Airstrike Readiness

Under `<h2 class="section">Airstrike</h2>`. Displays two circular gauge widgets (jQuery Knob) showing progress toward airstrike requirements. The Country President can use an airstrike to declare war on countries without shared borders.

```html
<div class="indent borders">
    <!-- Energy Units gauge -->
    <div>
        <input id="health_units" data-req="245000" class="knob"
               data-width="90" data-height="90"
               data-borderColor="#D6D6D6" data-displayPrevious=false
               data-fgColor="#9ADE53" data-bgColor="#FFF" data-skin="tron"
               value="13" data-thickness=".25"
               data-readOnly=true readonly=readonly />
        <span><strong>Energy Units required:</strong>
            <em id="health_unit_cur">34,090</em> / 245,000
        </span>
    </div>

    <!-- Currency gauge -->
    <div>
        <input id="currency_req" data-req="240000" class="knob"
               data-width="90" data-height="90"
               data-borderColor="#D6D6D6" data-displayPrevious=false
               data-fgColor="#EC9E7A" data-bgColor="#FFF" data-skin="tron"
               value="100" data-thickness=".25"
               data-readOnly=true readonly=readonly />
        <span><strong>Currency required:</strong>
            <em id="currency_req_cur">1,448,095,127</em> / 240,000
        </span>
    </div>
</div>
```

| Data | HTML Element | Attribute | Example (USA) |
|------|-------------|-----------|---------------|
| Energy required | `input#health_units` | `data-req` | `245000` |
| Energy current | `em#health_unit_cur` | text content | `34,090` |
| Energy gauge % | `input#health_units` | `value` (0-100) | `13` |
| Currency required | `input#currency_req` | `data-req` | `240000` |
| Currency current | `em#currency_req_cur` | text content | `1,448,095,127` |
| Currency gauge % | `input#currency_req` | `value` (0-100) | `100` |

> **Note:** Airstrike requirements are **country-specific**. Compare:
> - USA: 245,000 energy units, 240,000 currency
> - Lithuania: 45,750 energy units, 270,000 currency
> - Albania: 103,250 energy units, 66,666.67 currency
>
> The `value` attribute is an integer 0-100 representing gauge fill percentage. The gauge color is green (`#9ADE53`) for energy and orange (`#EC9E7A`) for currency. The `data-req` can be a **floating-point number** (e.g., Albania's currency: `66666.666666667`) — the display rounds it.

##### 3b. Airstrike Donation Form (Logged-in Only)

When authenticated (level 25+), below the airstrike gauges, a donation form appears for contributing Food and Currency to the airstrike effort. This is similar to the Economy page donation form but **without Gold**:

```html
<table class="donate_table" cellpading="0" cellspacing="0">
    <tr>
        <!-- Food donation with quality selector (same as economy page) -->
        <td><b class="green">Food:</b></td>
        <td><input type="text" id="food_amount" name="food_amount" value="10" /></td>
        <td>
            <!-- Food quality selector: q1 through q7 images -->
            <div class="food_type">
                <img src=".../q1_30x30.png" data-quality="1" alt="" />
                <!-- ... -->
                <img src=".../q7_30x30.png" data-quality="7" alt="" />
            </div>
        </td>
        <td>
            <a onclick="donateFood_onClick();" title="Contribute">
                <span>Contribute</span>
            </a>
        </td>
    </tr>
    <tr>
        <!-- Currency donation -->
        <td><b class="orange">Currency:</b></td>
        <td><input type="text" id="currency_amount" name="currency_amount" value="20" /></td>
        <td>
            <span class="currency_cl">
                <img src="//www.erepublik.net/images/flags_png/XL/Lithuania.png"
                     alt="" width="24" /><strong>LTL</strong>
            </span>
        </td>
        <td>
            <a onclick="donateCurrency_onClick();" title="Contribute">
                <span>Contribute</span>
            </a>
        </td>
    </tr>
</table>
```

> **Note:** The airstrike donation form uses `donateFood_onClick()` and `donateCurrency_onClick()` (without `_eTab` suffix, unlike the Economy page). The `info_message` above mentions the level 25 requirement and that "The Country President can use the Airstrike to declare war and attack a country that you do not have borders with." The `SERVER_DATA.countryId` is set inline for the donation JS handlers.

##### 4. Active Wars

Under `<h2 class="section">Active wars in {countryName}</h2>`. Wars are listed in a `table.political.largepadded`:

```html
<h2 class="section">Active wars in USA</h2> <!-- adaugat text -->
<div class="indent">
    <table class="political largepadded">
        <tr>
            <th width="530px"></th>
            <th width="65px"></th>
        </tr>
        <!-- War entry (repeated for each war) -->
        <tr>
            <td>
                <div class="warholder-small">
                    <div class="attacker">
                        <div class="flagholder">
                            <a href="//www.erepublik.com/en/country/society/Romania">
                                <img src="//www.erepublik.net/images/flags_png/XL/Romania.png"
                                     alt="" width="48" style="margin-top:-6px;"/>
                            </a>
                        </div>
                        <div class="nameholder" style="font-size:14px; margin-bottom:1px;">
                            <a class="dotted" href="//www.erepublik.com/en/country/society/Romania"
                               title="">Romania</a>
                        </div>
                        <!-- No allies variant: -->
                        <a id="defender_allies" href="javascript:;" class="none"
                           title="no allies">no allies</a>
                        <!-- Has allies variant: -->
                        <a href="javascript:;" class="show_allies">1 allies
                            <div class="allies_tooltip left_side">
                                <img src="//www.erepublik.net/images/modules/pvp/tip.png"
                                     class="tip" alt="" />
                                <ul>
                                    <li><img src="//www.erepublik.net/images/flags_png/S/Estonia.png"
                                             alt="" />Estonia</li>
                                </ul>
                                <div class="clear"></div>
                            </div>
                        </a>
                    </div>
                    <div class="defender">
                        <!-- Same structure as attacker -->
                    </div>
                    <div class="middle">VS</div>
                </div>
            </td>
            <td>
                <span class="">
                    <a href="//www.erepublik.com/en/wars/show/192366"
                       class="std_global_btn mediumSize blueColore">
                        <span>details</span>
                    </a>
                </span>
            </td>
        </tr>
        <!-- More war entries... -->
    </table>

    <!-- "All wars" form at the bottom -->
    <form action="//www.erepublik.com/en/military/campaigns" method="POST"
          name="wars_filters_form" id="wars_filters_form">
        <input type="hidden" name="_token" value="{csrfToken}" id="fwt" />
        <input type="hidden" name="fwt" value="conquest" id="fwt" />
        <input type="hidden" name="fws" value="all" id="fws" />
        <input type="hidden" name="fwc" value="all" id="fwc" />
        <a href="javascript:;" onclick="document.getElementById('wars_filters_form').submit()"
           class="std_global_btn smallSize blueColor">All wars</a>
    </form>
</div>
```

**War entry CSS selectors for parsing:**

| Data | CSS Selector | Notes |
|------|-------------|-------|
| War container | `div.warholder-small` | One per war |
| Attacker name | `div.warholder-small > div.attacker div.nameholder a.dotted` | text content |
| Attacker flag | `div.warholder-small > div.attacker div.flagholder img` | `src` attr → flag image |
| Attacker country link | `div.warholder-small > div.attacker div.flagholder a` | `href` → `/en/country/society/{country}` |
| Defender name | `div.warholder-small > div.defender div.nameholder a.dotted` | text content |
| Defender flag | `div.warholder-small > div.defender div.flagholder img` | `src` attr |
| No allies | `a.none[title="no allies"]` | Has class `none` |
| Has allies | `a.show_allies` | Text: `{N} allies` |
| Ally names | `div.allies_tooltip li` | Text content of each `<li>` |
| Ally flags | `div.allies_tooltip li img` | Small flag (`/flags_png/S/`) |
| War details link | `a.std_global_btn[href*="wars/show"]` | href → `/en/wars/show/{warId}` |
| War ID | Extract from details link | Integer from URL path |

**Allies tooltip structure:**

```html
<!-- No allies: simple <a> with class="none" -->
<a id="defender_allies" href="javascript:;" class="none" title="no allies">no allies</a>

<!-- Has allies: <a> with class="show_allies" containing tooltip -->
<a href="javascript:;" class="show_allies">1 allies
    <div class="allies_tooltip left_side">
        <img src="//www.erepublik.net/images/modules/pvp/tip.png" class="tip" alt="" />
        <ul>
            <li><img src="//www.erepublik.net/images/flags_png/S/Estonia.png" alt="" />Estonia</li>
            <!-- More allies as <li> elements -->
        </ul>
        <div class="clear"></div>
    </div>
</a>
```

> **Note:** The tooltip uses small flag images (`/flags_png/S/`) while the main war entry uses XL flags (`/flags_png/XL/`).

**"All wars" form** — hidden form that POSTs to `/en/military/campaigns` with filter parameters:

| Hidden Field | Value | Description |
|-------------|-------|-------------|
| `_token` | CSRF token | Anti-forgery token |
| `fwt` | `conquest` | War type filter |
| `fws` | `all` | War status filter |
| `fwc` | `all` | Country filter |

Example active wars data (USA, eDay 6654, **32 active wars**):

| # | Attacker | Defender | Allies (Attacker) | War ID |
|---|----------|----------|-------------------|--------|
| 1 | Romania | **USA** | none | 192366 |
| 2 | Canada | **USA** | Estonia (1) | 192823 |
| 3 | Poland | **USA** | Finland (1) | 192904 |
| 4 | Netherlands | **USA** | none | 198537 |
| 5 | Lithuania | **USA** | none | 199687 |
| 6 | Portugal | **USA** | none | 202192 |
| 7 | Australia | **USA** | none | 205693 |
| 8 | Philippines | **USA** | none | 211305 |
| 9 | United Kingdom | **USA** | none | 211788 |
| 10 | **USA** | France | none | 214786 |
| 11 | **USA** | Indonesia | none | 215769 |
| 12 | United Arab Emirates | **USA** | none | 216548 |
| 13 | Argentina | **USA** | none | 217413 |
| 14 | Sweden | **USA** | none | 217753 |
| 15 | Czech Republic | **USA** | none | 217834 |
| 16 | Japan | **USA** | none | 217996 |
| 17 | Thailand | **USA** | none | 219357 |
| 18 | Brazil | **USA** | none | 219510 |
| 19 | Latvia | **USA** | none | 220376 |
| 20 | Greece | **USA** | none | 220994 |
| 21 | Belarus | **USA** | none | 222069 |
| 22 | Republic of Moldova | **USA** | none | 222102 |
| 23 | **USA** | Republic of China (Taiwan) | none | 222124 |
| 24 | Mexico | **USA** | none | 222321 |
| 25 | **USA** | Cuba | none | 222375 |
| 26 | **USA** | China | none | 222719 |
| 27 | **USA** | Paraguay | none | 223166 |
| 28 | Spain | **USA** | none | 223261 |
| 29 | Israel | **USA** | none | 223264 |
| 30 | North Macedonia | **USA** | none | 223310 |
| 31 | Egypt | **USA** | none | 223577 |
| 32 | South Africa | **USA** | none | 223581 |
| 33 | Bulgaria | **USA** | none | 224057 |
| 34 | Colombia | **USA** | none | 224147 |

> **Observation:** The viewed country can appear as either attacker or defender. USA is attacker in wars #10, #11, #23, #25, #26, #27 (attacking France, Indonesia, Taiwan, Cuba, China, Paraguay) and defender in all others.

##### 5. Active Resistance Wars

Under `<h2 class="section">Active resistance wars in {countryName}</h2>`. Uses `table.political.padded` (note: `.padded` not `.largepadded` like active wars):

```html
<!-- When no resistance wars: -->
<h2 class="section">Active resistance wars in Albania</h2>
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="215px"></th>
            <th width="318px"></th>
            <th width="63px"></th>
        </tr>
        <tr>
            <td colspan="3">
                There are no resistance wars in this country.
            </td>
        </tr>
    </table>
</div>

<!-- When resistance wars exist (Poland example): -->
<h2 class="section">Active resistance wars in Poland</h2>
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="215px"></th>
            <th width="318px"></th>
            <th width="63px"></th>
        </tr>
        <tr>
            <td class="last">
                <img style="vertical-align:middle"
                     src="//www.erepublik.net/images/flags_png/L/Resistance.png" alt="" />
                Resistance Force of Canada
            </td>
            <td class="last">
                <span class="goright influence_multiplier"
                      title="Canada receives x2.94 Influence due to their Determination to be free.">
                    2.94<span></span>
                </span>
            </td>
            <td class="last">
                <a href="//www.erepublik.com/en/wars/show/224764"
                   class="std_global_btn mediumSize blueColore">
                    <span>details</span>
                </a>
            </td>
        </tr>
    </table>
</div>
```

**Resistance war entry CSS selectors:**

| Data | CSS Selector | Example |
|------|-------------|---------|
| Resistance flag | `td img[src*="Resistance.png"]` | `/images/flags_png/L/Resistance.png` |
| Resistance force name | `td` text after flag img | `Resistance Force of Canada` |
| Determination multiplier | `span.influence_multiplier` text | `2.94` (citizens of the occupied country receive x2.94 influence) |
| War details link | `a[href*="wars/show"]` | `/en/wars/show/224764` |

> **Note:** The resistance wars table has 3 columns (215px, 318px, 63px) vs active wars table with 2 columns (530px, 65px). The flag used is a special `/flags_png/L/Resistance.png` generic resistance flag, not a country flag. The determination multiplier works the same as on the Society page for occupied regions.

##### 6. Mutual Protection Pacts (MPP)

Under `<h2 class="section">Mutual Protection Pacts</h2>`. Uses `table.political.padded`:

```html
<!-- When no MPPs: -->
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="405px;"></th>
            <th width="195px;"></th>
        </tr>
        <tr>
            <td colspan="3">
                This country has no mutual protection pacts.
            </td>
        </tr>
    </table>
    <a href="//www.erepublik.com/en/list/mpp/0/1"
       class="std_global_btn smallSize blueColor">All Mutual Protection Pacts</a>
</div>

<!-- When MPPs exist (Poland example): -->
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="405px;"></th>
            <th width="195px;"></th>
        </tr>
        <tr>
            <td class="last">
                <span class="fakeheight">
                    <img src="//www.erepublik.net/images/flags_png/L/Finland.png"
                         alt="" class="flag"/>
                    <a href="//www.erepublik.com/en/country/society/Finland"
                       class="dotted" title="">
                        Finland
                    </a>
                </span>
            </td>
            <td class="last">
                <img src="//www.erepublik.net/images/parts/icon-pact.gif"
                     alt="" class="icon" />
                Expires in 2 months
            </td>
        </tr>
    </table>
    <a href="//www.erepublik.com/en/list/mpp/0/1"
       class="std_global_btn smallSize blueColor">All Mutual Protection Pacts</a>
</div>
```

**MPP entry CSS selectors:**

| Data | CSS Selector | Example |
|------|-------------|---------|
| Country flag | `td span.fakeheight img.flag` src | `/images/flags_png/L/Finland.png` |
| Country name | `td span.fakeheight a.dotted` text | `Finland` |
| Country link | `td span.fakeheight a.dotted` href | `/en/country/society/Finland` |
| Pact icon | `td img.icon` src | `/images/parts/icon-pact.gif` |
| Expiry text | `td` text after pact icon | `Expires in 2 months` |

> **Note:** The MPP table has 2 columns (405px, 195px). An "All Mutual Protection Pacts" button links to `/en/list/mpp/0/1`. The expiry text uses relative time (e.g., "Expires in 2 months", "Expires in 25 days") — the exact date is not shown. Flag images use the `/flags_png/L/` (Large) size.

#### JavaScript on this Page

Unlike the Economy page, the Military page has **no structured data variables**. The JS is purely for UI rendering:

| Script | Purpose |
|--------|---------|
| `jquery.knob.js` | Renders circular gauge widgets for airstrike readiness |
| `allies_dropdown.js` | Handles allies tooltip show/hide on hover |
| `countryPage.js` | General country page interactions |
| `countrySelector.js` | Country dropdown selector |

The only JS variable of interest:

```javascript
var countrySelector = {
    placeholderText: "Choose a country...",
    urlTab: "country/military"
};
```

The jQuery Knob initialization uses `data-skin="tron"` for the arc-style rendering with custom draw function. The `value` attribute (0-100) drives the gauge fill animation.

Inline JS also includes `selectQuality`, `active_elem`, `current_elem` variables related to a food quality selector (likely for donation UI, only visible when logged in).

#### Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/wars/show/{warId}` | War details page |
| `/en/list/mpp/0/1` | All Mutual Protection Pacts list |
| `/en/military/campaigns` | All campaigns page (POST with filter params) |
| `/en/country/society/{countryPermalink}` | Country page links use permalink from flag href |

#### CSS Selectors Summary for Parsing

| Section | Key Selector | Data |
|---------|-------------|------|
| Natural Enemy | `h2.section:contains("Natural Enemy") ~ div.indent div.nameholder` | Enemy name or "No current Natural Enemy" |
| Defence Shield | `div.defence_shield` | Shield capacity text |
| Airstrike Energy | `input#health_units` | `data-req` (required), `value` (%), `em#health_unit_cur` (current) |
| Airstrike Currency | `input#currency_req` | `data-req` (required), `value` (%), `em#currency_req_cur` (current) |
| Active Wars | `table.political.largepadded div.warholder-small` | One per war |
| War details | `a[href*="wars/show"]` | War ID from URL |
| Resistance Wars | `h2:contains("resistance wars") ~ div table.political.padded` | Content or "no resistance wars" |
| MPP | `h2:contains("Mutual Protection Pacts") ~ div table.political.padded` | Content or "no mutual protection pacts" |

#### Additional CSS & JS Resources

| Resource | URL Pattern |
|----------|-------------|
| War list styles | `//www.erepublik.net/css/cmp/war_list.{buildId}.css` |
| jQuery Knob | `//www.erepublik.net/js/jquery.knob.{buildId}.js` |
| Allies dropdown | `//www.erepublik.net/js/wars/allies_dropdown.{buildId}.js` |
| Country page | `//www.erepublik.net/js/country/countryPage.{buildId}.js` |
| Country selector | `//www.erepublik.net/js/country/countrySelector.{buildId}.js` |

#### Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible **without authentication** (but Cloudflare may challenge bot-like requests)
- **No structured JavaScript data variables** — unlike the Economy page, all data is rendered directly in HTML and must be parsed from the DOM
- Airstrike requirements (`data-req`) are **country-specific values** — they differ between countries (USA: 245k energy / 240k currency; Lithuania: 45,750 energy / 270k currency)
- The number of active wars varies dynamically — USA had **34 active wars** at time of documentation (eDay 6654)
- War IDs (e.g., `192366`, `224147`) are sequential integers usable with `/en/wars/show/{warId}`
- Country permalinks in URLs use hyphenated names (e.g., `Republic-of-Moldova`, `United-Arab-Emirates`, `Republic-of-China-Taiwan`)
- The `<h2 class="section">` headings contain the section name and sometimes the country name (e.g., "Active wars in USA", "Active resistance wars in USA")
- HTML source contains **Romanian developer comments**: `<!-- schimbari-->` ("changes"), `<!-- adaugat text -->` ("added text")
- The "All wars" button submits a hidden POST form to `/en/military/campaigns` with filter parameters — this could be used to programmatically navigate to the campaigns page with preset filters
- The `blueColore` CSS class on war details buttons appears to be a **typo** in the source (should likely be `blueColor`)
- Ally flag images use the small size path (`/flags_png/S/`) while war entry flags use XL (`/flags_png/XL/`)
- Cloudflare challenge script is present at the bottom of the page

---

### Country Administration Page

**Method:** GET
**URL:** `/en/country-administration/{countryName}/{page}`
**Auth Required:** No

#### Description

Retrieves the country administration page as HTML. This is the **5th tab** of the country pages and uses a **different URL pattern** from the other four tabs (`/en/country-administration/` instead of `/en/country/{tab}/`). The page shows military priorities (Campaign of the Day / deploy allied forces) and a paginated history of law proposals.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Republic-of-Moldova`) |
| page | integer | Yes | Page number for law proposals pagination (starts at `1`) |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (Cloudflare may challenge bot-like requests) |

#### Example Request (cURL)

```bash
curl -X GET 'https://www.erepublik.com/en/country-administration/USA/1' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

#### Example Response

Returns a full HTML page (`<title>eRepublik Country Administration | USA</title>`) with two main sections.

#### Page Sections (parsed from HTML)

##### 1. Access Message

A message indicating the viewer's access level:

```html
<div class="holder bordersep">
    <p class="infoholder">
        <span class="rightpadded">You are not a president or a congress member in this country.</span>
        <a href="//wiki.erepublik.com/index.php/USA" target="_blank">
            <img src="//www.erepublik.net/images/parts/btn_icon-wiki.gif" title="Open Wiki page" class="flag" alt="wiki">
        </a>
    </p>
</div>
```

> **Note:** When logged in as the Country President or a Congress member, this area likely shows additional controls (e.g., propose law buttons). The message text changes based on the viewer's role.

##### 2. Military Priorities

Under `<h2 class="section">Military Priorities</h2>` inside `div#militaryPriorities`:

```html
<div class="holder" id="militaryPriorities">
    <h2 class="section">Military Priorities</h2>
    <div class="adminaction">
        <div class="goleft padded">
            <a class="std_global_btn smallSize orangeColor mppPriorities disabled"
               id="mppPriorityTrigger" title="Deploy allied forces" tipsy>
                <span>Deploy allied forces</span>
            </a>
        </div>
    </div>
</div>
```

| Data | CSS Selector | Notes |
|------|-------------|-------|
| Deploy allied forces button | `a#mppPriorityTrigger` | Has class `disabled` when viewer is not president |
| Button text | `a#mppPriorityTrigger span` | `Deploy allied forces` |

> **Note:** The "Deploy allied forces" button has class `disabled` for non-president viewers. When the Country President is logged in, this button opens the MPP priority popup (handled by `generalPopup.renderPopup('mppPriority')` in JS). There may also be a "Campaign of the Day" feature (see JS variables below).

##### 2b. Manage Organizations

Under `<h2 class="section">Manage Organizations</h2>` inside `div#manageOrganizations`. This section appears **after** Military Priorities:

```html
<div class="holder" id="manageOrganizations">
    <h2 class="section">Manage Organizations</h2>
    <div class="adminaction">
        <div class="goleft padded">
            <a class="std_global_btn smallSize orangeColor mppPriorities disabled"
               id="manageOrganizationsTrigger" title="Manage Organizations" tipsy>
                <span>Manage Organizations</span>
            </a>
        </div>
    </div>
</div>
```

| Data | CSS Selector | Notes |
|------|-------------|-------|
| Manage Organizations button | `a#manageOrganizationsTrigger` | Has class `disabled` when viewer is not president |
| Button text | `a#manageOrganizationsTrigger span` | `Manage Organizations` |

> **Note:** Like the "Deploy allied forces" button, the "Manage Organizations" button has class `disabled` for non-president viewers. When the Country President is logged in, this button likely opens a popup to manage country organizations. Note the CSS class `mppPriorities` is reused from the Military Priorities section (likely a copy-paste from the original code).

##### 3. Law Proposals

Under `<h2 class="section">Law proposals</h2>`. Contains a table listing recent law proposals with their status:

```html
<div class="holder">
    <h2 class="section">Law proposals</h2>
    <div class="indent">
        <table class="laws lawDescription largepadded">
            <!-- Law entry (repeated for each proposal) -->
            <tr>
                <td width="250" class="first">
                    <a href="//www.erepublik.com/en/main/law/USA/327910"
                       title="" class="dotted taxStyle Donate">
                        Donate
                        <span class="lawIco"></span>
                    </a>
                </td>
                <td width="150" class="first">
                    Day 6,653, 21:35 <br><small>(6 hours ago)</small>
                </td>
                <td width="140" class="first">
                    <img src="//www.erepublik.net/images/parts/icon-law_empty.gif"
                         alt="Pending" class="icon">Pending
                </td>
                <td width="60" class="first">
                    <a href="//www.erepublik.com/en/main/law/USA/327910"
                       class="std_global_btn tinySize blueColor">
                        <span>Details</span>
                    </a>
                </td>
            </tr>
            <!-- More law entries... -->
        </table>
        <ul class="pager">
            <!-- Pagination links when multiple pages exist -->
        </ul>
    </div>
</div>
```

**Law entry CSS selectors for parsing:**

| Data | CSS Selector | Example |
|------|-------------|---------|
| Law type + link | `table.laws a.dotted` | text: `Donate`, href: `/en/main/law/USA/327910` |
| Law type CSS class | `table.laws a.dotted` class attr | `taxStyle Donate`, `taxStyle NaturalEnemy` |
| Law icon | `table.laws span.lawIco` | Empty span, styled via CSS based on parent class |
| Date/time | `table.laws td:nth-child(2)` | `Day 6,653, 21:35` with `<small>(6 hours ago)</small>` |
| Status icon | `table.laws td:nth-child(3) img.icon` | `alt` attr: `Pending` or `Accepted` |
| Status text | `table.laws td:nth-child(3)` | `Pending` or `Accepted` |
| Details link | `table.laws a.std_global_btn` | href: `/en/main/law/{countryName}/{lawId}` |
| Law ID | Extract from details link | Integer from URL (e.g., `327910`) |
| Pagination | `ul.pager` | Contains page links when more than one page of laws |

**Law types discovered (from CSS classes):**

| CSS Class | Law Type | Description |
|-----------|----------|-------------|
| `taxStyle Donate` | Donate | Treasury donation proposal |
| `taxStyle NaturalEnemy` | Natural Enemy | Declare/remove natural enemy |
| `taxStyle Airstrike` | Airstrike | Launch an airstrike against a non-bordering country |
| `taxStyle DeclareWar` | Declare War | Declare war on a bordering country |

> **Note:** Additional law types likely exist (e.g., MPP, Embargo, Tax Change, Impeachment) but have not been observed yet. The CSS class on the `<a>` tag follows the pattern `taxStyle {LawType}`.

**Status icons:**

| Status | Icon File | Alt Text |
|--------|----------|----------|
| Pending | `icon-law_empty.gif` | `Pending` |
| Accepted | `icon-law_valid.gif` | `Accepted` |
| Rejected | `icon-law_invalid.gif` | `Rejected` |

> **Note:** Additional statuses may exist (e.g., Expired) with corresponding icon files.

Example law proposals data (USA, eDay 6654, page 1):

| Law Type | Law ID | Date | Status |
|----------|--------|------|--------|
| Donate | 327910 | Day 6,653, 21:35 | Pending |
| Natural Enemy | 327868 | Day 6,653, 06:28 | Pending |
| Donate | 327847 | Day 6,652, 21:10 | Accepted |
| Donate | 327788 | Day 6,651, 21:08 | Accepted |
| Donate | 327725 | Day 6,650, 21:02 | Accepted |
| Donate | 327676 | Day 6,649, 20:59 | Accepted |
| Donate | 327619 | Day 6,648, 20:09 | Accepted |
| Donate | 327561 | Day 6,647, 19:13 | Accepted |
| Donate | 327504 | Day 6,646, 18:36 | Accepted |
| Donate | 327437 | Day 6,645, 18:30 | Accepted |

> **Observation:** The first row has `class="first"` on all `<td>` elements — subsequent rows do not have this class. This is purely for CSS styling (top border).

#### JavaScript on this Page

| Variable | Value | Description |
|----------|-------|-------------|
| `SERVER_culture` | `"en"` | Language/culture code |
| `SERVER_token` | `"{csrfToken}"` | CSRF token (same as `csrfToken` global) |
| `txt_no_bod` | `"No Campaign of the Day is currently set."` | Campaign of the Day empty state text |
| `txt_set_success` | `"Successfully set as Campaign of the Day"` | Campaign of the Day set confirmation |
| `txt_unset_success` | `"This campaign is no longer Campaign of the Day"` | Campaign of the Day unset confirmation |
| `COUNTRY_ADMINISTRATION_TEXTS` | `{set_text: 'Set', unset_text: 'Unset'}` | Button labels for Campaign of the Day |

The `countrySelector` variable uses `urlTab: "country-administration"` (note: hyphenated, not `/country/administration`).

| Script | Purpose |
|--------|---------|
| `battle_of_td.js` | Campaign of the Day (Battle of the Day) functionality |
| `countryPage.js` | General country page interactions |
| `countrySelector.js` | Country dropdown selector |

> **Campaign of the Day:** The JS variables and `battle_of_td.js` script reveal a feature where the Country President can set a military campaign as the "Campaign of the Day" to prioritize it for citizens. This feature is likely only visible/functional when logged in as president.

#### Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/country-administration/{countryName}/{page}` | Administration page (paginated) |
| `/en/main/law/{countryName}/{lawId}` | Law proposal details page |

#### Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible **without authentication** — but shows reduced functionality with the message "You are not a president or a congress member in this country"
- **Different URL pattern** from other country tabs: uses `/en/country-administration/{countryName}/{page}` instead of `/en/country/{tab}/{countryName}`
- The `{page}` parameter is **required** (not optional) — must be at least `1`
- Law proposals are **paginated** — `ul.pager` contains pagination links when there are more laws than fit on one page
- Law IDs are **sequential integers** across all countries (e.g., `327910`, `327868`) — they can be used with `/en/main/law/{countryName}/{lawId}`
- The `class="first"` on `<td>` elements only appears on the first row — it's for CSS top-border styling
- The **Campaign of the Day** and **Deploy allied forces** features are only functional for the Country President (button is `disabled` for other viewers)
- Law types observed so far: `Donate`, `NaturalEnemy`, `Airstrike`, `DeclareWar`. Additional types likely exist (MPP, Embargo, Tax changes, Impeachment)
- The administration tab link in the country tabs uses the full URL with page number: `/en/country-administration/USA/1`
- Additional CSS: `country.{buildId}.css` (instead of `war_list.css` used by military tab)

---

## Congressional Actions

### Propose Donation (Country Treasury)

**Method:** POST
**URL:** `/en/{countryName}/new-donation`
**Auth Required:** Yes (must be a Congress member)

#### Description

Submits a congressional law proposal to donate currency from the country's treasury to a specified organization or citizen. This action creates a proposal that other Congress members can vote on. The URL pattern differs from the `/en/country/` page endpoints - it uses `/{countryName}/` directly.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country name (e.g., "Lithuania", "USA") |

#### Request Parameters (POST body)

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| requirments | number | Yes | Unknown flag (observed value: `1`). Note: the misspelling "requirments" is in the original API |
| currency | number | Yes | Currency type: `1` = local currency (e.g., LTL), possibly `0` = Gold |
| type_name | string | Yes | Name of the recipient organization/citizen (URL-encoded) |
| value | number | Yes | Amount of currency to donate |
| debate | string | No | Optional debate/discussion text for the proposal |
| commit | string | Yes | Action trigger, value: `Propose` |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Referer | `https://www.erepublik.com/en/{countryName}/new-donation` | Recommended |
| Origin | `https://www.erepublik.com` | Recommended |

#### Example Request (cURL)

```bash
curl -X POST 'https://www.erepublik.com/en/Lithuania/new-donation' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Origin: https://www.erepublik.com' \
  -H 'Referer: https://www.erepublik.com/en/Lithuania/new-donation' \
  --data-raw '_token=YOUR_CSRF_TOKEN&requirments=1&currency=1&type_name=Lietuvos+Taupomasis+Bankas&value=400000&debate=&commit=Propose'
```

#### Example Response

This endpoint returns an **HTML redirect** (HTTP 302), not JSON:
- **On success:** Redirects to the country laws/proposals page where the new proposal appears
- **On failure (not authenticated):** Redirects to homepage (`/en`)

#### Notes

- **Congress members only** - this action requires the authenticated user to be a Congress member of the specified country
- **CSRF token required** - must be obtained from the GET version of the same page (`/en/{countryName}/new-donation`)
- **Unauthenticated access** returns HTTP 302 redirect to `/en` (homepage)
- The `type_name` field is the **display name** of the recipient (organization or citizen), not an ID
- The `requirments` field has a **typo in the original API** (missing 'e') - must be sent as-is
- The `currency` field likely maps to: `1` = local currency, other values may represent Gold
- This is a **form submission endpoint** (not AJAX) - it follows the traditional POST-redirect-GET pattern
- The GET version of the same URL (`/en/{countryName}/new-donation`) shows the donation proposal form
- URL pattern `/{countryName}/new-donation` is different from other country pages which use `/country/{tab}/{countryName}`
- Similar congressional action endpoints likely exist for other proposal types (e.g., natural enemy, MPP, embargo, tax changes)

---

## Global JavaScript Variables (All Country Pages)

These JavaScript variables are embedded in all country pages and provide useful metadata that is **not page-specific**. They appear in a `<script>` block in the `<head>` section.

### Dictatorships and Empires

Two global constants identify current dictatorship and empire countries:

```javascript
const dictatorships = {"47":1,"67":1,"28":1,"26":1,"33":1,"69":1,"170":1,"168":1,"30":1},
      empires = {"35":1};

// Applied via:
erepublik.info.updateCountryInfo(countryId, {is_dictatorship: true});
erepublik.info.updateCountryInfo(countryId, {is_empire: true});
```

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

> **Note:** These values are **dynamic** — they reflect the current game state at the time the page is loaded. Dictatorships and empires change as in-game political events occur (coups, elections, etc.).

### ErpkPvp Configuration

PvP-related configuration, present on all pages when logged in:

```javascript
var ErpkPvp = (ErpkPvp || {});
ErpkPvp.bosh_service = 'unassigned2';
ErpkPvp.muc_host = 'unassigned1';
ErpkPvp.citizenId = 4690052;
```

### SERVER_DATA.countryId (Economy & Military Pages Only)

On Economy and Military pages, the country ID is injected into `SERVER_DATA` via inline script:

```javascript
(SERVER_DATA || (SERVER_DATA = {})).countryId = 167;
```

This makes the viewed country's ID available to donation form JavaScript handlers.

---

## Edge Cases and Empty States

Country pages handle various edge states differently. These patterns apply across all tab types:

### Society Page — No Regions (0 Regions)

When a country has lost all its territory (all regions conquered), the regions section shows an empty table:

```html
<h2 class="section" id="currentRegions">Regions (0)</h2>
<div class="indent">
    <table class="regions"><tr>
        <th width="490px"></th>
        <th width="65px"></th>
    </tr>
    </table>
</div>
```

### Society Page — No Alliance

When a country has no alliance, the `<a class="alliance_name">` element is simply absent from the `#profileholder`:

```html
<div id="profileholder">
    <h1>Albania</h1>
    <p class="padded">
        <a href="//wiki.erepublik.com/index.php/Albania" target="_blank" class="goleft rightpadded">...</a>
        <a class="country_rank" href="...">Rank 25</a>
        <!-- No alliance_name element -->
    </p>
</div>
```

### Politics Page — Dissolved Congress (Dictatorship)

Under dictatorship, the Congress table has headers but **no party rows**:

```html
<table class="political largepadded">
    <tr>
        <th width="210px"></th>
        <th width="85px"></th>
        <th width="305px"></th>
    </tr>
    <!-- No party rows — Congress is dissolved -->
</table>
```

### Military Page — No Active Wars

```html
<tr>
    <td colspan="2">
        This country is not involved in any war.
    </td>
</tr>
```

### Military Page — No Resistance Wars / No MPPs

```html
<!-- No resistance wars -->
<td colspan="3">There are no resistance wars in this country.</td>

<!-- No MPPs -->
<td colspan="3">This country has no mutual protection pacts.</td>
```

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

- [API Table of Contents](API_TOC.md)
- [World & Travel API](API_WORLD.md)
- [Social API](API_SOCIAL.md)
- [Military API](API_MILITARY.md)
- [Economy API](API_ECONOMY.md)
