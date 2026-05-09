# Wall Posts

#erepublik #api #social #wall

Endpoints for creating and deleting wall posts on citizen profiles and country pages.

[< Back to Social API](README.md)

**Related:** [Citizen Profiles](profiles.md) | [Friends](friends.md)

---

## Create Wall Post

**Method:** POST
**URL:** `/en/main/wall-post/create/json`
**Auth Required:** Yes

### Description

Creates a new wall post on the citizen's profile or country page. Posts are rate-limited to prevent spam (typically a few minutes between posts).

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| post_as | number | Yes | Posting mode: `0` = post as self, other values likely for organizations |
| post_message | string | Yes | The wall post message content |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/wall-post/create/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'post_as=0&post_message=Hello%20world&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "wallData": {
    "wallType": "friends",
    "params": {
      "wallType": "friends",
      "viewPost": "viewPost",
      "wallPosts": "wall-post",
      "wallComments": "wall-comment"
    },
    "isGroupWall": false,
    "showDictator": true,
    "showPoliticalTitles": false,
    "canPost": true
  },
  "citizenData": {
    "4690052": {
      "id": 4690052,
      "name": "driver sti",
      "avatar": "https://cdnt.erepublik.net/.../avatar.png",
      "profileUrl": "//www.erepublik.com/en/citizen/profile/4690052",
      "isDictator": 0,
      "politicalTitle": ""
    }
  },
  "wallPosts": [
    {
      "id": 26680941,
      "authorId": 4690052,
      "title": false,
      "message": "Here is a more sophisticated post. <q class=\"emoji emoji_1f601\" alt=\"&#x1f601;\" draggable=\"false\">&#x1f601;</q><br />\n\u0406 \u0432\u0456\u043d \u043c\u0456\u0441\u0442\u0438\u0442\u044c \u043a\u0438\u0440\u0438\u043b\u0438\u0446\u044e",
      "createdAt": 1769353893,
      "createdAtTimeAgo": "now",
      "canRemove": true,
      "canReport": false,
      "showCommentsAndVotes": true,
      "showLinkToPost": true,
      "canVote": true,
      "canReact": true,
      "isVoted": false,
      "numVotes": 0,
      "reactions": {
        "1f44d": 0,
        "1f60d": 0,
        "1f606": 0,
        "1f632": 0,
        "1f614": 0,
        "1f621": 0,
        "1f44e": 0
      },
      "reactionId": false,
      "votedBy": [],
      "canComment": true,
      "numComments": 0,
      "commentedBy": [],
      "meta": {
        "reportURL": "//www.erepublik.com/en/main/tickets-report/26680941/wall-post",
        "isAutopost": false,
        "isOwnPost": true,
        "isLatestEvent": false,
        "seeMoreURL": null,
        "isInternationalEvent": false,
        "isNewCitizensPost": false,
        "isAutomaticMedalPost": false,
        "isMuCommanderPost": false,
        "isCountryLeaderPost": false,
        "cssClasses": []
      }
    }
  ],
  "currentPage": 1,
  "pages": 100,
  "hasMore": true
}
```

</details>

<details>
<summary>Example Response (Error - Rate Limited)</summary>

```json
{
  "error": true,
  "message": "You must wait 4 minutes before posting again"
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| wallData | object | Metadata about the wall (type, permissions, display settings) |
| citizenData | object | Author citizen data keyed by citizen ID |
| wallPosts | array | Array containing the newly created post object |
| currentPage | number | Current page number for pagination |
| pages | number | Total number of pages |
| hasMore | boolean | Whether more posts exist |

### Wall Post Object Fields

| Field | Type | Description |
|-------|------|-------------|
| id | number | Unique post ID |
| authorId | number | Citizen ID of the post author |
| message | string | Post content (HTML encoded, may contain emoji tags) |
| createdAt | number | Unix timestamp of creation |
| createdAtTimeAgo | string | Human-readable time (e.g., "now", "5 minutes ago") |
| canRemove | boolean | Whether viewer can delete this post |
| canVote | boolean | Whether viewer can vote on this post |
| canReact | boolean | Whether viewer can add reactions |
| reactions | object | Reaction counts by emoji unicode (e.g., "1f44d" = thumbs up) |
| numVotes | number | Total vote count |
| numComments | number | Total comment count |
| meta | object | Additional post metadata (report URL, post type flags) |

### Notes

- Requires authentication via session cookie
- CSRF token (`_token`) must be obtained from the page where the post is created
- **Rate limiting:** Enforced server-side to prevent spam (typically 3-5 minutes between posts)
- The `post_as` parameter likely controls posting as citizen (0) vs organization/other entity
- Returns HTTP 200 even when rate-limited (check `error` field in JSON response)
- Wall posts appear on the citizen's profile page or country wall, depending on context
- **Unicode/Emoji support:** Full unicode support including emojis (converted to HTML tags) and Cyrillic/other scripts
- Response includes full wall context with pagination metadata
- The newly created post appears as the first item in the `wallPosts` array

---

## Delete Wall Post

**Method:** POST
**URL:** `/en/main/wall-post/delete/json`
**Auth Required:** Yes

### Description

Deletes a wall post by its ID. Only the post author can delete their own posts.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| postId | number | Yes | The ID of the wall post to delete |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/wall-post/delete/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'postId=12345678&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "status": true,
  "message": "Post deleted successfully"
}
```

</details>

<details>
<summary>Example Response (Error - Post Not Found)</summary>

```json
{
  "error": true,
  "message": "This post is not available anymore"
}
```

</details>

<details>
<summary>Example Response (Error - Not Authorized)</summary>

```json
{
  "error": true,
  "message": "You are not authorized to delete this post"
}
```

</details>

### Notes

- Requires authentication via session cookie
- CSRF token (`_token`) must be obtained from the page context
- Only the post author can delete their own posts
- Returns HTTP 200 even on error (check `error` field in JSON response)
- Possible error scenarios:
  - Post ID doesn't exist or was already deleted
  - User doesn't have permission to delete the post (not the author)
  - Invalid or expired CSRF token

---

## Auto-Post Award to Wall

**Method:** POST
**URL:** `/en/main/wall-post/automatic`
**Auth Required:** Yes
**Source:** `awards.{ts}.js`

### Description

Automatically posts an award/medal announcement to the citizen's wall. This is triggered from the award popup on the homepage when the citizen checks the "Share on wall" checkbox before clicking "Get Reward".

Unlike the regular `wall-post/create/json` endpoint, this one uses a **dedicated CSRF token** from `#award_token` (not the standard `#_token`), and accepts the message content directly from a pre-filled checkbox value.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from `$j('#award_token').val()` — **not** the standard `#_token` |
| message | string | Yes | Wall post message text (pre-populated by the server in the checkbox value) |
| awardId | string | Yes | Award identifier (extracted from checkbox element ID) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/wall-post/automatic' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-urlencode '_token=YOUR_AWARD_CSRF_TOKEN' \
  --data-urlencode 'message=I earned a Hard Worker medal!' \
  --data-urlencode 'awardId=12345'
