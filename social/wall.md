# Wall and feeds

[API docs](../README.md) › Social › Wall and feeds

The feed block of the home page: tabs for friends, country, military unit, party and community. Every tab uses the same set of requests under its own route name.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Load a feed](#load-a-feed) | GET | `/en/main/{feedRoute}/retrieve/json` | ✅ Verified 2026-11-27 |
| [Older posts](#older-posts) | POST | `/en/main/{feedRoute}/older/json` | 🔍 From code |
| [Create a post](#create-a-post) | POST | `/en/main/{feedRoute}/create/json` | 🔍 From code |
| [React to a post](#react-to-a-post) | POST | `/en/main/{feedRoute}/vote/json` | 🔍 From code |
| [Delete a post](#delete-a-post) | POST | `/en/main/{feedRoute}/delete/json` | 🔍 From code |
| [Load comments](#load-comments) | POST | `/en/main/{commentRoute}/retrieve/json` | 🔍 From code |
| [Create a comment](#create-a-comment) | POST | `/en/main/{commentRoute}/create/json` | 🔍 From code |
| [React to a comment](#react-to-a-comment) | POST | `/en/main/{commentRoute}/vote/json` | 🔍 From code |
| [Delete a comment](#delete-a-comment) | POST | `/en/main/{commentRoute}/delete/json` | 🔍 From code |

Automatic posts (`POST /en/main/wall-post/automatic`) are described under [badges and auto-posts](../home/badges.md#auto-post).

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Feeds and routes

The routes come from `SERVER_DATA.wallData.citizenFeeds` on the home page (fields: [dashboard](../home/dashboard.md)). Seen on 2026-11-27:

| Tab | `feedRoute` | `commentRoute` | `viewPostParameter` | Posting |
|-----|-------------|----------------|---------------------|---------|
| `friends` | `wall-post` | `wall-comment` | `viewPost` | Yes |
| `country` | `country-post` | `country-comment` | `countryPost` | Yes |
| `militaryUnit` | `military-unit-post` | `military-unit-comment` | `unitPost` | Yes |
| `party` | `party-post` | `party-comment` | `viewPartyPost` | Yes |
| `community` | `community-post` | `community-comment` | `communityPost` | Officials only |

`canPost` per feed says whether you may post. `SERVER_DATA.wallData` also gives `postsPerPage` (20), `commentsPerPage` (5), `postCharactersLimit` (280) and `commentCharactersLimit` (480); on the community tab the client applies the comment limit to posts too. Each tab has an `unreadCount`, which the client resets when you open it; loading a feed may therefore mark its posts as read.

---

## Load a feed

`GET /en/main/{feedRoute}/retrieve/json` · Auth required · ✅ Verified 2026-11-27

The first page of a tab. Verified with `wall-post` and `community-post`.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `firstPostId` | query | number | No | The feed's `firstPostId`, set when the page was opened from a link to one post |

```bash
curl 'https://www.erepublik.com/en/main/wall-post/retrieve/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened; a community post:

```json
{
  "wallData": {
    "wallType": "community",
    "params": { "wallType": "community", "viewPost": "communityPost", "wallPosts": "community-post", "wallComments": "community-comment" },
    "isGroupWall": true,
    "showDictator": false,
    "showPoliticalTitles": false,
    "canPost": false
  },
  "citizenData": {
    "2": { "id": 2, "name": "Plato", "avatar": "https://cdnt.erepublik.net/…", "profileUrl": "//www.erepublik.com/en/citizen/profile/2", "isDictator": false, "politicalTitle": "" }
  },
  "wallPosts": [
    {
      "id": 75,
      "authorId": 2,
      "title": false,
      "message": "Dear Citizens,<br />…",
      "createdAt": 1793630748,
      "createdAtTimeAgo": "25 days ago",
      "canRemove": false,
      "canReport": false,
      "showCommentsAndVotes": true,
      "showLinkToPost": true,
      "canVote": true,
      "canReact": true,
      "isVoted": false,
      "numVotes": 126,
      "reactions": { "1f44d": 69, "1f60d": 7, "1f606": 9, "1f632": 14, "1f614": 6, "1f621": 10, "1f44e": 10 },
      "reactionId": false,
      "votedBy": [],
      "canComment": false,
      "numComments": 0,
      "commentedBy": [],
      "meta": {
        "reportURL": "//www.erepublik.com/en/main/tickets-report/75/community-post",
        "isAutopost": false,
        "isOwnPost": false,
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
  "pages": 4,
  "hasMore": true
}
```

| Field | Type | Description |
|-------|------|-------------|
| `wallData` | object | Wall type, route names, permissions and display settings |
| `citizenData` | object | Authors, keyed by citizen ID |
| `wallPosts[]` | array | Up to 20 posts |
| `wallPosts[].id`, `authorId` | number | Post and author |
| `wallPosts[].message` | string | HTML: `<br />`, emoji as `<q class="emoji emoji_1f601">`; Unicode text is kept |
| `wallPosts[].createdAt`, `createdAtTimeAgo` | number, string | Unix time; relative text |
| `wallPosts[].canRemove`, `canReport`, `canVote`, `canReact`, `canComment` | boolean | Your permissions |
| `wallPosts[].reactions` | object | Count per emoji code point |
| `wallPosts[].reactionId` | `false` or value | Your reaction |
| `wallPosts[].numVotes`, `numComments` | number | Totals |
| `wallPosts[].meta` | object | Report link and post type flags; `isAutomaticMedalPost` marks [auto-posts](../home/badges.md#auto-post), which the client collapses |
| `currentPage`, `pages`, `hasMore` | number, number, boolean | Pagination |

### Errors

Without a session: HTTP `200`, `{"error":"not_authorized"}`.

**Source:** real requests, 2026-11-27; `erpk_angular_controllers.js` (version 1795779904), `ErpkFeedController`, `ErpkFeedFactory`.

---

## Older posts

`POST /en/main/{feedRoute}/older/json` · Auth required · 🔍 From code

The **load more** request. It only reads.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `lastPostId` | number | Yes | Smallest post ID already loaded |
| `page` | number | Yes | `currentPage + 1` |

```bash
curl 'https://www.erepublik.com/en/main/wall-post/older/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&lastPostId=12345678&page=2'
```

The client merges `citizenData`, `wallData` and `wallPosts` and reads `currentPage`, `pages`, `hasMore`: the shape of [load a feed](#load-a-feed).

**Source:** same script, `loadMore`.

---

## Create a post

`POST /en/main/{feedRoute}/create/json` · Auth required · 🔍 From code

> [!WARNING]
> Publishes a post.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `post_message` | string | Yes | Text |
| `post_as` | number | Yes | `0` posts as yourself, the client default |

```bash
curl 'https://www.erepublik.com/en/main/wall-post/create/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'post_as=0&post_message=Hello%20world&_token=YOUR_CSRF_TOKEN'
```

### Response

The shape of [load a feed](#load-a-feed), with the new post as `wallPosts[0]`.

### Errors

HTTP `200` with `error` and `message`. Earlier notes report a limit of one post every few minutes (typically 3–5):

```json
{ "error": true, "message": "You must wait 4 minutes before posting again" }
```

**Source:** same script, `postPost`; error and limit — earlier notes.

---

## React to a post

`POST /en/main/{feedRoute}/vote/json` · Auth required · 🔍 From code

> [!WARNING]
> Records your reaction on a post.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `postId` | number | Yes | Post |
| `reactionId` | | Yes | Chosen reaction |

```bash
curl 'https://www.erepublik.com/en/main/wall-post/vote/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&postId=12345678&reactionId=REACTION_ID'
```

The client reads `numVotes`, `reactions`, `reactionId`, or `error` with `message`.

**Source:** same script, `postReact`.

---

## Delete a post

`POST /en/main/{feedRoute}/delete/json` · Auth required · 🔍 From code

> [!WARNING]
> Deletes the post. The client asks for confirmation first.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `postId` | number | Yes | Post with `canRemove: true` |

```bash
curl 'https://www.erepublik.com/en/main/wall-post/delete/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'postId=12345678&_token=YOUR_CSRF_TOKEN'
```

### Response

The client reads only `error` and `message`. Examples from earlier notes:

```json
{ "status": true, "message": "Post deleted successfully" }
```

```json
{ "error": true, "message": "This post is not available anymore" }
```

```json
{ "error": true, "message": "You are not authorized to delete this post" }
```

All with HTTP `200`. Earlier notes: you can delete your own posts; an invalid or expired token also fails.

**Source:** same script, `postDelete`; examples — earlier notes.

---

## Load comments

`POST /en/main/{commentRoute}/retrieve/json` · Auth required · 🔍 From code

Sent when the comments of a post are first opened. It only reads.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `postId` | number | Yes | Post |

```bash
curl 'https://www.erepublik.com/en/main/wall-comment/retrieve/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&postId=12345678'
```

The client reads `postComments[]` (with `id`), or `error` with `message`. A link to a post may carry `?commentId={id}`, which the client highlights.

**Source:** same script, `commentsToggle`.

---

## Create a comment

`POST /en/main/{commentRoute}/create/json` · Auth required · 🔍 From code

> [!WARNING]
> Publishes a comment.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `postId` | number | Yes | Post |
| `comment_message` | string | Yes | Text, up to `commentCharactersLimit` |

```bash
curl 'https://www.erepublik.com/en/main/wall-comment/create/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&postId=12345678&comment_message=Hello'
```

The client appends `postComments[0]` of the response.

**Source:** same script, `postComment`.

---

## React to a comment

`POST /en/main/{commentRoute}/vote/json` · Auth required · 🔍 From code

> [!WARNING]
> Records your reaction on a comment.

Fields: `_token`, `postId`, `commentId`, `reactionId`.

```bash
curl 'https://www.erepublik.com/en/main/wall-comment/vote/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&postId=12345678&commentId=1&reactionId=REACTION_ID'
```

The client reads `numVotes`, `reactions`, `reactionId`.

**Source:** same script, `commentReact`.

---

## Delete a comment

`POST /en/main/{commentRoute}/delete/json` · Auth required · 🔍 From code

> [!WARNING]
> Deletes the comment.

Fields: `_token`, `postId`, `commentId`.

```bash
curl 'https://www.erepublik.com/en/main/wall-comment/delete/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&postId=12345678&commentId=1'
```

The client reads `error` and `message`.

**Source:** same script, `commentDelete`.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Where posts appear | Profile and country walls | Home page feed tabs | Code |
| `post_as` | Other values likely for organizations | `postAs` was `null` | Not settled |
| `reactionId` values | Not described | Probably the emoji code points in `reactions` | Not settled |
| `firstPostId` | Not described | `firstPostId=1` returned an ordinary first page | Not settled |
| `votedBy`, `commentedBy` | Not described | Empty in the posts read | — |
| Delete success | `status`, `message` | Client checks only `error` | Both listed |
| Reporting and moderation | Not described | `meta.reportURL`; the moderator script (`ModerationService`) is for moderators only | Not described here |
