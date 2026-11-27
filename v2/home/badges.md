# Badges, achievements, auto-posts

[API docs](../README.md) › Home and daily routine › Badges, achievements, auto-posts

The invitation **Badges** page, and the endpoint that shares a medal or reward on your wall.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Badges page](#badges-page) | GET | `/en/badges` | ✅ Verified 2026-11-27 |
| [Auto-post](#auto-post) | POST | `/en/main/wall-post/automatic` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Badges page

`GET /en/badges` · ✅ Verified 2026-11-27

An HTML page with referral banners in five sizes (200×80, 125×125, 125×25, 200×125, 300×250) and the code to embed them. It has no data endpoint and these are not achievement medals. Only the logged-in request was tried.

```bash
curl 'https://www.erepublik.com/en/badges' -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real request, 2026-11-27.

---

## Auto-post

`POST /en/main/wall-post/automatic` · Auth required · 🔍 From code

> [!WARNING]
> Publishes a post on your wall.

Three places in the client call it, each with its own fields.

| Caller | `_token` | `message` | Other fields |
|--------|----------|-----------|--------------|
| Award popup (`awards.js`) | `#award_token` value, not the page token | The checkbox value, text filled in by the server | `awardId` |
| Achievement popup (Angular) | CSRF token | `achievement.automatic_type` | `achievementId` |
| Pack opening results (path ends with `/`) | CSRF token | `legendary_pull`, `epic_pull` or `pull` | — |

```bash
curl 'https://www.erepublik.com/en/main/wall-post/automatic' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode '_token=YOUR_AWARD_CSRF_TOKEN' \
  --data-urlencode 'message=I earned a Hard Worker medal!' \
  --data-urlencode 'awardId=12345'
```

### Response

For the award popup:

```json
{ "message": true }
```

```json
{ "message": false, "error_message": "Something went wrong" }
```

The achievement popup treats a response with `wallData` as success and shows `message` otherwise. Posts made this way have `meta.isAutomaticMedalPost: true` ([wall posts](../../social/wall.md)).

### Award popup flow

- Awards are rendered by the server as hidden `.home_reward` blocks on the home page and shown one by one (`nextAward()`); other popups wait (`erepublik.settings.achievementPopup`).
- The post is sent only if **Share on wall** is checked.
- `awardId` is the fourth part of the checkbox ID `award_checkbox_{index}_{awardId}`.

**Source:** `awards.js` (version 1698060179); `CitizenAchievementsFactory` and the pack controller in `erpk_angular_controllers.js` (version 1795779904); award examples — earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Variants | Only the award popup | Three callers | Code |
| Angular achievements | Not described | `getAchievements` returns an empty list, so the popup only shows pushed items | Code |
| Awards on the home page | Always rendered when pending | None pending on 2026-11-27 | — |
