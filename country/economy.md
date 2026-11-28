# Economy

[API docs](../README.md) › Country and politics › Economy

The **Economy** tab of a country: treasury, 60 days of tax revenue and production, resources, concessions, embargoes, taxes and salaries. Citizens of level 25 and above can donate to the treasury from here.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Economy page](#economy-page) | GET | `/en/country/economy/{country}` | ✅ Verified 2026-11-27 |
| [Donate to the country](#donate-to-the-country) | POST | `/en/main/country-donate` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Path rules, page header and country selector: [Society](society.md#common-to-all-country-pages).

---

## Economy page

`GET /en/country/economy/{country}` · No auth (earlier notes) · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/country/economy/Poland' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Section (`h2`) | Where the data is |
|----------------|-------------------|
| [Treasury](#treasury) | `table.donation_status_table` |
| [Tax Revenue (60 days)](#tax-revenue) | `chartDataJSON`, `tableDataJSON` |
| [Gross Domestic Product (60 days)](#gross-domestic-product) | `gdpChartData` |
| [Productivity Information](#productivity) | `countryProductivityBonuses` |
| [Country resources](#country-resources) | `table.resource_list` |
| [Resource Concessions](#resource-concessions) | `#resourcesInConcession`, `#conceededResources` |
| [Trade embargoes](#trade-embargoes) | `table.political.padded` |
| [Taxes](#taxes) | `table.citizens.largepadded` |
| [Salary](#salary) | `table.citizens` |

### Inline variables

| Variable | Type | Description |
|----------|------|-------------|
| `chartDataJSON` | array of arrays | Daily tax revenue by source; first row is the header |
| `tableDataJSON` | array of arrays | The same data transposed, with a `Totals` column |
| `gdpChartData` | array of arrays | Daily production value by industry |
| `countryProductivityBonuses` | object | Production bonuses |
| `countryChartDays` | number | Chart window, `60` |
| `SERVER_DATA.countryId` | number | Viewed country, used by the donation form |

Google Charts (`https://www.gstatic.com/charts/loader.js`) draws the charts.

---

### Treasury

Gold, local currency and Energy. Gold and currency keep their decimals in `<sup>`; Energy has none. Amounts below are placeholders.

```html
<table class="donation_status_table">
  <tr><td><span class="special">0</span><sup>.00</sup></td><td class="c2"><span class="currency">GOLD</span></td></tr>
  <tr><td><span class="special">0</span><sup>.00</sup></td><td class="c2"><span class="currency">PLN</span></td></tr>
  <tr><td><span class="special">0</span></td><td class="c2"><span class="currency">ENERGY</span></td></tr>
</table>
```

The currency code is in `span.currency`, next to the flag image.

### Donation form

A **Contribute** button (`a.donate_open`) reveals `table.donate_table.in_economy` with three rows. The page text says donating requires level 25.

| Row | Current treasury value | Input | Button handler |
|-----|------------------------|-------|----------------|
| Gold | `#currentGoldValue` | `#gold_amount` | `donateGold_onClick_eTab()` |
| Currency | `#currentCurrencyValue` | `#currency_amount` | `donateCurrency_onClick_eTab()` |
| Food | `#currentFoodValue` (Energy) | `#food_amount`, quality in `#img_active` | `donateFood_onClick_eTab()` |

The food quality picker shows `/images/icons/industry/1/q{1..7}_30x30.png` with `data-quality`. All three buttons call [Donate to the country](#donate-to-the-country). Earlier notes: the form appears only when logged in, and the currency flag is that of the viewer's citizenship.

---

### Tax revenue

```javascript
var chartDataJSON = [
  ["Day", "Work as Manager", "Medals", "Import Tax", "Value Added Tax", "Work",
   "Combat Orders", "Treasury Donations", "Cities Subsidy", "Resource Concession Fee",
   "Iran", "Canada"],
  ["Day 6,594", 2365760.93, 114114.93, "…"]
];
```

```javascript
var tableDataJSON = [
  ["Revenue source", "Day 6,594", "Day 6,595", "…", "Totals", "Revenue source"],
  ["Work as Manager", 2365760.93, 2625536.01, "…", 142981353.98, "Work as Manager"]
];
```

| Column | Description |
|--------|-------------|
| `Work as Manager` | Tax from Work as Manager |
| `Work` | Employee work tax |
| `Value Added Tax` | VAT on market sales |
| `Medals` | Medal-related revenue |
| `Import Tax` | Duty on foreign goods |
| `Combat Orders` | Combat order fees |
| `Treasury Donations` | Direct donations to the treasury |
| `Cities Subsidy` | City upkeep, **negative** |
| `Resource Concession Fee` | Rent for [resources leased in](#resource-concessions), **negative**; absent without incoming concessions |
| *Country names* | Revenue from occupied regions of that country; `0` on days without any |

> [!IMPORTANT]
> Column order and presence differ between countries, and columns with no activity in the window may be missing. Always read the header row.

**Concession fee.** The column is one total for all incoming concessions. Each day it equals, per concession, the percentage in its [law](laws.md#question-formats) times that day's GDP of the affected industry (from `gdpChartData`). It cannot be split per resource from this page alone.

<details>
<summary>Validation: Poland, days 6,761–6,820 (earlier notes, 2026-07-23)</summary>

Three incoming concessions: Deer from Brazil (Food, 5%), Rubber from Finland (Weapons, 5%), Limestone from the Netherlands (House, 4%).

```text
predicted[day] = 0.05*gdp.Food[day] + 0.05*gdp.Weapons[day] + 0.04*gdp.House[day]

60-day actual    279,974,012 PLN
60-day predicted 282,304,616 PLN
difference            -0.83%
```

The remainder comes from concessions starting or ending inside the window.

</details>

### Gross domestic product

```javascript
var gdpChartData = [
  ["Day", "Food", "Weapons", "House", "Aircraft Weapons"],
  ["6,887", 14457283, 64399133, 35248763, 1407371]
];
```

The day is written `"6,887"` here and `"Day 6,887"` in the revenue data.

### Productivity

```javascript
var countryProductivityBonuses = {
  "byToken": {"FOOD": 100, "WEAPON": 100, "HOUSE": 100, "AIRCRAFT": 27.5},
  "byId":    {"1": 100, "2": 100, "4": 100, "23": 27.5}
};
```

Also rendered as `<p data-industryId="1" data-bonus="100" class="amount full-bonus">+100%</p>`; classes `full-bonus` (100%), `partial-bonus` (1–99%), `noBonus` (0%). Industry icons: `/images/buildings/{industryId}.png`.

---

### Country resources

`table.resource_list`, columns: Resource · Regions with a Trade Route · Regions without a Trade Route.

```html
<tr>
  <td title="+30% to Weapon production">
    <img src="//www.erepublik.net/images/resources/10.png">
    <span>Rubber</span> <span class="bonus_value">(+30%)</span>
  </td>
  <td><a href="//www.erepublik.com/en/main/region/Lapland">Lapland (<img src="/images/flags_png/S/Finland.png" class="vmiddleFlag">Concession)</a></td>
  <td><small>Not available</small></td>
</tr>
```

> [!IMPORTANT]
> `span.bonus_value` and `td[title]` show what **this country** gets, not a property of the resource. Identify a resource by its icon ID.

| Country holds the resource | Shown value | `td[title]` |
|----------------------------|-------------|-------------|
| In a routed region, or by concession | Full base bonus | `+X% to {Industry} production` |
| Only in regions without a route | Half the base bonus | `+X% to {Industry} production` |
| Both | Full base bonus (not a sum) | `+X% to {Industry} production` |
| Not at all | `(+0%)` | Empty |

| Data | Selector or test |
|------|------------------|
| Regions | `td:nth-child(2) a`, `td:nth-child(3) a`, comma-separated |
| Received by concession | Link text contains `(Concession)`; counted in the routed column |
| Source country of a concession | Only the file name of `img.vmiddleFlag` (`/images/flags_png/S/{Country}.png`) |

Base bonus depends only on rarity, the same in every industry. All five routed resources of an industry reach the 100% cap. See [[Productivity#Resource Bonus Percentages]] and [[Raw_Materials]].

| ID | Resource | Industry | Rarity | Base bonus |
|----|----------|----------|--------|------------|
| 1 | Grain | Food | rare | 25% |
| 2 | Fish | Food | very common | 10% |
| 3 | Fruits | Food | common | 15% |
| 4 | Cattle | Food | uncommon | 20% |
| 5 | Deer | Food | very rare | 30% |
| 6 | Iron | Weapon | very common | 10% |
| 7 | Saltpeter | Weapon | rare | 25% |
| 8 | Aluminum | Weapon | common | 15% |
| 9 | Oil | Weapon | uncommon | 20% |
| 10 | Rubber | Weapon | very rare | 30% |
| 11 | Sand | House | very common | 10% |
| 12 | Clay | House | uncommon | 20% |
| 13 | Wood | House | common | 15% |
| 14 | Limestone | House | rare | 25% |
| 15 | Granite | House | very rare | 30% |
| 16 | Neodymium | Aircraft | very rare | 30% |
| 17 | Magnesium | Aircraft | very common | 10% |
| 18 | Cobalt | Aircraft | rare | 25% |
| 19 | Titanium | Aircraft | common | 15% |
| 20 | Wolfram | Aircraft | uncommon | 20% |

Resource icons: `/images/resources/{id}.png`.

<details>
<summary>Evidence: maximum seen per resource across 58 countries (earlier notes, 2026-07-23)</summary>

| Resources | Maximum | Country, routed column |
|-----------|---------|------------------------|
| Grain / Fish / Fruits / Cattle / Deer | 25 / 10 / 15 / 20 / 30% | USA |
| Iron / Saltpeter / Aluminum / Oil / Rubber | 10 / 25 / 15 / 20 / 30% | USA |
| Sand / Clay / Wood / Limestone / Granite | 10 / 20 / 15 / 25 / 30% | Venezuela |
| Neodymium | 30% | Romania |
| Magnesium, Cobalt, Wolfram | 10%, 25%, 20% | Bulgaria |
| Titanium | 15% | Serbia |

Half values: China Grain 12.5% (no route); Poland Titanium 7.5%, Neodymium 15%, Magnesium 5% (all without a route). Croatia Deer 30% with the resource in both columns.

</details>

### Resource concessions

Two tables, `#resourcesInConcession` (received) and `#conceededResources` (given away; the double `e` is in the original HTML).

| Data | Selector | Example |
|------|----------|---------|
| Row | `tr.active` | The header row lacks the class |
| Counterparty | `td:nth-child(2) a` | Links to its economy page |
| Resource ID | `td:nth-child(3) img[src]` | `/images/resources/14.png` |
| Resource and region | `td:nth-child(4) span` | `Limestone (Southern Netherlands)` |
| Expiry | `td:nth-child(5)` | `Expires in 14 days`, `Expires tomorrow` |

Empty tables show `No resources in concession from other countries.` and `No Resources conceded to other countries.` The fee percentage is not on this page, only in the [concession law](laws.md#question-formats).

### Trade embargoes

Without embargoes the table says `This country can trade with any other country in eRepublik.` An active embargo (earlier notes):

```html
<tr>
  <td><img src="//www.erepublik.net/images/flags/L/Russia.gif">
      <a href="//www.erepublik.com/en/country/society/Russia" class="dotted">Russia</a></td>
  <td><img src="//www.erepublik.net/images/parts/icon-pact.gif"> Expires in 25 days</td>
</tr>
```

### Taxes

`table.citizens.largepadded`: industry, Work Tax, Import Tax, VAT. The industry ID is in the icon URL `/images/icons/industry/{id}/default.png`. Raw material rows have an empty VAT cell.

| Industry | ID | VAT |
|----------|----|-----|
| Food | 1 | Yes |
| Weapons | 2 | Yes |
| Moving Tickets | 3 | Yes |
| House | 4 | Yes |
| Aircraft Weapons | 23 | Yes |
| Food Raw Materials | 7 | No |
| Weapon Raw Materials | 12 | No |
| House Raw Materials | 17 | No |
| Aircraft Weapons Raw Materials | 24 | No |

Seen for Food in Poland: work tax `1.00%`, import tax `99%`, VAT `1%`.

### Salary

`table.citizens`: **Minimum** and **Average**, in local currency (for example `8.00 PLN`). The minimum is set by a [Minimum Wage law](laws.md#law-types).

**Source:** real request, 2026-11-27; concession fee analysis, bonus rules and embargo markup — earlier notes.

---

## Donate to the country

`POST /en/main/country-donate` · Auth required · 🔍 From code

> [!WARNING]
> Moves your gold, currency or food to the country treasury. The page text requires level 25.

Also used by the [airstrike form](military.md#airstrike) on the Military tab.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `countryId` | number | Yes | `SERVER_DATA.countryId` |
| `action` | string | Yes | `gold`, `currency` or `food` |
| `value` | number | Yes | Amount, or number of food units |
| `quality` | number | For `food` | `1`–`7`; the client sends `1` if nothing was picked |

```bash
curl 'https://www.erepublik.com/en/main/country-donate' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'countryId=35&action=food&value=10&quality=1&_token=YOUR_CSRF_TOKEN'
```

### Response

Shape reconstructed from what the client reads:

```json
{ "error": false, "message": "…", "params": { "amount": 0, "currency": "PLN", "remaining": 0 } }
```

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | `true` makes the client show `message` as an error |
| `message` | string | Text to display |
| `params.amount` | number | Added to the treasury figure on the page |
| `params.currency` | string | Shown after the amount (gold and currency) |
| `params.remaining` | number | Your new balance (gold and currency) |
| `params.has_food`, `params.has_special_food` | number | Food only: below `1` changes the energy button |

**Source:** `countryPage.js` (version 1698060179).

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Routeless resources | One revision said they give no bonus | Half the bonus (verified 2026-07-23) | Half |
| Food donation target | "Food or Energy" | Treasury is shown in Energy | Energy |
