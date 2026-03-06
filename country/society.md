# Country Society Page

#erepublik #api #country #society

[< Back to Country Overview](README.md)

---

Retrieves the country society page as HTML. This is a **web page endpoint**, not a JSON API. The page displays citizen statistics (active citizens, new citizens, average level, online count) for the country compared to the world, and a full list of regions currently controlled by the country -- including capital marker, determination multipliers for occupied regions, and transport route warnings.

**Method:** GET
**URL:** `/en/country/society/{countryName}`
**Auth Required:** No

## Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Bosnia-Herzegovina`). Case-sensitive. Multi-word names use hyphens |

## Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (avoids 403 from Cloudflare) |

## Example Request (cURL)

<details>
<summary>cURL example</summary>

```bash
curl -X GET 'https://www.erepublik.com/en/country/society/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

</details>

## Example Response

Returns a full HTML page (`<body id="country">`) with the following sections:

<details>
<summary>HTML response</summary>

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

</details>

## Page Sections (parsed from HTML)

### 1. Country Header

Displays the country flag, name, rank, and alliance membership.

<details>
<summary>HTML example</summary>

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

</details>

| Data | CSS Selector / Pattern | Example |
|------|----------------------|---------|
| Country name | `#profileholder h1` | `USA` |
| Country rank | `a.country_rank` | `Rank 11` |
| Alliance name | `a.alliance_name span` | `Asteria` |
| Alliance ID | `a.alliance_name` href -> `/en/main/alliances/{allianceId}` | `7` |
| Wiki link | `a[href*="wiki.erepublik.com"]` | `//wiki.erepublik.com/index.php/USA` |
| Country flag | `#filters .bigcore img` src -> `/images/flags/XL/{countryName}.gif` | `USA.gif` |

### 2. Navigation Tabs

Five tabs linking to other country pages:

| Tab | URL Pattern |
|-----|-------------|
| **Society** (current) | `/en/country/society/{countryName}` |
| Economy | `/en/country/economy/{countryName}` |
| Politics | `/en/country/politics/{countryName}` |
| Military | `/en/country/military/{countryName}` |
| Administration | `/en/country-administration/{countryName}/1` |

> **Note:** The Administration tab uses a different URL pattern (`/en/country-administration/` instead of `/en/country/`).

### 3. Citizens Statistics Table

A table comparing country stats to world stats. Parse from `table.citizens`:

<details>
<summary>HTML example</summary>

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

</details>

| Metric | CSS Class | Country Value | World Value |
|--------|-----------|---------------|-------------|
| Active citizens | `span.special` | 980 | 32,764 |
| New citizens today | `span.special` | 5 | 59 |
| Citizenship requests | Link to `/en/country/{countryName}/citizenship/applications/1` | -- | -- |
| Average citizen level | `span.bg-level-green` | 283 | 187 |
| Online now | `span.special` + link to online users page | 39 | 1,130 |

Related URL patterns:
- Online users (country): `/en/main/online-users/{countryName}/all/1`
- Online users (world): `/en/main/online-users/all/all/1`
- Citizenship applications: `/en/country/{countryName}/citizenship/applications/1`

### 4. Regions Table

Lists all regions currently controlled by the country. Parse from `table.regions`. Total count shown in the `<h2>` header:

```html
<h2 class="section" id="currentRegions">Regions (31)</h2>
```

Each region row can contain:

**a) Standard region:**

<details>
<summary>HTML example</summary>

```html
<td>
    <a href="//www.erepublik.com/en/main/region/Colorado" class="fakeheight">Colorado</a>
</td>
```

</details>

**b) Capital region** (marked with `span.icon-capital`):

<details>
<summary>HTML example</summary>

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

</details>

**c) Occupied region with determination multiplier** (foreign territory):

<details>
<summary>HTML example</summary>

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

</details>

**d) Region with no transport route to capital** (disconnected territory):

<details>
<summary>HTML example</summary>

```html
<td>
    <span class="route_container">
        <strong class="route_warning">No direct transport route to the Capital</strong>
    </span>
    <a href="//www.erepublik.com/en/main/region/Alaska" class="fakeheight">Alaska</a>
</td>
```

</details>

