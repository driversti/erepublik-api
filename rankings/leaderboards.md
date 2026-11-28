# Leaderboards

[API docs](../README.md) › Rankings › Leaderboards

The weekly leaderboards screen (`/en/main/leaderboards`): citizens, military units and countries ranked by damage, defeated opponents, domination points or deployed energy. The page is an HTML shell; its Angular controller loads every table from one of the JSON endpoints below. For the older server-rendered tables see [Rankings pages](rankings.md).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Leaderboards page](#leaderboards-page) | GET | `/en/main/leaderboards` | ✅ Verified 2026-11-27 |
| [Citizen damage](#citizen-leaderboards) | GET | `/en/main/leaderboards-damage[-aircraft]-rankings/{params}` | ✅ Verified 2026-11-27 |
| [Citizen opponents defeated](#citizen-leaderboards) | GET | `/en/main/leaderboards-kills[-aircraft]-rankings/{params}` | ✅ Verified 2026-11-27 |
| [Military unit boards](#military-unit-leaderboards) | GET | `/en/main/leaderboards-mu{damage,kills}[aircraft]-rankings/{params}` | ✅ Verified 2026-11-27 |
| [Country boards](#country-leaderboards) | GET | `/en/main/leaderboards-co{damage,kills}[aircraft]-rankings/{params}`, `leaderboards-copoints-rankings` | ✅ Verified 2026-11-27 |
| [Points to next target](#points-to-next-target) | GET | `/en/main/leaderboards-copointsleft-rankings/{params}` | 🔍 From code |
| [Deployed energy](#deployed-energy-leaderboards) | GET | `/en/main/leaderboards-{,mu-,country-}energy-rankings/{params}` | ✅ Verified 2026-11-27 |
| [Event energy](#event-energy) | GET | `/en/main/leaderboards-event-energy-rankings/{params}` | 🔍 From code |
| [League](#league-leaderboard) | GET | `/en/main/leaderboards-league-rankings/{section}` | ✅ Verified 2026-11-27 |

Headers and cookie: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). All requests on this page are GET and read-only.

---

## Leaderboards page

`GET /en/main/leaderboards` · Auth required · ✅ Verified 2026-11-27

An HTML page with the country, military unit, division and week selectors, the countdown to the weekly reset and the sidebar. The tables are filled by `LeaderboardsController`.

The client builds the sidebar and picks the first board from flags in `erepublik.info`:

| Flag | Effect | Default board when set |
|------|--------|------------------------|
| `eventEnergySpentLeaderboards` | Adds an event energy section at the top | [Event energy](#event-energy) |
| `leagueOfAlliesLeaderboards` | Adds league sections at the top | The active [league](#league-leaderboard) section |
| `resourceWars` | Moves countries to the top and adds [points to next target](#points-to-next-target) | Points to next target |
| none (observed) | — | Citizen damage |

The flags are checked for the default board in the order of the table.

**Source:** real request, 2026-11-27; `LeaderboardsController` and `LeaderboardsFactory` in `erpk_angular_controllers.js` (version 1795779904).

---

## Path parameters

All boards except the [league](#league-leaderboard) one share the same path:

```text
/en/main/leaderboards-{board}-rankings/{countryId}/{week}/{muId}/{division}
```

| Name | Type | Description |
|------|------|-------------|
| `countryId` | number | Country filter; `0` where the board has no country filter |
| `week` | number | `0` this week, `1` last week, up to `3`. Event energy uses it as "n-th previous leaderboard", `0`–`6` |
| `muId` | number | Military unit filter; `0` for the whole country or when the board has no unit filter |
| `division` | number | `1`–`4`; `0` all divisions or when the board has no division filter |

Which filters the UI offers (an unused one is sent as `0`):

| Board | Country | Unit | Division | Week |
|-------|:-------:|:----:|:--------:|:----:|
| Citizen damage and kills, tanks | ✓ | ✓ (unit tab) | ✓ | ✓ |
| Citizen damage and kills, aircraft | ✓ | ✓ (unit tab) | — | ✓ |
| Citizen energy | — | — | — | previous boards |
| Military unit boards, including energy | ✓ | — | — | ✓ |
| Country boards, including energy | — | — | — | ✓ |

The "Military Unit" and "National" sidebar items call the same endpoint; they differ only in `muId`.

Observed on 2026-11-27:

- With `countryId` = `0`, the damage, kills and unit damage boards return `404`. Country and citizen energy boards accept `0/0/0/0`.
- The parameters can be left out from the right: `.../leaderboards-damage-rankings/35` works. Without any parameter the citizen damage board returns 100 rows from several countries; `leaderboards-mudamage-rankings` without parameters returns `500`.
- Aircraft rows carry `myDivision` `11`. Passing `11` as `division` to the tank board returns an empty `top`.
- A country or unit with no entries returns an empty `top`, not an error.

---

## Response

All boards except the league one return the same four keys.

| Field | Type | Description |
|-------|------|-------------|
| `top` | array | Ranked entries |
| `myPosition` | object | Your own entry (or your unit's, or your country's), same shape as a `top` entry |
| `top3_lastWeek` | array | The three winners of the previous week, same shape |
| `mu_filter` | array | Options of the unit selector: `id`, `name`, `avatar`, `flag`. The first is `0`, "Overall" |

Fields of an entry:

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Citizen, unit or country ID, depending on the board |
| `name` | string | Name |
| `rank` | number | Position |
| `values` | number | The ranked quantity (damage, kills, points or energy) |
| `avatar` | string | Image URL (country flag on country boards) |
| `link` | string | Relative page path |
| `flag` | string | Entry type: `citizen_stats`, `group`, `country` in `top`; `citizen_img`, `mu_img`, `country_img` in `top3_lastWeek` |
| `countryId` | number | Country |
| `muCountryId` | number | Citizen and unit boards: the unit's country |
| `muId` | number | Citizen boards: the citizen's unit |
| `myDivision` | number | Citizen boards: the citizen's division |

The client inserts `myPosition` into the table when its `rank` is between 16 and 9998.

---

## Citizen leaderboards

`GET /en/main/leaderboards-{board}-rankings/{countryId}/{week}/{muId}/{division}` · Auth required · ✅ Verified 2026-11-27

| `{board}` | Ranks by |
|-----------|----------|
| `damage` | Tank (ground) damage |
| `damage-aircraft` | Air damage |
| `kills` | Opponents defeated, ground |
| `kills-aircraft` | Opponents defeated, air |

```bash
curl 'https://www.erepublik.com/en/main/leaderboards-damage-rankings/35/0/0/4' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

Shortened, with placeholder values:

```json
{
  "top": [
    {
      "id": 1000001,
      "name": "PlayerName",
      "rank": 1,
      "values": 123456789,
      "avatar": "https://cdnt.erepublik.net/…",
      "link": "citizen/profile/1000001",
      "flag": "citizen_stats",
      "countryId": 35,
      "muCountryId": 35,
      "muId": 1,
      "myDivision": 4
    }
  ],
  "myPosition": { "id": 1000002, "name": "MyName", "rank": 42, "values": 1000, "flag": "citizen_stats" },
  "top3_lastWeek": [ { "id": 1000003, "rank": 1, "values": 987654321, "flag": "citizen_img" } ],
  "mu_filter": [ { "id": 0, "name": "Overall", "avatar": "//www.erepublik.net/images/…", "flag": "mu_picture" } ]
}
```

(`myPosition`, `top3_lastWeek` and `mu_filter` entries are cut down.)

The number of rows varies: 70–100 in most observed responses, 23 for one division 1 request.

**Source:** real requests, 2026-11-27; `erpk_angular_controllers.js` (version 1795779904).

---

## Military unit leaderboards

`GET /en/main/leaderboards-{board}-rankings/{countryId}/{week}/0/0` · Auth required · ✅ Verified 2026-11-27

| `{board}` | Ranks by |
|-----------|----------|
| `mudamage` | Tank damage |
| `mudamageaircraft` | Air damage |
| `mukills` | Opponents defeated, ground |
| `mukillsaircraft` | Opponents defeated, air |
| `mu-energy` | Deployed energy, see [below](#deployed-energy-leaderboards) |

```bash
curl 'https://www.erepublik.com/en/main/leaderboards-mudamage-rankings/35/0/0/0' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

A `top` entry, with placeholder values:

```json
{
  "id": 1,
  "name": "Unit Name",
  "rank": 1,
  "values": 123456789,
  "avatar": "//cdnt.erepublik.net/…",
  "link": "main/group-show/1",
  "flag": "group",
  "countryId": 35,
  "muCountryId": 35
}
```

Observed responses had 14–17 rows. `myPosition` was your unit.

**Source:** real requests, 2026-11-27.

---

## Country leaderboards

`GET /en/main/leaderboards-{board}-rankings/0/{week}/0/0` · Auth required · ✅ Verified 2026-11-27

| `{board}` | Ranks by |
|-----------|----------|
| `codamage` | Tank damage |
| `codamageaircraft` | Air damage |
| `cokills` | Opponents defeated, ground |
| `cokillsaircraft` | Opponents defeated, air |
| `copoints` | Domination points |
| `country-energy` | Deployed energy, see [below](#deployed-energy-leaderboards) |

```bash
curl 'https://www.erepublik.com/en/main/leaderboards-codamage-rankings/0/0/0/0' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

A `top` entry, with placeholder values:

```json
{
  "id": 35,
  "countryId": 35,
  "name": "Poland",
  "rank": 1,
  "values": 123456789,
  "avatar": "//www.erepublik.net/images/flags_png/M/Poland.png",
  "link": "country/society/Poland",
  "flag": "country"
}
```

All observed boards had 74 rows, and `mu_filter` held only "Overall". `myPosition` was your citizenship country.

**Source:** real requests, 2026-11-27.

---

## Points to next target

`GET /en/main/leaderboards-copointsleft-rankings/0/0/0/0` · Auth required · 🔍 From code

The client offers this board only when `erepublik.info.resourceWars` is set, sends all parameters as `0` and reloads it every 60 seconds. On 2026-11-27, without that flag, the request returned `200` with an **empty body**, so the response shape is unknown.

**Source:** `erpk_angular_controllers.js` (version 1795779904); real request, 2026-11-27.

---

## Deployed energy leaderboards

`GET /en/main/leaderboards-{board}-rankings/{countryId}/{week}/0/0` · Auth required · ✅ Verified 2026-11-27

| `{board}` | Entries | `countryId` |
|-----------|---------|-------------|
| `energy` | Citizens, all countries | `0` |
| `mu-energy` | Military units of a country | Country |
| `country-energy` | Countries | `0` |

```bash
curl 'https://www.erepublik.com/en/main/leaderboards-energy-rankings/0/0/0/0' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

Entries have the [common shape](#response) of their type. The citizen board returned 260 rows.

**Source:** real requests, 2026-11-27.

---

## Event energy

`GET /en/main/leaderboards-event-energy-rankings/0/{previous}/0/0` · Auth required · 🔍 From code

Offered only while `erepublik.info.eventEnergySpentLeaderboards` is set. The selector sends `previous` as `0` (current) to `6` (6th previous). The info button on this board opens the `winterOlympicsPopup` popup.

On 2026-11-27, with no such event running, the request returned `404`.

**Source:** `erpk_angular_controllers.js` (version 1795779904); real request, 2026-11-27.

---

## League leaderboard

`GET /en/main/leaderboards-league-rankings/{section}` · Auth required · ✅ Verified 2026-11-27

A League of Allies board. The sidebar shows it only while `erepublik.info.leagueOfAlliesLeaderboards` is set (five sections, each with a `sectionName`). The endpoint still answered on 2026-11-27, when the sidebar did not offer it.

The client sends the sidebar item's internal ID minus 16 as `section`: `4`–`8` for the five items in order, or `5`–`9` when `resourceWars` adds an item before them. A section's `sectionId` only picks the default item (the one with ID `sectionId + 16`).

```bash
curl 'https://www.erepublik.com/en/main/leaderboards-league-rankings/4' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

| Field | Type | Description |
|-------|------|-------------|
| `top` | array | 100 rows: `id`, `name`, `rank`, `values`, `avatar`, `link` |
| `myPosition` | object | Your entry, with an extra `league` number |
| `myTop` | — | Not examined |
| `mu_filter` | array | Only one option |

Sections `1`, `4`, `5`, `8` and `9` all returned `200` with the same `league`, row count and own rank; whether the parameter matters while the event runs is unknown. `0` returned `404`. There is no `top3_lastWeek`.

**Source:** real requests, 2026-11-27; `erpk_angular_controllers.js` (version 1795779904).

---

## Open questions

| Topic | Observed or code | Page follows |
|-------|------------------|--------------|
| `leaderboards-copointsleft-rankings` | Empty body outside Resource Wars | Not settled |
| League `section` | Code sends the sidebar item ID minus 16; live values made no visible difference | Not settled |
| Row limits | Differ between boards and filters | Counts given as observed |
| Order of `top` on citizen energy | One neighbouring pair of 260 rows was out of order by `values` | Not settled |
