# Map & Regions

#erepublik #api #world #map #regions

Endpoints for retrieving world map data and region page information.

See also: [Travel](travel.md) | [Cities](cities.md) | [Overview](README.md)

---

## Get World Map Data

**Method:** GET
**URL:** `/en/main/map-data?updated_at={timestamp}`
**Auth Required:** Yes

### Description

Retrieves world map data for all regions (574 total), including current/original country ownership, natural resources, city information, geographic coordinates (bounding boxes, centroids), and active/past battle information. Supports incremental updates via the `updated_at` timestamp parameter, returning only regions modified since that time.

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| updated_at | string | Yes | ISO 8601 timestamp (URL-encoded). Returns regions updated since this time. Example: `2026-02-01T23%3A22%3A47-08%3A00` |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/javascript, */*; q=0.01` | Recommended |

### Example Request

<details>
<summary>cURL Example</summary>

```bash
curl 'https://www.erepublik.com/en/main/map-data?updated_at=2026-02-01T23%3A22%3A47-08%3A00' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01'
```

</details>

### Example Response

The response is an object keyed by region ID (string). The example below shows region `143` (Donbas), which is occupied and has a recently concluded battle:

<details>
<summary>JSON Response</summary>

```json
{
  "143": {
    "resources": [
      {
        "id": "1",
        "image": "/images/resources/1.png",
        "name": "Grain",
        "industry": "food",
        "bonus": "25"
      }
    ],
    "original_country": {
      "id": "40",
      "permalink": "Ukraine",
      "name": "Ukraine",
      "flag": "//www.erepublik.net/images/flags_png/M/Ukraine.png"
    },
    "current_country": {
      "id": "29",
      "permalink": "United-Kingdom",
      "name": "United Kingdom",
      "flag": "//www.erepublik.net/images/flags_png/M/United-Kingdom.png"
    },
    "city": {
      "name": "Donetsk",
      "latitude": 48.015883,
      "longitude": 37.80285,
      "is_capital": false
    },
    "region": {
      "name": "Donbas",
      "id": "143",
      "population": "151",
      "centroid_lat": 48.5119,
      "centroid_lon": 38.34001,
      "bbox_min_lat": 46.87564,
      "bbox_max_lat": 50.06317,
      "bbox_min_lon": 36.53113,
      "bbox_max_lon": 40.15923,
      "area": 52902.34297
    },
    "past_battle_info": {
      "invader": {
        "id": "29",
        "name": "United Kingdom",
        "flag": "//www.erepublik.net/images/flags_png/M/United-Kingdom.png"
      },
      "victim": {
        "id": "40",
        "name": "Ukraine",
        "flag": "//www.erepublik.net/images/flags_png/M/Ukraine.png"
      },
      "war_id": "213028",
      "is_resistance": false,
      "status": "3",
      "id": "871728",
      "finished": "6,667"
    }
  }
}
```

</details>

### Response Structure

- Response is an object keyed by region ID (string)
- Each region contains: `resources`, `original_country`, `current_country`, `city`, `region`
- Optional fields per region: `active_battle_info`, `past_battle_info`

### Response Fields (per region)

#### resources (array)

| Field | Type | Description |
|-------|------|-------------|
| id | string | Resource ID |
| image | string | Relative path to the resource icon image |
| name | string | Resource display name (e.g., "Grain", "Iron", "Oil") |
| industry | string | Industry type: `food` or `weapon` |
| bonus | string | Production bonus percentage (e.g., `"25"`) |

#### original_country / current_country (object)

| Field | Type | Description |
|-------|------|-------------|
| id | string | Country ID |
| permalink | string | URL-friendly country name (e.g., `"United-Kingdom"`) |
| name | string | Country display name |
| flag | string | Protocol-relative URL to the country flag image |

#### city (object)

| Field | Type | Description |
|-------|------|-------------|
| name | string | City name |
| latitude | number | City latitude coordinate |
| longitude | number | City longitude coordinate |
| is_capital | boolean | Whether this city is the country capital |

#### region (object)

| Field | Type | Description |
|-------|------|-------------|
| id | string | Region ID |
| name | string | Region name |
| population | string | Number of residents in the region |
| centroid_lat | number | Centroid latitude of the region |
| centroid_lon | number | Centroid longitude of the region |
| bbox_min_lat | number | Bounding box minimum latitude |
| bbox_max_lat | number | Bounding box maximum latitude |
| bbox_min_lon | number | Bounding box minimum longitude |
| bbox_max_lon | number | Bounding box maximum longitude |
| area | number | Region area in square kilometers |

#### active_battle_info (object, optional)

Present when a battle is currently ongoing in the region. Structure is likely similar to `past_battle_info` (see below); exact fields for active battles are not yet verified.

#### past_battle_info (object, optional)

Present when a battle recently concluded in the region.

| Field | Type | Description |
|-------|------|-------------|
| invader | object | The attacking country (see country sub-object below) |
| victim | object | The defending country (see country sub-object below) |
| war_id | string | War ID this battle belongs to |
| is_resistance | boolean | Whether this was a resistance war |
| status | string | Battle outcome status code |
| id | string | Battle ID |
| finished | string | Formatted number indicating when the battle finished (eRepublik day or tick) |

**Country sub-object** (used in `invader` and `victim`):

| Field | Type | Description |
|-------|------|-------------|
| id | string | Country ID |
| name | string | Country display name |
| flag | string | Protocol-relative URL to the country flag image |

### Notes

- **Incremental Updates:** Use the `updated_at` parameter to fetch only regions that changed since the last request. This is essential for real-time map tracking applications to minimize bandwidth.
- **Response Size:** Full response is approximately 620KB (574 regions). Using incremental updates significantly reduces payload size.
- **Geographic Data:** The bounding box fields (`bbox_min_lat`, `bbox_max_lat`, `bbox_min_lon`, `bbox_max_lon`) and centroid fields (`centroid_lat`, `centroid_lon`) are suitable for map rendering libraries (Leaflet, Mapbox, etc.).
- **Battle Tracking:** Regions with active battles include `active_battle_info`. Recently concluded battles appear in `past_battle_info`. The `active_battle_info` structure is not yet verified from a live response.
- **Occupation Status:** Compare `original_country.id` vs `current_country.id` to determine if a region is occupied.
- **Resources:** Each region can have multiple natural resources providing production bonuses. All numeric-looking fields (`id`, `bonus`, `population`) are returned as strings.
- **Flag URLs:** Flag image URLs use protocol-relative format (`//www.erepublik.net/...`) -- prepend `https:` before use.
- **Timestamp Format:** The `updated_at` parameter must be URL-encoded. Example: `2026-02-01T23:22:47-08:00` becomes `2026-02-01T23%3A22%3A47-08%3A00`.

---

## Get Region Page

**Method:** GET
**URL:** `/en/main/region/{RegionPermalink}`
**Auth Required:** No

### Description

Retrieves a **server-rendered HTML page** containing comprehensive information about a specific region. Unlike most API endpoints that return JSON, this page embeds structured data within HTML elements (`data-` attributes, CSS classes) and inline JavaScript variables. The page does not require authentication and can be accessed with a simple GET request.

The region permalink is the URL-friendly name of the region (e.g., `Lithuania-Minor`, `Rhineland-Westphalia`).

### URL Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| RegionPermalink | string | Yes | URL-friendly region name (e.g., `Lithuania-Minor`) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

### Example Request

<details>
<summary>cURL Example</summary>

```bash
curl -s 'https://www.erepublik.com/en/main/region/Lithuania-Minor'
```

</details>

### Response Format

The response is an HTML page (~38KB). Data is embedded in various HTML structures and JavaScript variables described below.

### Embedded Data Sections

#### 1. Country Info

Found in the page header area and country dropdown.

| Selector / Source | Data |
|-------------------|------|
| `#countrySelectorDropdown option[selected]` | Country ID (`value`), permalink (`data-permalink`) |
| `a.country_rank` | Country rank (e.g., "Rank 39") |
| `a.alliance_name` | Alliance name and link (e.g., `/en/main/alliances/6` for "WOLF"). Only present if the country belongs to an alliance. |
| `span.dictatorship_flag_icon_l` | Dictatorship indicator icon. Present before the country flag image only when the country is under a dictatorship government form. Absent for democracies. |
| Country flag image | Flag URL from `<img>` element near country name |

<details>
<summary>Example HTML (democracy with alliance)</summary>

```html
<option value="48" data-permalink="India" selected>India</option>
<a class="alliance_name" href="//www.erepublik.com/en/main/alliances/6" title="World Organization of Loyalty and Freedom"><span>WOLF</span></a>
```

</details>

<details>
<summary>Example HTML (dictatorship without alliance)</summary>

```html
<span class="dictatorship_flag_icon_l"></span>
<img src="//www.erepublik.net/images/flags/XL/Venezuela.gif" />
```

</details>

#### 2. City & Population

Found in `div.holder.city` elements with ID pattern `cityId_{id}`.

| Selector | Data |
|----------|------|
| `div#cityId_{id}` | Container for city data; ID is the city numeric ID |
| `a.cityLink` | City page URL and name (e.g., `/en/main/city/Klaipeda`) |
| `img.cityEmblem` | City emblem image URL |
| `h2.cityName small` | City role (e.g., "Capital City of Lithuania Minor") |
| `span.cityResidents` | Number of residents (e.g., "274 residents") |
| `span.cityVisitors` | Number of visitors (e.g., "54 visitors") |
| `span.cityVoters small` | Voter registration info. Appears alongside both residents and visitors. Common values: "No registered voters", "No residents with the citizenship of this country". |

<details>
<summary>Example HTML</summary>

```html
<div class="holder city" id="cityId_714">
    <h2 class="section">Population</h2>
    <a href="/en/main/city/Marijampole" class="cityLink">
        <img src="//www.erepublik.net/images/modules/cities/128px/Marijampole.png"
             class="cityEmblem goleft" alt="City Emblem" />
    </a>
    <h2 class="largepadded special cityName">
        <a class="cityLink" href="/en/main/city/Marijampole">Marijampole</a>
        <br>
        <small>(Capital City of Sudovia)</small>
    </h2>
    <p class="special">
        <span class="cityResidents">226 residents</span>
        <span class="cityVoters">
            <small>(No registered voters)</small>
        </span>
    </p>
    <p class="special">
        <span class="cityVisitors">15 visitors</span>
        <span class="cityVoters">
            <small>(No residents with the citizenship of this country)</small>
        </span>
    </p>
</div>
```

</details>

#### 3. Constructions (Building Slots)

Found in `.construction_listing` divs. Each region has **15 building slots**.

| Selector / Attribute | Data |
|----------------------|------|
| `.construction_listing strong` | Slot number (1-15) |
| `span.product_tooltip` | Building in slot |
| `span[industry]` | Industry type ID |
| `span[quality]` | Building quality level |
| `span[id]` | Pattern: `building_{slot}{quality}` |

Industry type IDs:

| ID | Industry |
|----|----------|
| 1 | Food |
| 2 | Weapons |
| 4 | House |
| 6 | Defense Systems |
| 23 | Aircraft Weapons |

<details>
<summary>Example HTML (Defense System in slot 3)</summary>

```html
<span id="building_31" class="product_tooltip" industry="6" quality="1">
    <img src="https://www.erepublik.net/images/icons/industry/6/q1.png" />
</span>
```

</details>

#### 4. Determination

Found in inline `<script>` blocks as JavaScript arrays.

| Variable | Description |
|----------|-------------|
| `regionDeterminations` | Array of active resistance war determinations |
| `defensiveDeterminations` | Array of active defensive determinations |

Both arrays are empty (`[]`) when the region has not been recently conquered.

Each determination object has the following fields:

| Field | Type | Description |
|-------|------|-------------|
| countryId | number | Country ID |
| countryName | string | Country display name |
| permalink | string | URL-friendly country name |
| determination | number | Influence multiplier (e.g., `3.68` means x3.68 influence). **Note:** Values may have floating-point precision artifacts (e.g., `3.8300000000000001` instead of `3.83`) -- round to 2 decimal places when displaying. |
| active | boolean | `true` if this country is the current occupier |

Additional determination text can appear in the HTML:
- "This region has not been conquered in the past 250 days."
- "Liberated: 1023 days ago"
- "This region has no determination when attacked."

The HTML section uses CSS class `hasDetermination` or `noDetermination` on `.indent.productivity.regionDetermination` to indicate state. Active occupier items have the CSS class `active` on `.item`.

<details>
<summary>Example (empty -- no recent conquests)</summary>

```javascript
var regionDeterminations = [];
var defensiveDeterminations = [];
```

</details>

<details>
<summary>Example (populated -- Sudovia occupied by Indonesia)</summary>

```javascript
var regionDeterminations = [
    {
        "countryId": 49,
        "countryName": "Indonesia",
        "permalink": "Indonesia",
        "determination": 3.68,
        "active": true
    },
    {
        "countryId": 166,
        "countryName": "United Arab Emirates",
        "permalink": "United-Arab-Emirates",
        "determination": 3.68,
        "active": false
    }
];
var defensiveDeterminations = [];
```

</details>

<details>
<summary>Example (liberated region -- determinations against multiple past occupiers, none currently active)</summary>

```javascript
var regionDeterminations = [
    {
        "countryId": 24,
        "countryName": "USA",
        "permalink": "USA",
        "determination": 1.49,
        "active": false
    },
    {
        "countryId": 53,
        "countryName": "Portugal",
        "permalink": "Portugal",
        "determination": 1.22,
        "active": false
    }
];
var defensiveDeterminations = [];
```

</details>

> **Note:** A region can have determination entries against multiple countries even when none of them is the current occupier (`active: false` for all). This occurs when a region has been liberated but retains stored determination from past conquests. Different countries may have different determination multipliers based on how long/often they occupied the region.

#### 5. Productivity (Country Bonuses & Region Resources)

**Country bonuses** are found in `.indent.productivity .area.bonus .item`:

| Attribute | Data |
|-----------|------|
| `data-industryId` | Industry type ID |
| `data-bonus` | Bonus percentage (can be decimal, e.g., `22.5`) |
| CSS class `full-bonus` / `partial-bonus` / `noBonus` | Bonus level: `full-bonus` = 100%, `partial-bonus` = 1-99%, `noBonus` = 0% (text shows "No bonus") |

<details>
<summary>Example HTML (Lithuania Minor -- has bonuses in all industries)</summary>

```html
<p data-industryId="1" data-bonus="100" class="amount full-bonus">+100%</p>
<p data-industryId="2" data-bonus="100" class="amount full-bonus">+100%</p>
<p data-industryId="4" data-bonus="5" class="amount partial-bonus">+5%</p>
<p data-industryId="23" data-bonus="25" class="amount partial-bonus">+25%</p>
```

</details>

<details>
<summary>Example HTML (Sudovia -- some industries have no bonus)</summary>

```html
<p data-industryId="1" data-bonus="100" class="amount full-bonus">+100%</p>
<p data-industryId="2" data-bonus="0" class="amount noBonus">No bonus</p>
<p data-industryId="4" data-bonus="0" class="amount noBonus">No bonus</p>
<p data-industryId="23" data-bonus="85" class="amount partial-bonus">+85%</p>
```

</details>

**Region resources** are found in `.area.resource .item`:

| Attribute | Data |
|-----------|------|
| `data-resourceId` | Resource ID |
| `data-bonus` | Bonus percentage from this resource |
| CSS class `type-{industry}` | Which industry the resource benefits |
| CSS class `rarity-{level}` | Resource rarity (see table below) |
| `p.name` | Resource name (e.g., "Grain", "Iron", "Sand", "Neodymium") |
| `div.icon.{name}` | Resource icon element (e.g., `div.icon.grain`, `div.icon.iron`, `div.icon.sand`, `div.icon.neodymium`) |
| `title` attribute | Human-readable bonus description |

Known resource rarities and their bonus percentages:

| Rarity Class | Bonus | Example Resources |
|--------------|-------|-------------------|
| `rarity-verycommon` | +2% | Fish, Iron, Sand, Magnesium |
| `rarity-common` | +3% | Fruits, Aluminum, Wood, Titanium |
| `rarity-uncommon` | +4% | Cattle, Oil, Clay, Wolfram |
| `rarity-rare` | +5% | Grain, Saltpeter, Cobalt, Limestone |
| `rarity-veryrare` | +6% | Deer, Granite, Neodymium |
| `rarity-epic` | -- | (not yet observed) |
| `rarity-legendary` | -- | (not yet observed) |

Known resource industry types on region pages: `type-food`, `type-weapon`, `type-house`, `type-aircraft`.

Known resource IDs (from region page `data-resourceId` attributes):

| ID | Name | Industry | Rarity | Bonus |
|----|------|----------|--------|-------|
| 1 | Grain | Food | rare | +5% |
| 2 | Fish | Food | very common | +2% |
| 3 | Fruits | Food | common | +3% |
| 4 | Cattle | Food | uncommon | +4% |
| 5 | Deer | Food | very rare | +6% |
| 6 | Iron | Weapon | very common | +2% |
| 7 | Saltpeter | Weapon | uncommon | +4% |
| 8 | Aluminum | Weapon | common | +3% |
| 9 | Oil | Weapon | uncommon | +4% |
| 10 | Rubber | Weapon | rare | +5% |
| 11 | Sand | House | very common | +2% |
| 12 | Clay | House | uncommon | +4% |
| 13 | Wood | House | common | +3% |
| 14 | Limestone | House | rare | +5% |
| 15 | Granite | House | very rare | +6% |
| 16 | Neodymium | Aircraft | very rare | +6% |
| 17 | Magnesium | Aircraft | very common | +2% |
| 18 | Cobalt | Aircraft | rare | +5% |
| 19 | Titanium | Aircraft | common | +3% |
| 20 | Wolfram | Aircraft | uncommon | +4% |

> **Note on food resource ID discrepancy:** The region page and the [map-data endpoint](#get-world-map-data) use **different resource ID schemas for food**. On region pages: Grain=1, Fish=2, Fruits=3, Cattle=4, Deer=5. In map-data: Grain=1, Cattle=3, Fish=11. IDs 1 match (Grain), but other food resources have different IDs across the two endpoints. Non-food resource IDs (6-20) appear consistent between both endpoints.
>
> **Note on bonus values:** The map-data endpoint uses a different bonus scale than region pages. For example, Iron shows `bonus: 10` in map-data but `data-bonus="2"` on region pages. Both refer to the same resource (id=6). The map-data bonus appears to represent a percentage of base production, while the region page bonus represents the resource's contribution to the country's total industry bonus.

<details>
<summary>Example HTML (Donbas -- food resources)</summary>

```html
<div data-resourceId="1" data-bonus="5" class="item type-food rarity-rare"
     title="+5% to Food production">
    <p class="name">Grain</p>
    <div class="icon grain" title="+5% to Food production"></div>
    <p class="amount">+5%</p>
</div>
<div data-resourceId="3" data-bonus="3" class="item type-food rarity-common"
     title="+3% to Food production">
    <p class="name">Fruits</p>
    <div class="icon fruits" title="+3% to Food production"></div>
    <p class="amount">+3%</p>
</div>
<div data-resourceId="4" data-bonus="4" class="item type-food rarity-uncommon"
     title="+4% to Food production">
    <p class="name">Cattle</p>
    <div class="icon cattle" title="+4% to Food production"></div>
    <p class="amount">+4%</p>
</div>
<div data-resourceId="5" data-bonus="6" class="item type-food rarity-veryrare"
     title="+6% to Food production">
    <p class="name">Deer</p>
    <div class="icon deer" title="+6% to Food production"></div>
    <p class="amount">+6%</p>
</div>
```

</details>

<details>
<summary>Example HTML (Lithuania Minor -- weapon resources)</summary>

```html
<div data-resourceId="6" data-bonus="2" class="item type-weapon rarity-verycommon"
     title="+2% to Weapon production">
    <p class="name">Iron</p>
</div>
<div data-resourceId="8" data-bonus="3" class="item type-weapon rarity-common"
     title="+3% to Weapon production">
    <p class="name">Aluminum</p>
</div>
```

</details>

<details>
<summary>Example HTML (Mazovia -- house resources)</summary>

```html
<div data-resourceId="11" data-bonus="2" class="item type-house rarity-verycommon"
     title="+2% to House production">
    <p class="name">Sand</p>
    <div class="icon sand" title="+2% to House production"></div>
    <p class="amount">+2%</p>
</div>
<div data-resourceId="15" data-bonus="6" class="item type-house rarity-veryrare"
     title="+6% to House production">
    <p class="name">Granite</p>
    <div class="icon granite" title="+6% to House production"></div>
    <p class="amount">+6%</p>
</div>
```

</details>

<details>
<summary>Example HTML (Maharashtra -- aircraft resources)</summary>

```html
<div data-resourceId="16" data-bonus="6" class="item type-aircraft rarity-veryrare"
     title="+6% to Aircraft production">
    <p class="name">Neodymium</p>
    <div class="icon neodymium" title="+6% to Aircraft production"></div>
    <p class="amount">+6%</p>
</div>
<div data-resourceId="18" data-bonus="5" class="item type-aircraft rarity-rare"
     title="+5% to Aircraft production">
    <p class="name">Cobalt</p>
    <div class="icon cobalt" title="+5% to Aircraft production"></div>
    <p class="amount">+5%</p>
</div>
<div data-resourceId="19" data-bonus="3" class="item type-aircraft rarity-common"
     title="+3% to Aircraft production">
    <p class="name">Titanium</p>
    <div class="icon titanium" title="+3% to Aircraft production"></div>
    <p class="amount">+3%</p>
</div>
<div data-resourceId="20" data-bonus="4" class="item type-aircraft rarity-uncommon"
     title="+4% to Aircraft production">
    <p class="name">Wolfram</p>
    <div class="icon wolfram" title="+4% to Aircraft production"></div>
    <p class="amount">+4%</p>
</div>
```

</details>

> **Note:** Some regions have no resources at all (e.g., Sudovia). In that case, the `.area.resource .row` div is empty -- only containing a `<div class="clear"></div>`.

#### 6. Pollution

Found in the `regionPollutionDetails` JavaScript variable. This is a JSON object keyed by industry ID, where each value is an array of 8 entries (indices 0-7 corresponding to raw/q1-q7).

Industry IDs:

| ID | Industry |
|----|----------|
| 1 | Food |
| 2 | Weapons |
| 4 | House |
| 23 | Aircraft Weapons |

Each entry has:

| Field | Type | Description |
|-------|------|-------------|
| has_pollution | boolean \| 0 | Whether pollution data exists; `0` means N/A for that quality |
| pollution | string | Pollution percentage (e.g., `"5.50%"`, `"0%"`, `"N/A"`) |

<details>
<summary>Example (Lithuania Minor)</summary>

```javascript
var regionPollutionDetails = {
    "1": [
        {"has_pollution": true, "pollution": "1.83%"},
        {"has_pollution": true, "pollution": "5.50%"},
        {"has_pollution": true, "pollution": "7.96%"},
        {"has_pollution": true, "pollution": "1.97%"},
        {"has_pollution": true, "pollution": "0.73%"},
        {"has_pollution": true, "pollution": "5.30%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "0.18%"}
    ],
    "2": [
        {"has_pollution": true, "pollution": "10.08%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": true, "pollution": "25.00%"}
    ],
    "4": [
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": true, "pollution": "0.00%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": 0, "pollution": "N/A"},
        {"has_pollution": 0, "pollution": "N/A"}
    ],
    "23": [
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": false, "pollution": "0%"},
        {"has_pollution": 0, "pollution": "N/A"},
        {"has_pollution": 0, "pollution": "N/A"}
    ]
};
```

</details>

The HTML also contains a `table.pollution` with CSS classes indicating pollution severity:
- `pollution-status-high` -- significant pollution
- `pollution-status-none` -- no pollution
- `industry-{id}` and `quality-{q}` -- identify the cell

#### 7. Neighbors

Found in `table.neighbours`. Each row contains a neighboring region with its current owner country.

| Selector | Data |
|----------|------|
| Row `<a>` href | Link to neighbor region page |
| Row text | Region name |
| Country flag `<img>` | Owner country flag |
| Country name text | Owner country name |

Example neighbors for Lithuania Minor: Samogitia (Lithuania), Kaliningrad (Lithuania), Sudovia (Indonesia), Scania (Sweden).

### Other Embedded JavaScript Variables

| Variable | Description |
|----------|-------------|
| `SERVER_DATA.csrfToken` | CSRF token (useful if making subsequent POST requests) |
| `SERVER_DATA.serverTime` | Server time with RFC, dateTime, timezone, unix timestamp |
| `SERVER_DATA.isLoggedIn` | Whether the current session is authenticated |
| `erepublik.settings.eDay` | Current eRepublik day number |
| `erepublik.settings.culture` | Language code (e.g., `"en"`) |
| `countrySelector` | Country dropdown configuration |

<details>
<summary>Example SERVER_DATA.serverTime</summary>

```json
{
    "rfc": "Fri, 06 Feb 2026 22:04:47 -0800",
    "dateTime": "2026-02-06 22:04:47",
    "timezone": "PST",
    "offset": -800,
    "hour": 22,
    "unix": 1770444287,
    "unixMs": 1770444287000
}
```

</details>

### Notes

- **HTML Response:** Unlike most endpoints in this documentation, this is a full HTML page, not a JSON API. You need an HTML parser (e.g., Jsoup for Java, BeautifulSoup for Python, Cheerio for Node.js) to extract data.
- **No Authentication Required:** The page is publicly accessible. However, authenticated requests may show additional UI elements.
- **Permalink Format:** Region permalinks use hyphens for spaces (e.g., `Lithuania-Minor`, `Rhineland-Westphalia`). Invalid permalinks return a 404 page.
- **Pollution Array Indexing:** Index 0 = raw materials, indices 1-7 = quality levels q1-q7. Houses and Aircraft only go up to q5; indices 6-7 have `has_pollution: 0` and `pollution: "N/A"`.
- **Dynamic Content:** Some sections (like determination arrays) may be populated during wartime. Empty arrays indicate no active resistance or defensive determinations. See the Sudovia example for populated determination data.
- **Dictatorship Indicator:** Countries under dictatorship government display a `<span class="dictatorship_flag_icon_l">` element before the country flag image. This can be used to determine the country's government type.
- **Occupied Regions:** When a region is occupied by a foreign country, the country dropdown shows the occupier as `selected`.
- **Voter Registration:** The `span.cityVoters` element appears on all region pages (not just occupied ones). It shows messages like "No registered voters" alongside residents and "No residents with the citizenship of this country" alongside visitors.
- **Building Slots:** All regions have exactly 15 construction slots. Empty slots have no `<span>` with building data inside.
- **No Resources:** Some regions have no natural resources. The `.area.resource .row` will be empty in this case.

---

**Tags:** #erepublik #api #world #map #regions #geography #resources
