# Search

[API docs](../README.md) › Social › Search

The search box in the page header opens an HTML results page. A separate JSON endpoint searches citizens by name.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Search page](#search-page) | GET | `/en/main/search/` | ✅ Verified 2026-11-27 |
| [Citizen search](#citizen-search) | GET | `/en/main/citizen-search` | ✅ Verified 2026-11-27 |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Search page

`GET /en/main/search/?q={query}` · Auth not checked · ✅ Verified 2026-11-27

The target of the header form `#search`. Only logged-in requests were tried.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `q` | query | string | No | Name to look for; without it the page shows no results |

```bash
curl 'https://www.erepublik.com/en/main/search/?q=plato' -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Response

An HTML page. Results are rows of `table.bestof`:

| Column | Content |
|--------|---------|
| No. | Position |
| Name | Avatar and profile link `/en/citizen/profile/{id}` |
| Country | Flag linking to the country's society page |
| Experience Points | Number |

Observed on 2026-11-27:

| `q` | Result |
|-----|--------|
| `plato`, `mar` | 5 rows, no pager links found |
| `a`, `pl` | Message "Too many results. Please refine your search query." |
| `zzzzqqqqxxx`, `Taupomasis` | No rows |

**Source:** real requests, 2026-11-27.

---

## Citizen search

`GET /en/main/citizen-search` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `name` | query | string | Yes | Start of the name, at least 2 characters |
| `page` | query | number | No | Page, default `1` |

```bash
curl 'https://www.erepublik.com/en/main/citizen-search?name=plato' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened:

```json
{
  "query": "plato",
  "citizens": [
    {
      "id": 2,
      "name": "Plato",
      "level": 27,
      "birthday": "Day 169",
      "xp": 10365,
      "countryId": 30,
      "avatar": "https://cdnt.erepublik.net/…"
    }
  ],
  "page": 1,
  "resultCount": 6,
  "timestamp": 1795801893
}
```

| Field | Type | Description |
|-------|------|-------------|
| `query` | string | Submitted text |
| `citizens[]` | array | Matches; 30 on a full page |
| `citizens[].id`, `name` | number, string | Citizen |
| `citizens[].level`, `xp` | number | Level and experience |
| `citizens[].birthday` | string | Registration game day |
| `citizens[].countryId` | number | Citizenship country ID |
| `citizens[].avatar` | string | Image URL |
| `page` | number | Requested page |
| `resultCount` | number | All matches (`727` for `pl`) |
| `timestamp` | number | Unix time |

A page past the end returns an empty `citizens` array.

### Errors

All with HTTP `200`.

| Response | Cause |
|----------|-------|
| `{"error":{"message":"Invalid name parameter"}}` | `name` missing |
| `{"error":{"message":"Query string too short"}}` | One character |
| `{"error":"not_authorized"}` | No session |

Earlier notes add: matching is by prefix (`name` matches "NameSuffix", not "PrefixName"); inactive and dead citizens are included (`level: 1`, `xp: 0` suggests an account that never played). Typical uses: autocomplete, finding a citizen to message or befriend.

No call to this endpoint was found in the current web scripts that were searched.

**Source:** real requests, 2026-11-27; matching rules — earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed | Page follows |
|-------|---------------|----------|--------------|
| Result limit | About 10 | 30 per page, with `page` and `resultCount` | Live response |
| Response fields | `query`, `citizens` | Also `page`, `resultCount`, `timestamp` | Live response |
| Search page vs JSON | Not described | `plato`: 5 rows on the page, `resultCount` 6 | Not settled |
| When the page says "Too many results" | Not described | Threshold unknown | — |