```

</details>

<details>
<summary>Example Response (Success — Expected)</summary>

```json
{
  "message": true
}
```

</details>

<details>
<summary>Example Response (Error — Expected)</summary>

```json
{
  "message": false,
  "error_message": "Something went wrong"
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| message | boolean | `true` if the wall post was created successfully |
| error_message | string | Human-readable error text (only present on failure) |

### How the Frontend Calls This

```javascript
// In awards.js — only fires if a "Share on wall" checkbox is checked
var checkbox = $j(this).siblings('p').children('input:checked');
if (checkbox.length) {
    var checkbox_id_bits = checkbox.attr('id').split('_');
    $j.post('/' + erepublik.settings.culture + '/main/wall-post/automatic', {
        _token: $j('#award_token').val(),
        message: checkbox.val(),
        awardId: checkbox_id_bits[3]  // from id="award_checkbox_{index}_{awardId}"
    }, function(response) { ... }, 'json');
}
```

### Notes

- **Optional action** — only fires when the user explicitly checks the "Share on wall" checkbox in the award popup
- Uses a separate CSRF token (`#award_token`) from the standard page token (`#_token`)
- The `awardId` is parsed from the checkbox DOM element's `id` attribute, split by `_`, taking the 4th segment (index 3)
- The `message` comes pre-filled from the server as the checkbox's `value` attribute
- On error, the message is displayed via `erepublik.functions.displayHumanMsgError()`
- On success, the response has `message: true` (boolean) — note this is different from the regular wall post endpoint which returns full wall data
- The award popup system blocks other popups (`erepublik.settings.achievementPopup = true`) until all awards are dismissed — see [framework/client-objects.md](../framework/client-objects.md)
- Awards are rendered server-side as hidden `.home_reward` divs in the homepage HTML and shown sequentially via `nextAward()`
- Posts created this way have `meta.isAutomaticMedalPost: true` in the wall post object (see Create Wall Post response above)
