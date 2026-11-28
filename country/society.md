# Society

[API docs](../README.md) › Country and politics › Society

The **Society** tab of a country: citizen statistics, requests for citizenship and the list of regions the country controls. The "Who?" links open the list of citizens who are online.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Society page](#society-page) | GET | `/en/country/society/{country}` | ✅ Verified 2026-11-27 |
| [Online citizens](#online-citizens) | GET | `/en/main/online-users/{country}/all/{page}` | ✅ Verified 2026-11-27 |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

Other tabs: [Economy](economy.md) · [Politics](politics.md) · [Military](military.md) · [Administration](administration.md)

---

## Common to all country pages

### Path

`{country}` is the country **permalink**: case-sensitive, spaces replaced by hyphens (`USA`, `Bosnia-Herzegovina`, `Republic-of-China-Taiwan`, `United-Arab-Emirates`). All tabs are server-rendered HTML. Four use `/en/country/{tab}/{country}`; Administration uses `/en/country-administration/{country}/{page}`.

Earlier notes: Society, Economy, Politics, Military and Administration can be read **without** a session (`<body class="logged_out">`); an anonymous request still receives an `erpk` cookie (15-minute expiry), and Cloudflare may challenge it without a browser `User-Agent`. Anonymous responses also set `erpk_plang` (30-day expiry) and came through Cloudflare. The checks on 2026-11-27 were made logged in.

### Page header

| Data | Selector | Example |
|------|----------|---------|
| Name | `#profileholder h1` | `Poland` |
| Flag | `#filters .bigcore img` | `/images/flags/XL/{country}.gif` |
| Empire mark | `#filters .empire_flag_deco` | Present for an empire |
| Rank | `a.country_rank` | `Rank 10`, links to the [country rankings](../rankings/rankings.md#countries) |
| Alliance | `a.alliance_name span` | `Nexus`; the element is absent without an alliance |
| Alliance ID | `a.alliance_name` href | `/en/main/alliances/{allianceId}`, see [Alliances](alliances.md) |
| Wiki | `a[href*="wiki.erepublik.com"]` | `//wiki.erepublik.com/index.php/{country}` |

### Country selector

`select#countrySelectorDropdown` holds every country: `<option value="{countryId}" data-permalink="{country}">`. The script `countrySelector.js` redirects to `/en/{countrySelector.urlTab}/{permalink}`; each tab sets `urlTab` inline (`country/society`, `country/economy`, `country/politics`, `country/military`, `country-administration`). Country IDs are not sequential.

<details>
<summary>Country IDs (74 countries, earlier notes)</summary>

| IDs and permalinks |
|--------------------|
| 1 Romania · 9 Brazil · 10 Italy · 11 France · 12 Germany · 13 Hungary · 14 China · 15 Spain |
| 23 Canada · 24 USA · 26 Mexico · 27 Argentina · 28 Venezuela · 29 United-Kingdom · 30 Switzerland · 31 Netherlands |
| 32 Belgium · 33 Austria · 34 Czech-Republic · 35 Poland · 36 Slovakia · 37 Norway · 38 Sweden · 39 Finland |
| 40 Ukraine · 41 Russia · 42 Bulgaria · 43 Turkey · 44 Greece · 45 Japan · 47 South-Korea · 48 India |
| 49 Indonesia · 50 Australia · 51 South-Africa · 52 Republic-of-Moldova · 53 Portugal · 54 Ireland · 55 Denmark · 56 Iran |
| 57 Pakistan · 58 Israel · 59 Thailand · 61 Slovenia · 63 Croatia · 64 Chile · 65 Serbia · 66 Malaysia |
| 67 Philippines · 68 Singapore · 69 Bosnia-Herzegovina · 70 Estonia · 71 Latvia · 72 Lithuania · 73 North-Korea · 74 Uruguay |
| 75 Paraguay · 76 Bolivia · 77 Peru · 78 Colombia · 79 North-Macedonia · 80 Montenegro · 81 Republic-of-China-Taiwan · 82 Cyprus |
| 83 Belarus · 84 New-Zealand · 164 Saudi-Arabia · 165 Egypt · 166 United-Arab-Emirates · 167 Albania · 168 Georgia · 169 Armenia |
| 170 Nigeria · 171 Cuba |

The same tables, with regions, are in the [ID tables](../basics/id-tables.md).

</details>

### Inline JavaScript

Country pages carry two maps of countries currently ruled differently. They are game state, not constants:

```javascript
var dictatorships = {"47": 1, "67": 1, "14": 1},   // shortened
    empires = {"35": 1};
// applied to erepublik.info.countries[id] (isDictatorship / isEmpire)
```

Earlier notes show the same data as `const` declarations applied through `erepublik.info.updateCountryInfo(countryId, {...})`, and list the usual page globals: `erepublik.settings` (`eDay`, `culture`, `requestUri` in Base64), `erepublik.shortcuts.links`, `SERVER_DATA` (`csrfToken`, `isLoggedIn`, `serverTime`, …), `Environment`, `ErpkShop`, and for logged-in users `ErpkPvp` (`bosh_service`, `muc_host`, `citizenId`). See [Client framework](../basics/framework.md#javascript-globals). The Economy and Military tabs also set `SERVER_DATA.countryId` to the viewed country.

---

## Society page

`GET /en/country/society/{country}` · No auth (earlier notes) · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/country/society/Poland' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Citizen statistics

`table.citizens.largepadded`, columns: icon, metric, the country, `World`.

| Metric | Country | World | Notes |
|--------|---------|-------|-------|
| Active citizens | `1,008` | `32,246` | |
| New citizens today | `2` | `58` | |
| Citizenship requests | `View requests` | — | Links to [citizenship applications](administration.md#citizenship-applications) |
| Average citizen level | `312` | `206` | |
| Online now | `122 (Who?)` | `1863 (Who?)` | Links to [online citizens](#online-citizens) |

### Regions

`h2#currentRegions` shows the count (`Regions (20)`); `table.regions` has one row per region with a **Details** button.

```html
<tr>
  <td>
    <span class="goright influence_multiplier inactive"
          title="This region's Determination to be free. Soldiers will receive x6.00 Influence when fighting for its freedom.">6.00<span></span></span>
    <span class="route_container"><strong class="route_warning">No direct transport route to the Capital</strong></span>
    <a href="//www.erepublik.com/en/main/region/{permalink}" class="fakeheight">Region name</a>
  </td>
  <td><a href="//www.erepublik.com/en/main/region/{permalink}" class="std_global_btn smallSize blueColor"><span>Details</span></a></td>
</tr>
```

| Element | Selector | Meaning |
|---------|----------|---------|
| Region name and link | `a.fakeheight` | [Region page](../world/map.md#region-page) |
| Capital | `span.icon-capital` | Only on the capital (earlier notes) |
| Determination | `span.influence_multiplier` | Present on occupied foreign territory: the bonus for those who fight to free it |
| No route | `strong.route_warning` | Region disconnected from the capital |

A country with no territory shows `Regions (0)` and a table with only the header row (earlier notes).

**Source:** real request, 2026-11-27; capital marker and empty state — earlier notes.

---

## Online citizens

`GET /en/main/online-users/{country}/all/{page}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `country` | path | string | Yes | Permalink, or `all` for the world |
| `page` | path | number | Yes | Starts at `1` |

The meaning of the middle `all` segment is not known.

```bash
curl 'https://www.erepublik.com/en/main/online-users/Poland/all/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

An HTML page with `table.bestof` and a `ul.pager`:

| Column | Content |
|--------|---------|
| No. | Row number |
| Name | Avatar, link to the [profile](../social/profiles.md), link `/en/main/messages-compose/{citizenId}` |
| Citizenship | Country flag |
| Location | Region name and its country flag |

**Source:** real request, 2026-11-27.

---

## Open questions

| Topic | Earlier notes | Observed 2026-11-27 | Page follows |
|-------|---------------|---------------------|--------------|
| Dictatorship maps | `const` declarations on all country pages, USA example with Mexico, Venezuela, Switzerland and others | Both `const` and `var` forms seen; a different set of countries | Treated as current state |
| Anonymous access | All tabs readable | Not rechecked | Earlier notes |
