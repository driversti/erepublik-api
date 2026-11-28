# Military

[API docs](../README.md) › Country and politics › Military

The **Military** tab of a country: natural enemy, defence shield, airstrike readiness, active wars and resistance wars, and mutual protection pacts (MPP). The president sets which allies are deployed first from the [Administration](administration.md) tab.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Military page](#military-page) | GET | `/en/country/military/{country}` | ✅ Verified 2026-11-27 |
| [All mutual protection pacts](#all-mutual-protection-pacts) | GET | `/en/list/mpp/{countryId}/{page}` | ✅ Verified 2026-11-27 |
| [Allied forces popup](#allied-forces-popup) | GET | `/en/main/country-mpp-priority` | ✅ Verified 2026-11-27 |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Path rules, page header and country selector: [Society](society.md#common-to-all-country-pages).

Related: [Wars and resistance wars](../military/wars.md) · [Campaigns](../military/campaigns.md)

---

## Military page

`GET /en/country/military/{country}` · No auth (earlier notes) · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/country/military/Poland' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

All data is in the HTML. The only variable of note is `countrySelector`. Scripts: `jquery.knob.js` (gauges), `allies_dropdown.js`, `countryPage.js`, `countrySelector.js`.

### Natural Enemy

Citizens get +10% damage against the natural enemy.

| State | Content of `div.indent > div.attacker` |
|-------|----------------------------------------|
| None | `div.nameholder`: `No current Natural Enemy` |
| Set | Flag (`/images/flags_png/L/{country}.png`), `a.dotted` to the country, then `Since: Day 6,371, 11:40 (9 months ago)` |

### Defence Shield

`div.defence_shield`: `Defence Shield: 0 damage left`. The page explains: when a region is attacked, the country gets a damage bonus equal to the shield capacity divided by the number of regions owned.

### Airstrike

Food (as Energy) and currency collected so that the president can declare war on a country without a shared border. Contributing requires level 25. Current amounts below are placeholders.

```html
<input id="health_units" data-req="285500" class="knob" value="0" readonly>
<span><strong>Energy Units required:</strong> <em id="health_unit_cur">0</em> / 285,500</span>
<input id="currency_req" data-req="375000" class="knob" value="0" readonly>
<span><strong>Currency required:</strong> <em id="currency_req_cur">0</em> / 375,000</span>
```

| Data | Selector | Notes |
|------|----------|-------|
| Requirement | `input[data-req]` | Differs by country and may be fractional (`66666.666666667`) |
| Collected | `#health_unit_cur`, `#currency_req_cur` | Can exceed the requirement |
| Gauge fill | `input[value]` | `0`–`100` |

Requirements seen:

| Country | Energy | Currency | Date |
|---------|--------|----------|------|
| Poland | 285,500 | 375,000 | 2026-11-27 |
| USA | 245,000 | 240,000 | Earlier notes |
| Lithuania | 45,750 | 270,000 | Earlier notes |
| Albania | 103,250 | 66,666.67 | Earlier notes |

**Donation form** (`table.donate_table`, food quality 1–7 and currency, no gold). Its buttons call `donateFood_onClick()` and `donateCurrency_onClick()`, which use [Donate to the country](economy.md#donate-to-the-country) with `action` `food` or `currency`.

### Active wars

`h2` **Active wars in {country}**, `table.political.largepadded`, one `div.warholder-small` per war:

```html
<div class="warholder-small">
  <div class="attacker">
    <div class="flagholder"><a href="//www.erepublik.com/en/country/society/{country}">
      <img src="//www.erepublik.net/images/flags_png/XL/{country}.png" width="48"></a></div>
    <div class="nameholder"><a class="dotted" href="…">Country</a></div>
    <a href="javascript:;" class="show_allies">1 allies
      <div class="allies_tooltip left_side"><ul>
        <li><img src="//www.erepublik.net/images/flags_png/S/Estonia.png">Estonia</li>
      </ul></div></a>
  </div>
  <div class="defender"><!-- same structure --></div>
  <div class="middle">VS</div>
</div>
```

| Data | Selector |
|------|----------|
| Sides | `div.attacker`, `div.defender` → `.nameholder a.dotted`, `.flagholder img` |
| No allies | `a.none[title="no allies"]` |
| Allies | `a.show_allies` (`{N} allies`), names in `.allies_tooltip li` |
| [War page](../military/wars.md) | `a[href*="wars/show"]` → `/en/wars/show/{warId}` |

The viewed country may be on either side. Without wars: `This country is not involved in any war.`

Below the list, the **All wars** button submits a hidden form to `/en/military/campaigns` with `_token`, `fwt=conquest`, `fws=all`, `fwc=all`.

<details>
<summary>Example: USA's wars, day 6,654 (earlier notes)</summary>

| Attacker | Defender | War ID |
|----------|----------|--------|
| Romania | USA | 192366 |
| Canada (ally: Estonia) | USA | 192823 |
| Poland (ally: Finland) | USA | 192904 |
| USA | France | 214786 |
| USA | Cuba | 222375 |

</details>

### Active resistance wars

`h2` **Active resistance wars in {country}**, `table.political.padded`, three columns:

| Data | Selector | Example |
|------|----------|---------|
| Force | Text after `img[src*="Resistance.png"]` | `Resistance Force of Canada` |
| Determination | `span.influence_multiplier` | `3.04`: influence bonus of that side |
| War | `a[href*="wars/show"]` | `/en/wars/show/{warId}` |

Without any: `There are no resistance wars in this country.`

### Mutual Protection Pacts

`table.political.padded`, two columns:

```html
<tr>
  <td class="last"><span class="fakeheight">
    <img src="//www.erepublik.net/images/flags_png/L/Finland.png" class="flag">
    <a href="//www.erepublik.com/en/country/society/Finland" class="dotted">Finland</a></span></td>
  <td class="last"><img src="//www.erepublik.net/images/parts/icon-pact.gif" class="icon"> Expires in 9 days</td>
</tr>
```

Only relative expiry text is given. Without pacts: `This country has no mutual protection pacts.`

**Source:** real request, 2026-11-27; empty states, natural enemy markup and war examples — earlier notes.

---

## All mutual protection pacts

`GET /en/list/mpp/{countryId}/{page}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | Country filter; `0` for all |
| `page` | path | number | Yes | Starts at `1` |

```bash
curl 'https://www.erepublik.com/en/list/mpp/0/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

HTML `table.offers`, one pact per row: first country, second country, `Expires in …`.

**Source:** real request, 2026-11-27 (`0/1`); country filter URL from `election/main.js`.

---

## Allied forces popup

`GET /en/main/country-mpp-priority` · Auth required · ✅ Verified 2026-11-27 (without the role)

The **Deploy allied forces** popup (`generalPopup.renderPopup('mppPriority')`), opened from [Administration](administration.md#military-priorities), the campaigns screen and the battlefield.

```bash
curl 'https://www.erepublik.com/en/main/country-mpp-priority' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

For a citizen without the right, an HTML fragment:

```html
<div class="global_pack mppPriority">
  <div class="pack_header"><h2>Deploy Allied Forces</h2></div>
  <div class="tab_wrapper"><div class="tab_content"><p class="errorMessage">Not allowed</p></div></div>
  <div class="footer"><a id="saveMppPriority" class="std_global_btn saveMppPriority">Save</a></div>
</div>
```

**Source:** real request, 2026-11-27; popup map in `erepublik.js` (version 1795779896).

---

## Open questions

| Topic | Notes |
|-------|-------|
| President's popup | Its content and the request behind **Save** were not seen; the common scripts contain no handler |
| Airstrike donation | Earlier notes mention a level 25 and logged-in condition for the form; the level text is on the page |
