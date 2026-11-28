# Rankings pages

[API docs](../README.md) › Rankings › Rankings pages

The classic rankings screens (countries, citizens, parties, newspapers, military units, fighters, alliances) and the Latest Updates news page. They are **server-rendered HTML**, so a client has to parse the markup. For JSON data see [Leaderboards](leaderboards.md).

| Page | Method | Path | Status |
|------|--------|------|--------|
| [Country power](#countries) | GET | `/en/main/rankings-countries` | ✅ Verified 2026-11-27 |
| [Population](#population) | GET | `/en/main/rankings-population` | ✅ Verified 2026-11-27 |
| [Alliances](#alliances) | GET | `/en/main/rankings-alliances` | ✅ Verified 2026-11-27 |
| [Citizens by experience](#citizens-by-experience) | GET | `/en/main/rankings-citizens-experience/{period}/{country}` | ✅ Verified 2026-11-27 |
| [True patriots](#true-patriots) | GET | `/en/main/rankings-true-patriots-new/{period}/{country}` | ✅ Verified 2026-11-27 |
| [Guerrilla fights](#guerrilla-fights) | GET | `/en/main/rankings-guerrilla/{period}/{country}` | ✅ Verified 2026-11-27 |
| [Top fighters](#top-fighters-and-aviators) | GET | `/en/main/rankings-damage/{day}` | ✅ Verified 2026-11-27 |
| [Top aviators](#top-fighters-and-aviators) | GET | `/en/main/rankings-damage-air/{day}` | ✅ Verified 2026-11-27 |
| [Super soldiers](#super-soldiers) | GET | `/en/main/rankings-super-soldiers/{n}/{country}` | ✅ Verified 2026-11-27 |
| [Parties](#parties) | GET | `/en/main/rankings-parties/{n}/{country}` | ✅ Verified 2026-11-27 |
| [Newspapers](#newspapers) | GET | `/en/main/rankings-newspapers/{n}/{country}` | ✅ Verified 2026-11-27 |
| [Military units](#military-units) | GET | `/en/main/rankings-military/{n}/{country}` | ✅ Verified 2026-11-27 |
| [Latest updates](#latest-updates) | GET | `/en/main/latest-updates[/0/{id}]` | ✅ Verified 2026-11-27 |
| [Latest updates, next page](#latest-updates-next-page) | POST | `/en/main/latest-updates-history` | 🔍 From code |

Headers and cookie: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). The pages were requested with a logged-in session; whether they work without one was not tested.

---

## Common to the ranking tables

```bash
curl 'https://www.erepublik.com/en/main/rankings-parties/1/35' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Path values

| Value | Meaning |
|-------|---------|
| `{country}` | A [country ID](../basics/id-tables.md), `overall` (all countries), or `my` on links of pages that have no country selected |
| `{period}` | `weekly` (labelled "Last 7 days") or `alltime` |
| `{n}` | The UI always links `1`. `2` returned the same parties and military unit tables, so its meaning is unknown |

`overall` was tried on parties, experience and true patriots. A path with its parameters missing returns `404`.

The country selector (`select#countryId`) replaces the last path segment with the chosen ID and reloads the page.

### Markup

The ranking is `table.bestof`. The first row is the header.

| Selector | Meaning |
|----------|---------|
| First cell | Rank; on several tables followed by the change |
| `.rank_diff_value` with `.diff_up`, `.diff_down` or `.diff_equal` | Direction of the rank change |
| `.entity .nameholder a` | Name and link of the citizen, party, newspaper or unit |
| `a[hovercard]` | Citizen links; the attribute holds the citizen ID |
| `a[href^="/en/country/society/"]` | Country |
| `span.special` | The ranked value, formatted with thousands separators |
| `#myPositionHighlight` | The row of your own entry, on some tables |
| `.empty_space` | Class used in the tables, apparently for the gap before a second block of rows |

Long tables are cut: the first ranks, then a block further down (for example ranks 1–5 and 33–37 on experience, 1–10 and 212–216 on parties overall).

**Source:** real requests, 2026-11-27.

---

## Countries

`GET /en/main/rankings-countries` · Auth: not tested without a session · ✅ Verified 2026-11-27

All countries by **Country Power** (74 rows). Columns: No., Country, Country Power. Empires and dictatorships carry an extra mark (`.empire_flag_deco`, `.dictatorship_flag_icon_l`). The page note says that a state reaching 4,000 Country Power is automatically declared an Empire.

Country pages link here as `/en/main/rankings-countries/1/1` (see [earlier notes](../../country/society.md)); on 2026-11-27 it showed the same table.

**Source:** real requests, 2026-11-27.

---

## Population

`GET /en/main/rankings-population` · Auth: not tested without a session · ✅ Verified 2026-11-27

All countries by population (74 rows). Columns: No., Name, Population.

**Source:** real requests, 2026-11-27.

---

## Alliances

`GET /en/main/rankings-alliances` · Auth: not tested without a session · ✅ Verified 2026-11-27

Alliances by number of members (34 rows). Columns: No., Name, Members. Each row links to `/en/main/alliances/{id}`.

**Source:** real requests, 2026-11-27.

---

## Citizens by experience

`GET /en/main/rankings-citizens-experience/{period}/{country}` · Auth: not tested without a session · ✅ Verified 2026-11-27

Columns: rank, Name, Country, Experience Points. 11 rows observed.

**Source:** real requests, 2026-11-27.

---

## True patriots

`GET /en/main/rankings-true-patriots-new/{period}/{country}` · Auth: not tested without a session · ✅ Verified 2026-11-27

Columns: rank, Name, Country, Patriot Damage. 11 rows observed.

**Source:** real requests, 2026-11-27.

---

## Guerrilla fights

`GET /en/main/rankings-guerrilla/{period}/{country}` · Auth: not tested without a session · ✅ Verified 2026-11-27

Columns: rank, Name, Military Unit, Guerrilla Fight Wins. 50 rows. Both periods were requested for one country; the UI link goes to `alltime`.

**Source:** real requests, 2026-11-27.

---

## Top fighters and aviators

`GET /en/main/rankings-damage/{day}` · `GET /en/main/rankings-damage-air/{day}` · Auth: not tested without a session · ✅ Verified 2026-11-27

The 100 best by total damage on one day, ground and air. Columns: rank, Name, Country, Total Damage.

| `{day}` | Meaning |
|---------|---------|
| `1` | Yesterday |
| `2`–`5` | 2 to 5 days ago |

For `rankings-damage`, `0` and `6` returned `404`. The page says the list is updated daily around 01:30 game time. There is no country filter.

**Source:** real requests, 2026-11-27.

---

## Super soldiers

`GET /en/main/rankings-super-soldiers/{n}/{country}` · Auth: not tested without a session · ✅ Verified 2026-11-27

Citizens by strength. Columns: rank, Name, Country, Strength. 11 rows observed.

**Source:** real requests, 2026-11-27.

---

## Parties

`GET /en/main/rankings-parties/{n}/{country}` · Auth: not tested without a session · ✅ Verified 2026-11-27

Political parties by members. Columns: rank, Name, Country, Members. Rows link to the party page.

**Source:** real requests, 2026-11-27.

---

## Newspapers

`GET /en/main/rankings-newspapers/{n}/{country}` · Auth: not tested without a session · ✅ Verified 2026-11-27

Newspapers by subscribers. Columns: rank, Name, Country, Subscribers. Rows link to `/en/newspaper/…`.

**Source:** real requests, 2026-11-27.

---

## Military units

`GET /en/main/rankings-military/{n}/{country}` · Auth: not tested without a session · ✅ Verified 2026-11-27

Military units by opponents defeated. Columns: rank, Name, Country, Total Opponents Defeated. Rows link to `/en/main/group-show/{id}`. 5 rows observed for one country.

**Source:** real requests, 2026-11-27.

---

## Latest updates

`GET /en/main/latest-updates` · `GET /en/main/latest-updates/0/{id}` · ✅ Verified 2026-11-27

The game's announcements, linked from the page footer (as `/en/main/ga_track/latest-updates`). The list shows 10 posts; the second path opens with one post in front. The meaning of the `0` segment is unknown.

```bash
curl 'https://www.erepublik.com/en/main/latest-updates/0/333' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Selector | Meaning |
|----------|---------|
| `.updates_container` | The posts |
| `.update` | One post; `.update.firstMode` is the one opened by ID |
| `.updateTitle a[title]` | Title; on the list its `href` is the post's address |
| `.updateContent` | HTML body |
| `.day_number strong` | Game day; the `title` attribute holds the date |
| `.pager a[trigger="paginator"]` | [Paging](#latest-updates-next-page); `rel="page_{n}"` |

The newest post on 2026-11-27 was `333`, dated December 2023.

**Source:** real requests, 2026-11-27.

---

## Latest updates, next page

`POST /en/main/latest-updates-history` · Auth required · 🔍 From code

Loads another page of the list. It only reads.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `page` | number | Yes | Zero-based: the `rel="page_{n}"` number minus 1 |

```bash
curl 'https://www.erepublik.com/en/main/latest-updates-history' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'page=1&_token=YOUR_CSRF_TOKEN'
```

The client expects JSON:

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | The response is ignored when truthy |
| `page` | number | Page number, used as a cache key |
| `html` | string | Replaces the posts |
| `paginator` | string | Replaces the pager |

**Source:** `https://www.erepublik.net/js/homepage/latest_updates.1698060179.js`, `retrieve_all`.

---

## Open questions

| Topic | Observed | Page follows |
|-------|----------|--------------|
| `{n}` segment of super soldiers, parties, newspapers and military units | `1` and `2` give identical tables where compared | Not settled |
| Which rows a cut table shows | Looks like the top plus a block around your own position, but own rows were not marked on every table | Described as observed |
| `0` segment of latest updates | Always `0` in links | Not settled |

Rankings of seasonal events (`countries-tournament-*`, `epic-soldier-event-*`) belong to the Events section; they are described in [Tournaments](../events/tournaments.md).
