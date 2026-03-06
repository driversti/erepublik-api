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
