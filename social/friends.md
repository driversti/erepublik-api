# Add/Remove Friend

#erepublik #api #social #friends

Endpoint for managing friendship connections between citizens.

[< Back to Social API](README.md)

**Related:** [Citizen Profiles](profiles.md) | [Search Citizens](search.md)

---

## Add/Remove Friend

**Method:** POST
**URL:** `/en/main/citizen-addRemoveFriend/`
**Auth Required:** Yes

### Description

Adds or removes a friendship connection with another citizen. This endpoint handles both sending friend requests and removing existing friendships.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| citizenId | number | Yes | The target citizen ID to add/remove as friend |
| action | string | Yes | Either `addFriend` or `removeFriend` |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/citizen-addRemoveFriend/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw '_token=YOUR_CSRF_TOKEN&citizenId=2886612&action=addFriend'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "status": true,
  "message": "Friend request sent successfully"
}
```

</details>

<details>
<summary>Example Response (Error - Pending Request)</summary>

```json
{
  "message": "ghoghnooos already has a friendship request pending from you.",
  "status": false,
  "error": true
}
```

</details>

### Notes

- Requires authentication via session cookie
- CSRF token (`_token`) must be obtained from the page where the action is triggered
- The `action` parameter determines whether to add or remove friendship
- Possible error scenarios:
  - Pending friend request already exists
  - Citizen is already a friend (when using `addFriend`)
  - Not friends with the citizen (when using `removeFriend`)
- Response includes a user-friendly message describing the result

---

## List Friends (Paginated)

**Method:** GET
**URL:** `/en/main/citizen-friends/{citizenId}/{page}/list`
**Auth Required:** Yes

### Description

Returns a single page of a citizen's friend list. The friends page on a profile lazy-loads its list via this endpoint, fetching 20 friends at a time as the user scrolls. The response is a JSON envelope: a `results` field with the human-readable index range covered by this page, and a `content` field containing a rendered HTML `<table>` of friend rows.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| citizenId | number | Yes | Path segment — the citizen whose friends are being listed |
| page | number | Yes | Path segment — 1-based page index. Each page returns 20 friends (page 3 → entries 41–60) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Referer | `https://www.erepublik.com/en/main/citizen-friends/{citizenId}` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/citizen-friends/1844547/3/list' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'accept: application/json, text/javascript, */*; q=0.01'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "results": "41 - 60",
  "content": "<table width=\"100%\" class=\"lister\">\n\t<tr id=\"friend_1243978\">\n\t\t<td class=\"friend_info\">\n\t\t\t<a hovercard=1 href=\"/en/citizen/profile/1243978\" title=\"Justino Figueiredo\">\n\t\t\t\t<img src=\"https://cdnt.erepublik.net/.../55x55/smart/avatars/Citizens/...jpg\" alt=\"Justino Figueiredo\" width=\"55\" height=\"55\" />\n\t\t\t\t<strong>Justino Figueiredo</strong>\n\t\t\t</a>\n\t\t</td>\n\t</tr>\n\t<tr id=\"friend_6443107\" class=\"dead\">\n\t\t<td class=\"friend_info\">\n\t\t\t<a hovercard=1 href=\"/en/citizen/profile/6443107\" title=\"twall\">\n\t\t\t\t<img src=\"...\" alt=\"twall\" width=\"55\" height=\"55\" />\n\t\t\t\t<strong>twall</strong>\n\t\t\t</a>\n\t\t</td>\n\t</tr>\n\t...\n</table>"
}
```

</details>

### Parsing the `content` HTML

Each friend is a `<tr id="friend_{citizenId}">` row. From each row you can extract:

| Field | Source |
|-------|--------|
| Citizen ID | The `id` attribute (`friend_{id}`) or the profile link `/en/citizen/profile/{id}` |
| Name | The `<strong>` text (and the link `title` attribute) |
| Avatar URL | The `<img src>` |
| Dead/banned flag | The row carries `class="dead"` when the citizen account is dead/banned |

### Notes

- Returns HTTP `200` with a JSON body even though `content` is HTML markup.
- Page size is fixed at 20 friends; the `results` range string (e.g. `"41 - 60"`) reflects the requested page.
- Requesting a page beyond the last friend returns an empty/short table — iterate until the returned table has no `<tr>` rows to enumerate all friends.
- Works for any citizen whose friend list is public, not just your own (this example lists citizen `1844547`'s friends).
- Friend rows marked `class="dead"` indicate dead/banned accounts; the profile link still resolves.