| Element | CSS Selector | Description |
|---------|-------------|-------------|
| Region name | `a.fakeheight` | Region display name |
| Region link | `a.fakeheight` href -> `/en/main/region/{regionPermalink}` | Link to region details |
| Capital marker | `span.icon-capital` | Present only on the capital region |
| Determination | `span.influence_multiplier` | Multiplier for occupied foreign regions (e.g., `3.78`) |
| No route warning | `strong.route_warning` | Present when region is disconnected from capital |

### 5. Country Selector Dropdown

A `<select>` dropdown containing **all countries** with their IDs and permalink names. This is a valuable reference for country ID mapping:

<details>
<summary>HTML example</summary>

```html
<select id="countrySelectorDropdown">
    <option value="167" data-permalink="Albania">Albania</option>
    <option value="27" data-permalink="Argentina">Argentina</option>
    ...
    <option value="24" data-permalink="USA" selected>USA</option>
    <option value="28" data-permalink="Venezuela">Venezuela</option>
</select>
```

</details>

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

> **Note:** IDs are not sequential -- gaps exist (e.g., no IDs 2-8, 16-22, 25, 46, 60, 62, 85-163). Total: **74 countries**. See [API_REFERENCE.md](../API_REFERENCE.md) for the complete country and region reference tables.

## JavaScript Variables in Response

The HTML page includes embedded JavaScript with useful data in a `<script>` block in `<head>`:

### `erepublik` object

<details>
<summary>JavaScript example</summary>

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

</details>

| Field | Description |
|-------|-------------|
| `settings.eDay` | Current game day (e.g., `6654`) |
| `settings.culture` | Language code (`en`) |
| `settings.requestUri` | Base64-encoded request URI |
| `shortcuts.links` | Navigation shortcut URLs (useful for discovering other endpoints) |

### `SERVER_DATA` object

<details>
<summary>JavaScript example</summary>

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

</details>

### `Environment` object

<details>
<summary>JavaScript example</summary>

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

</details>

### Other variables

<details>
<summary>JavaScript example</summary>

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

</details>

## Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/main/rankings-countries/1/1` | Country rankings page |
| `/en/main/alliances/{allianceId}` | Alliance details page |
| `/en/main/region/{regionPermalink}` | Region details page |
| `/en/main/online-users/{countryName}/all/1` | Online users for a country |
| `/en/main/online-users/all/all/1` | All online users (world) |
| `/en/country/{countryName}/citizenship/applications/1` | Citizenship applications |
| `/en/country-administration/{countryName}/1` | Country administration page |

## Response Headers (notable)

| Header | Value | Notes |
|--------|-------|-------|
| `Set-Cookie` | `erpk=...` (15min expiry) | Session cookie set even for unauthenticated requests |
| `Set-Cookie` | `erpk_plang=en` (30 day expiry) | Language preference cookie |
| `Server` | Cloudflare (via Varnish 7.1) | May trigger challenges for automated requests |

## Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible without authentication (but Cloudflare may challenge bot-like requests)
- **No AJAX sub-requests** -- unlike some pages, all society data is rendered server-side in the HTML; no additional XHR calls needed
- The country selector dropdown is present on all country tab pages and contains the **complete country ID <-> permalink mapping** -- ideal for building a country reference table
- Regions listed include **foreign territories** the country currently occupies (e.g., USA holding "Guangdong", "Eastern Siberia") -- these have determination multipliers
- Determination multiplier values (e.g., `3.78`) represent the influence bonus enemies get when fighting to liberate that region
- The `route_warning` ("No direct transport route to the Capital") appears on regions geographically disconnected from the capital -- affects travel mechanics
- Country name in URL uses the **permalink format**: multi-word names use hyphens (e.g., `Bosnia-Herzegovina`, `United-Kingdom`, `Republic-of-China-Taiwan`)
- Page structure uses AngularJS 1.8.2 framework
- Cloudflare challenge platform script is injected at the bottom of the page (may affect scraping)

---

## See Also

- [Country Economy Page](economy.md)
- [Country Politics Page](politics.md)
- [Country Military Page](military.md)
- [Country Administration Page](administration.md)
- [Edge Cases and Empty States](edge-cases.md) -- includes no-regions and no-alliance states
