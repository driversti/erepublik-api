# Search Citizens

#erepublik #api #social #search

Endpoint for searching citizens by name prefix, used for autocomplete and citizen lookup.

[< Back to Social API](README.md)

**Related:** [Citizen Profiles](profiles.md) | [Friends](friends.md)

---

## Search Citizens

**Method:** GET
**URL:** `/en/main/citizen-search`
**Auth Required:** Yes

### Description

Searches for citizens by name prefix. Returns up to ~10 matching results ordered by relevance. Used for autocomplete in the game's citizen search functionality (e.g., when composing messages or adding friends).

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| name | string | Yes | Search query (citizen name prefix, minimum ~2 characters) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/citizen-search?name=plato' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

</details>

<details>
<summary>Example Response</summary>

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
      "avatar": "https://cdnt.erepublik.net/.../c81e728d9d4c2f636f067f89cc14862c.jpg"
    },
    {
      "id": 9435044,
      "name": "Plato Aparato",
      "level": 1,
      "birthday": "Day 4,008",
      "xp": 0,
      "countryId": 64,
      "avatar": "https://cdnt.erepublik.net/.../default_male.gif"
    }
  ]
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `query` | string | The search query that was submitted |
| `citizens` | array | Array of matching citizen results |
| `citizens[].id` | number | Citizen ID |
| `citizens[].name` | string | Citizen display name |
| `citizens[].level` | number | Experience level |
| `citizens[].birthday` | string | In-game birthday (e.g., "Day 169") |
| `citizens[].xp` | number | Experience points |
| `citizens[].countryId` | number | Citizenship country ID |
| `citizens[].avatar` | string | Avatar image URL |

### Notes

- **Prefix matching**: Searches by name prefix, not substring -- "plato" matches "Plato" and "Platoctm" but not "xPlato"
- **Result limit**: Returns approximately 10 results maximum
- **Error handling**: Returns `{"error": {"message": "Invalid name parameter"}}` if the `name` parameter is missing
- **Inactive citizens**: Results include inactive/dead citizens (check `level: 1` and `xp: 0` as indicators of never-played accounts)
- **Use cases**: Citizen lookup, autocomplete for message composition, friend search, building citizen directories
