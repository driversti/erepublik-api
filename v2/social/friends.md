# Friends and blocking

[API docs](../README.md) › Social › Friends and blocking

The friends screen of a profile, the friend buttons on profiles and [hovercards](profiles.md#hovercard), and the block button.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Friends page](#friends-page) | GET | `/en/main/citizen-friends/{citizenId}` | ✅ Verified 2026-11-27 |
| [Friend list page](#friend-list-page) | GET | `/en/main/citizen-friends/{citizenId}/{page}/list` | ✅ Verified 2026-11-27 |
| [Add or remove a friend](#add-or-remove-a-friend) | POST | `/en/main/citizen-addRemoveFriend/` | 🔍 From code |
| [Remove link of the friends page](#remove-link-of-the-friends-page) | GET | `/en/main/citizen-friends/{citizenId}/{friendId}/remove` | 🔍 From code |
| [Block or unblock](#block-or-unblock) | POST | `/en/main/citizen-blocklist-ajax/` | 🔍 From code |
| [Invited friends page](#invited-friends-page) | GET | `/en/main/citizen-invited-friends/{page}` | ✅ Verified 2026-11-27 |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Friends page

`GET /en/main/citizen-friends/{citizenId}` · Auth required · ✅ Verified 2026-11-27

The HTML screen. Its script reads `#current_page` and `#maxim_page` and, on **Previous** / **Next**, loads [one list page](#friend-list-page) as `{link href}/{page}/list`, replacing `#friends_list` and `#results`.

```bash
curl 'https://www.erepublik.com/en/main/citizen-friends/2' -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real request for the logged-in citizen's own page, 2026-11-27, with its inline scripts.

---

## Friend list page

`GET /en/main/citizen-friends/{citizenId}/{page}/list` · Auth required · ✅ Verified 2026-11-27

Up to 20 friends of any citizen. The response is a JSON envelope around an HTML table.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `citizenId` | path | number | Yes | Whose friends |
| `page` | path | number | Yes | 1-based; page 3 holds entries 41–60 |

```bash
curl 'https://www.erepublik.com/en/main/citizen-friends/2/1/list' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

Earlier notes also send `Accept: application/json, text/javascript, */*; q=0.01` and an optional `Referer` of the friends page.

### Response

```json
{ "results": "1 - 12", "content": "<script>…</script>\n<table width=\"100%\" class=\"lister\">…</table>" }
```

<details>
<summary>Example rows (names replaced)</summary>

```html
<table width="100%" class="lister">
  <tr id="friend_1000001">
    <td class="friend_info">
      <a hovercard=1 href="/en/citizen/profile/1000001" title="PlayerName">
        <img src="https://cdnt.erepublik.net/…" alt="PlayerName" width="55" height="55" />
        <strong>PlayerName</strong>
      </a>
    </td>
  </tr>
  <tr id="friend_1000002" class="dead">
    <td class="friend_info">…</td>
  </tr>
</table>
```

</details>

| Value | Source in `content` |
|-------|---------------------|
| Citizen ID | Row `id` (`friend_{id}`) or the profile link |
| Name | `<strong>` text, also the link `title` |
| Avatar | `<img src>` |
| Dead or banned account | Row has `class="dead"`; the profile link still resolves |
| Actions (your own list) | `td.actions`: `a.send` (compose a message) and `a.remove` ([remove link](#remove-link-of-the-friends-page)) |

> [!WARNING]
> On your own list every `a.remove` link carries your CSRF token in its URL. Do not publish the response, and never follow these links when crawling.

`content` starts with an inline `<script>` for the remove dialog. No action links were found in another citizen's list.

### Errors

A page past the last friend returns HTTP `404`. Without a session the request is redirected to the home page (HTML, `200`).

**Source:** real requests, 2026-11-27; example rows — earlier notes.

---

## Add or remove a friend

`POST /en/main/citizen-addRemoveFriend/` · Auth required · 🔍 From code

> [!WARNING]
> Sends or cancels a friend request, or removes a friend.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `citizenId` | number | Yes | The other citizen |
| `action` | string | Yes | `addFriend`, `removeFriend` or `cancelPending` |
| `requestedFrom` | string | No | `hovercard`, sent when removing from a hovercard |
| `url` | string | No | The hovercard also posts its own `actionURL` as a field |

```bash
curl 'https://www.erepublik.com/en/main/citizen-addRemoveFriend/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&citizenId=1000001&action=addFriend'
```

The hovercard posts to the button's `actionURL`, which has no trailing slash.

### Response

The profile page reads `error` and `message`, or on success `isFriend` and `isFriendRequestPending`. The hovercard treats a response with `status` as a message (`message`) and one without it as new hovercard data.

Examples from earlier notes:

```json
{ "status": true, "message": "Friend request sent successfully" }
```

```json
{ "message": "PlayerName already has a friendship request pending from you.", "status": false, "error": true }
```

### Errors

Earlier notes list: a request is already pending, already friends (`addFriend`), not friends (`removeFriend`).

**Source:** `erpk_angular_controllers.js` (version 1795779904), `ProfileFactory.friendAction`, `HovercardFactory`; `erpk_angular_directives.js`, `hovercardDetails`; examples — earlier notes.

---

## Remove link of the friends page

`GET /en/main/citizen-friends/{citizenId}/{friendId}/remove?_token={token}` · Auth required · 🔍 From code

> [!WARNING]
> A `GET` that removes a friend. The page asks for confirmation first; the URL itself does not.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `citizenId` | path | number | Yes | Your citizen ID |
| `friendId` | path | number | Yes | Friend to remove |
| `_token` | query | string | Yes | CSRF token, prefilled in the link |

**Source:** links and dialog script in the friends page, 2026-11-27; not requested.

---

## Block or unblock

`POST /en/main/citizen-blocklist-ajax/` · Auth required · 🔍 From code

> [!WARNING]
> Changes your blocklist.

The button appears on other citizens' profiles, except on citizen `2` (Plato).

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `blockedId` | number | Yes | The other citizen |
| `action` | string | Yes | `block` or `unblock` |

```bash
curl 'https://www.erepublik.com/en/main/citizen-blocklist-ajax/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&blockedId=1000001&action=block'
```

### Response

The client reads `error` with `message`, or `isBlocked`. The current state is `isBlocked` in the [profile data](profiles.md#profile-data).

The profile page links to a blocklist screen, `/en/main/citizen-blocklist`; it returned `404` on 2026-11-27.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `blockAction`.

---

## Invited friends page

`GET /en/main/citizen-invited-friends/{page}` · Auth required · ✅ Verified 2026-11-27

HTML list of the players you invited, with the columns **Citizen name**, **Collected Gold** and **Send message**.

```bash
curl 'https://www.erepublik.com/en/main/citizen-invited-friends/1' -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real request, 2026-11-27.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Page past the end | Empty or short table | HTTP `404` | Live response |
| `results` | Range of the requested page | `1 - 12` for a 12-friend list | Live response |
| Success of add or remove | `status`, `message` | Profile reads `isFriend`, `isFriendRequestPending` | Both listed |
| Visibility of a list | "Any citizen whose friend list is public" | One other citizen's list was readable; no privacy setting was found | Not settled |
| Blocklist screen | Not described | Linked, but `404` | Live response |
