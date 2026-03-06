# Country Economy Page

#erepublik #api #country #economy

[< Back to Country Overview](README.md)

---

Retrieves the country economy page as HTML. This is the **richest country page for scraping** -- it contains three major JavaScript data variables (`chartDataJSON`, `gdpChartData`, `countryProductivityBonuses`) plus extensive HTML tables covering treasury, resources, taxes, embargoes, concessions, and salary data.

**Method:** GET
**URL:** `/en/country/economy/{countryName}`
**Auth Required:** No

## Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Bosnia-Herzegovina`). Case-sensitive, multi-word names use hyphens |

## Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (avoids 403 from Cloudflare) |

## Example Request (cURL)

<details>
<summary>cURL example</summary>

```bash
curl -X GET 'https://www.erepublik.com/en/country/economy/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

</details>

## Example Response

Returns a full HTML page (`<body id="country">`) wrapped in `<div id="economy">`:

<details>
<summary>HTML response</summary>

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

</details>

## Page Sections (parsed from HTML)

### 1. Treasury

Displays current Gold, local currency, and Energy reserves. Parse from `table.donation_status_table`:

<details>
<summary>HTML example</summary>

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

</details>

| Resource | CSS Selector | Example Value |
|----------|-------------|---------------|
| Gold | `span.special` + `sup` (row 1) | 1,263.42 |
| Local Currency | `span.special` + `sup` (row 2) | 1,448,093,816.58 USD |
| Energy | `span.special` (row 3, no `sup`) | 34,090 |

> **Parsing note:** Gold and Currency have decimal parts in `<sup>` tags. Energy has no decimals. The currency code (e.g., `USD`, `LTL`) comes from the `span.currency` text and the flag image (`/images/flags/M/{countryName}.gif`).

### 1b. Treasury Donation Form (Logged-in Only)

When authenticated (level 25+), a "Contribute" button appears below the treasury table. Clicking it reveals a donation form with three rows: Gold, Currency, and Food. The form also includes a food quality selector (q1-q7).

<details>
<summary>HTML example</summary>

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

</details>

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

> **Note:** The donation form uses `_eTab` suffix on function names (economy tab). The [Military page's](military.md) airstrike section uses the same form but without Gold and without the `_eTab` suffix (uses `donateFood_onClick()` and `donateCurrency_onClick()`). The currency flag shown is the **viewer's citizenship currency** flag (e.g., Lithuanian flag for LTL), not the viewed country's flag. Food quality icons follow pattern: `/images/icons/industry/1/q{N}_30x30.png` where N is 1-7. The `info_message` above the treasury explains that "To donate, you must be level 25 or above."

### 2. Tax Revenue (60 days) -- JavaScript Variables

The most valuable data on this page. Three JS variables contain 60 days of daily tax revenue:

**`chartDataJSON`** -- Array of arrays for chart rendering. First row is column headers, subsequent rows are daily data:

<details>
<summary>JavaScript example</summary>

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

</details>

**`tableDataJSON`** -- Same data transposed for table view. Rows are revenue sources, columns are days. **Includes a "Totals" column** at the end:

<details>
<summary>JavaScript example</summary>

```javascript
var tableDataJSON = [
  ["Revenue source", "Day 6,594", "Day 6,595", ..., "Totals", "Revenue source"],
  ["Work as Manager", 2365760.93, 2625536.01, ..., 142981353.98, "Work as Manager"],
  ["Work", 114114.93, 120430.81, ..., 7764237.16, "Work"],
  ...
];
```

</details>

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

> **Important:** The column order is **NOT fixed** -- it varies between countries. For example:
> - USA: `Work as Manager, Work, Value Added Tax, Medals, Import Tax, Combat Orders, Treasury Donations, Cities Subsidy`
> - Albania: `Work as Manager, Value Added Tax, Import Tax, Medals, Work, Combat Orders, Cities Subsidy` (no Treasury Donations column)
>
> Columns that have had **zero activity** across the entire 60-day period may be **omitted entirely** (e.g., Albania has no `Treasury Donations` column). Always parse column headers from the first row rather than assuming a fixed order.

**Revenue source columns (dynamic -- conquered regions):**

After the standard columns, additional columns appear for each country whose regions are currently occupied. These are named by country (e.g., `"Paraguay"`, `"Turkey"`, `"Croatia"`). A value of `0` on a given day means no revenue from that country's regions that day. Countries may appear/disappear as territories change hands -- e.g., Albania's `"Croatia"` column shows revenue only for days 6,632-6,645 (when Albania held Croatian regions).

### 3. Gross Domestic Product (60 days) -- JavaScript Variable

**`gdpChartData`** -- 60 days of daily production values by industry:

<details>
<summary>JavaScript example</summary>

```javascript
var gdpChartData = [
  ["Day", "Food", "Weapons", "House", "Aircraft Weapons"],
  ["6,594", 5341423, 8676609, 840137, 377672],
  ["6,595", 5732159, 9128124, 945382, 418872],
  ...
];
```

</details>

| Column | Description |
|--------|-------------|
| Food | Total food production value |
| Weapons | Total weapons production value |
| House | Total house production value |
| Aircraft Weapons | Total aircraft weapons production value |

> **Note:** GDP day format uses just the number (e.g., `"6,594"`) while tax revenue uses `"Day 6,594"`.

### 4. Productivity Information -- JavaScript Variable

**`countryProductivityBonuses`** -- Production bonuses available in two formats:

<details>
<summary>JavaScript example</summary>

```javascript
var countryProductivityBonuses = {
  "byToken": {"FOOD": 100, "WEAPON": 100, "HOUSE": 0, "AIRCRAFT": 50},
  "byId": {"1": 100, "2": 100, "4": 0, "23": 50}
};
```

</details>

Also rendered in HTML with `data-*` attributes:

<details>
<summary>HTML example</summary>

```html
<div class="item">
    <p class="name">Food</p>
    <img src="//www.erepublik.net/images/buildings/1.png" />
    <p data-industryId="1" data-bonus="100" class="amount full-bonus">+100%</p>
