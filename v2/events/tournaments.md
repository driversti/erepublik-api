# Tournaments

[API docs](../README.md) › Events › Tournaments

Rankings of two seasonal events: the **Countries Tournament** (damage against a target, per citizen and per country) and the **Epic Soldier** event (points per citizen, average points per country). The calls come from the `countriesTournament` and `epicSoldier` objects of the main bundle.

> [!NOTE]
> All five endpoints still answered with data on 2026-11-27, although no event screen that calls them was found. Which edition or dates the data belongs to is unknown.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Tournament: citizens of a country](#tournament-citizens-of-a-country) | GET | `/en/main/countries-tournament-national-rankings/{countryId}` | ✅ Verified 2026-11-27 |
| [Tournament: countries](#tournament-countries) | GET | `/en/main/countries-tournament-world-rankings` | ✅ Verified 2026-11-27 |
| [Tournament: finalists](#tournament-finalists) | GET | `/en/main/countries-tournament-world-rankings/finalists` | ✅ Verified 2026-11-27 |
| [Epic Soldier: citizens](#epic-soldier-citizens) | GET | `/en/main/epic-soldier-event-citizens-rankings/{countryId}/{divisionId}` | ✅ Verified 2026-11-27 |
| [Epic Soldier: countries](#epic-soldier-countries) | GET | `/en/main/epic-soldier-event-countries-rankings` | ✅ Verified 2026-11-27 |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Common behaviour

✅ Verified 2026-11-27

- **A session is required.** Without the cookie, each endpoint answered HTTP 200 with a full HTML page instead of data.
- The body is JSON, but the `Content-Type` is `text/html`.
- `X-Requested-With` was not required (checked on the citizens ranking of the tournament).
- Each response has `top` (the ranking) and `myPosition` (the logged-in citizen, or their country).

How the client uses them:

- It builds protocol-relative URLs (`//{hostname}/{culture}/main/…`) and calls `$.getJSON`.
- Each response is cached in the page (`nationalData`, `worldData`, `finalistData`); only the national call has a force-reload argument.
- With no country selected it sends `erepublik.citizen.ctCountryId`.
- It inserts `myPosition` into `top` at index 5 when `myPosition.rank` is above 5 (finalists excluded; in the national view only when the shown country is the citizen's own).
- It shows the avatars of the first three entries.

---

## Tournament: citizens of a country

`GET /en/main/countries-tournament-national-rankings/{countryId}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | [Country ID](../basics/id-tables.md) |

```bash
curl 'https://www.erepublik.com/en/main/countries-tournament-national-rankings/35' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Response

Shortened to one entry; player data replaced with placeholders. Country 35 returned 100 entries.

```json
{
  "top": [
    {
      "id": "1000000",
      "name": "PlayerName",
      "avatar": "https://cdnt.erepublik.net/…/55x55/smart/avatars/…",
      "damage": "1000000000",
      "target": 50000000,
      "percent": "2,000.00%",
      "rank": "1",
      "countryId": "35",
      "link": "//www.erepublik.com/en/citizen/profile/1000000",
      "class": "citizen"
    }
  ],
  "myPosition": { "…": "same fields, for the logged-in citizen" }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `top[]` | array | Ranking entries |
| `top[].id` | string | Citizen ID |
| `top[].damage` | string | Damage counted for the tournament |
| `top[].target` | number | Damage target |
| `top[].percent` | string | `damage / target`, formatted with thousands separators and `%` |
| `top[].rank` | string | Position |
| `top[].countryId` | string | Country |
| `top[].link` | string | Protocol-relative profile URL |
| `top[].class` | string | `citizen` |
| `myPosition` | object | Same fields as a `top` entry |

The client replaces `country_filter` in the data with its own hardcoded country list (`id`, `name`, `permalink`) for the country dropdown. The response observed had no such key.

### Errors

- An unknown country (`999`) or no `countryId` at all: HTTP 200 with an empty `top`.

**Source:** real requests, 2026-11-27; `countriesTournament.getNationalData` in `erepublik.js` (version 1795779896).

---

## Tournament: countries

`GET /en/main/countries-tournament-world-rankings` · Auth required · ✅ Verified 2026-11-27

No parameters.

```bash
curl 'https://www.erepublik.com/en/main/countries-tournament-world-rankings' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Response

Shortened to one of 74 entries:

```json
{
  "top": [
    {
      "id": "28",
      "name": "Venezuela",
      "avatar": "//www.erepublik.net/images/flags_png/M/Venezuela.png",
      "damage": "43216078720",
      "target": 1432510475,
      "percent": "3,016.80%",
      "rank": "1",
      "link": "//www.erepublik.com/en/country/society/Venezuela",
      "class": "country"
    }
  ],
  "myPosition": { "…": "same fields, for a country" }
}
```

Fields are those of the [citizens ranking](#tournament-citizens-of-a-country), without `countryId`; `class` is `country`. The client shows `damage` only for countries.

**Source:** real request, 2026-11-27; `countriesTournament.getWorldData`.

---

## Tournament: finalists

`GET /en/main/countries-tournament-world-rankings/finalists` · Auth required · ✅ Verified 2026-11-27

No parameters. Called from the **Finalists** tab (`#switch_finalists`).

```bash
curl 'https://www.erepublik.com/en/main/countries-tournament-world-rankings/finalists' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Response

Same structure as the [countries ranking](#tournament-countries). It held 9 countries. Their `rank` values were 26, 28, 33, 50, 52, 54, 55, 59 and 60, so ranks are not renumbered within the finalists. The client hides the position column for this list.

Earlier notes say the endpoint is only available during the finalist phase; it answered on 2026-11-27.

**Source:** real request, 2026-11-27; `countriesTournament.getFinalistsData`.

---

## Epic Soldier: citizens

`GET /en/main/epic-soldier-event-citizens-rankings/{countryId}/{divisionId}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | [Country ID](../basics/id-tables.md) |
| `divisionId` | path | number | No | See below. The client opens the tab with `0` |

Observed for country 35:

| `divisionId` | Result |
|--------------|--------|
| omitted | 100 entries, same size as `0` |
| `0` | 100 entries; the client's default |
| `1`–`4` | 100 entries each, different lists |
| `11`, `99` | HTTP 500 |

```bash
curl 'https://www.erepublik.com/en/main/epic-soldier-event-citizens-rankings/35/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Response

Shortened to one entry; player data replaced with placeholders.

```json
{
  "top": [
    {
      "id": "1000000",
      "name": "PlayerName",
      "avatar": "https://cdnt.erepublik.net/…/55x55/smart/avatars/…",
      "average": "10000",
      "rank": 1,
      "countryId": 35,
      "link": "citizen/profile/1000000",
      "class": "citizen"
    }
  ],
  "myPosition": { "…": "same fields; rank was a string here" }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `top[].average` | string | The value ranked; the client labels it **Points** for citizens |
| `top[].rank` | number | Position (a string in `myPosition`) |
| `top[].countryId` | number | Country (a string in `myPosition`) |
| `top[].link` | string | Relative path; the client prefixes host and language |
| `myPosition` | object | The logged-in citizen |

There is no `damage` or `percent`, and no `country_filter`: the client adds its own country list as in the tournament.

**Source:** real requests, 2026-11-27; `epicSoldier.getNationalData` in `erepublik.js` (version 1795779896).

---

## Epic Soldier: countries

`GET /en/main/epic-soldier-event-countries-rankings` · Auth required · ✅ Verified 2026-11-27

No parameters.

```bash
curl 'https://www.erepublik.com/en/main/epic-soldier-event-countries-rankings' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Response

Shortened to one of 74 entries:

```json
{
  "top": [
    {
      "id": "59",
      "name": "Thailand",
      "avatar": "//www.erepublik.net/images/flags_png/XL/Thailand.png",
      "link": "country/society/Thailand",
      "rank": 1,
      "average": "38,856.77",
      "class": "country"
    }
  ],
  "myPosition": { "…": "same fields, for a country; rank was a string" }
}
```

`average` is the country's **Average Points**, formatted with a thousands separator.

**Source:** real request, 2026-11-27; `epicSoldier.getWorldData`.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Authentication | Not required | Anonymous requests get an HTML page | Live requests |
| `country_filter` | Returned by the server, then overridden by the client | No such key in the response; the client adds it | Live response |
| Entry fields | `id` and `damage` numbers, `percent` without `%` | Strings; `percent` ends with `%` | Live response |
| Epic Soldier entries | `damage` and `percent` | `average` | Live response |
| `divisionId` | Division or category ID | `1`–`4` work, `11` fails; the meaning of `0` is not established | Live requests |
| Outside the event | May return errors | Data returned on 2026-11-27 | Live requests |