</div>
```

</details>

| Industry ID | Token | Icon Path | Bonus CSS Classes |
|-------------|-------|-----------|-------------------|
| 1 | FOOD | `/images/buildings/1.png` | `full-bonus` (100%), `partial-bonus` (1-99%), `noBonus` (0%) |
| 2 | WEAPON | `/images/buildings/2.png` | Same pattern |
| 4 | HOUSE | `/images/buildings/4.png` | Same pattern |
| 23 | AIRCRAFT | `/images/buildings/23.png` | Same pattern |

### 5. Country Resources Table

Lists all 20 resource types with their production bonuses and which regions contain them. Parse from `table.resource_list`:

<details>
<summary>HTML example</summary>

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

</details>

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

> **Key distinction:** Resources in "Regions with a Trade Route" contribute to the country's production bonus. Resources in "Regions without a Trade Route" exist in disconnected territories and **do not contribute** to the bonus (see the [society page's](society.md) `route_warning` for the same regions).

### 6. Resource Concessions

Two tables showing concessions in and out:

<details>
<summary>HTML example</summary>

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

</details>

> **Note:** The typo `conceededResources` (double 'e') is in the original HTML.

### 7. Trade Embargoes

Lists active embargoes with expiry countdown:

<details>
<summary>HTML example</summary>

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

</details>

### 8. Taxes Table

Tax rates per industry. Parse from `table.citizens.largepadded` inside the Taxes section:

<details>
<summary>HTML example</summary>

```html
<tr>
    <td><img src="https://www.erepublik.net/images/icons/industry/1/default.png" /></td>
    <td><span class="fakeheight">Food</span></td>
    <td><span class="special">1.00%</span></td>    <!-- Work Tax -->
    <td><span class="special">1</span>%</td>        <!-- Import Tax -->
    <td><span class="special">1</span>%</td>        <!-- VAT -->
</tr>
```

</details>

Industry icon URL pattern: `/images/icons/industry/{industryId}/default.png`

| Industry | ID | Work Tax | Import Tax | VAT |
|----------|-----|----------|------------|-----|
| Food | 1 | 1.00% | 1% | 1% |
| Weapons | 2 | 1.00% | 1% | 1% |
| Aircraft Weapons | 23 | 1.00% | 1% | 1% |
| Moving Tickets | 3 | 1.00% | 1% | 1% |
| House | 4 | 1.00% | 5% | 1% |
| Food Raw Materials | 7 | 1.00% | 1% | -- |
| Weapon Raw Materials | 12 | 1.00% | 1% | -- |
| Aircraft Weapons Raw Materials | 24 | 1.00% | 1% | -- |
| House Raw Materials | 17 | 1.00% | 1% | -- |

> **Note:** Raw material industries have **no VAT** (empty `<span class="special"></span>`). The industry IDs are extracted from the icon image URL.

### 9. Salary Data

<details>
<summary>HTML example</summary>

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

</details>

| Metric | Example Value |
|--------|---------------|
| Minimum Salary | 1.00 USD |
| Average Salary | 6,636.52 USD |

## JavaScript Variables Summary

| Variable | Type | Description |
|----------|------|-------------|
| `chartDataJSON` | `Array[][]` | 60 days of daily tax revenue by source (chart format) |
| `tableDataJSON` | `Array[][]` | Same data transposed with totals (table format) |
| `gdpChartData` | `Array[][]` | 60 days of daily GDP by industry |
| `countryProductivityBonuses` | `Object` | Production bonuses in `byToken` and `byId` formats |
| `countryChartDays` | `number` | Chart time window (default: `60`) |
| `countrySelector` | `Object` | Country selector config with `urlTab: "country/economy"` |

## Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible without authentication (but Cloudflare may challenge bot-like requests)
- **Most data-rich country page** -- contains 3 major JS data variables ideal for scraping economic data
- Tax revenue `chartDataJSON` columns are **dynamic** -- both the order and presence of standard columns varies between countries, and conquered region columns appear/disappear as territories change. Always parse column headers from the first row
- The `tableDataJSON` includes a **Totals column** with 60-day aggregates -- useful for quick summaries without summing yourself
- **Cities Subsidy** is always negative (it's an expense, not revenue)
- `chartDataJSON` and `tableDataJSON` contain the exact same data in different orientations (chart vs table)
- GDP day format differs from tax revenue: `"6,594"` vs `"Day 6,594"`
- Tax rates and salary data must be parsed from HTML (not in JS variables)
- Resources split into "with Trade Route" vs "without Trade Route" -- only trade-route resources contribute to production bonus
- Google Charts library (`https://www.gstatic.com/charts/loader.js`) is loaded for chart rendering
- Industry IDs are consistent across all sections: `1`=Food, `2`=Weapons, `3`=Moving Tickets, `4`=House, `7`=Food RM, `12`=Weapon RM, `17`=House RM, `23`=Aircraft Weapons, `24`=Aircraft RM

---

## See Also

- [Country Society Page](society.md)
- [Country Politics Page](politics.md)
- [Country Military Page](military.md) -- airstrike donation form comparison
- [Country Administration Page](administration.md)
- [Congressional Donations](congress.md)
