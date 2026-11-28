# Alliances

[API docs](../README.md) › Country and politics › Alliances

Alliances are groups of countries with a leader and member titles. The alliance of a country is linked from every [country page header](society.md#page-header). Leader and president actions go through two POST endpoints.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Alliance page](#alliance-page) | GET | `/en/main/alliances`, `/en/main/alliances/{allianceId}` | ✅ Verified 2026-11-27 |
| [Member countries](#member-countries) | GET | `/en/main/alliances/{allianceId}/members` | ✅ Verified 2026-11-27 |
| [League popup](#league-popup) | GET | `/en/main/league-info` | ✅ Verified 2026-11-27 |
| [Propose an alliance law](#propose-an-alliance-law) | POST | `/en/main/createlaw` | 🔍 From code |
| [Vote and change titles](#vote-and-change-titles) | POST | `/en/main/alliance-callback` | 🔍 From code |
| [Upload the logo](#upload-the-logo) | POST | URL from the page | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Alliance page

`GET /en/main/alliances` · `GET /en/main/alliances/{allianceId}` · Auth required · ✅ Verified 2026-11-27

Without an ID the page shows one alliance chosen by the server.

```bash
curl 'https://www.erepublik.com/en/main/alliances/33' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Data | Selector |
|------|----------|
| ID | `input#allianceId` |
| Logo | `img#avatar` |
| Name and tag | `.header_right h2` (`Nexus (Nexus)`) |
| Member count | `p.top_text` |
| Founded | `Since 2026-01-19` |
| Leader | `.leader_info a` |
| Countries | `ul.country_members li a` |
| Description | `.alliances_details` (player-written HTML from BBCode) |

The page links to the [alliance rankings](../rankings/rankings.md#alliances) and carries `input#_token` and the confirmation texts for changing a title, leaving and revoking a member.

**Source:** real requests, 2026-11-27.

---

## Member countries

`GET /en/main/alliances/{allianceId}/members` · Auth required · ✅ Verified 2026-11-27

`table.list_members`:

| Column | Content |
|--------|---------|
| № | Row number |
| Country | Link and flag |
| Country President | Avatar and name |
| Title | `span#title_holder_{countryId}`, hidden `input#member_title_{countryId}` |
| *(last)* | Empty for an outsider |

Profile links on this page may use another language prefix (`/sr/`).

**Source:** real request, 2026-11-27.

---

## League popup

`GET /en/main/league-info` · Auth required · ✅ Verified 2026-11-27

The League of Allies event popup (`generalPopup.renderPopup('leagueOfAllies')`). On 2026-11-27 the event showed as finished.

```bash
curl 'https://www.erepublik.com/en/main/league-info' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

```html
<section class="leagueOfAlliesPopup">
  <div class="countdown"><span class="text">Finished</span></div>
  <div class="myPoints"><span class="text">Total points</span> <span class="number">0</span></div>
  <div class="loa_user_info"><p>LeagueName</p><img src="//www.erepublik.net/images/modules/misc/loa_4.png"></div>
  <div class="content" id="popupContent"></div>
  <section class="tabs">
    <a class="tab progress" data-tab="progress">Progress</a>
    <a class="tab rules" data-tab="rules">Rules</a>
    <a class="tab leaderboards" data-tab="leaderboards">Leaderboards</a>
    <a class="tab prizes" data-tab="prizes">Prizes</a>
  </section>
</section>
```

While the event runs, the leaderboards screen adds a league tab (`erepublik.info.leagueOfAlliesLeaderboards`).

**Source:** real request, 2026-11-27; popup map in `erepublik.js` (version 1795779896).

---

## Propose an alliance law

`POST /en/main/createlaw` · Auth required (role) · 🔍 From code

> [!WARNING]
> Changes alliance membership or leadership, or starts a proposal to do so.

| `action` | Other fields | Meaning |
|----------|--------------|---------|
| `alliance_invitation` | `country_list` = invited country | Invite a country |
| `propose_commander` | `new_leader` = citizen ID, `country_list` | Propose a new leader |
| `revoke_membership` | `country_list` = country ID | Remove a member |
| `leave_alliance` | `country_list` = country permalink | Leave |

Every request also sends `_token`.

```bash
curl 'https://www.erepublik.com/en/main/createlaw' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=alliance_invitation&country_list=COUNTRY&_token=YOUR_CSRF_TOKEN'
```

The client reads `error` and `message`; after leaving it also reads `params.countryPermalink` and opens that country's administration page.

Before proposing a leader the client looks the citizen up by `citizenId` with the POST variant of [Current user](../basics/framework.md#look-up-a-citizen).

**Source:** `alliances/main.js` (version 1698060179).

---

## Vote and change titles

`POST /en/main/alliance-callback` · Auth required (role) · 🔍 From code

> [!WARNING]
> Casts a vote or renames a member country's title.

| `action` | Other fields |
|----------|--------------|
| `vote-law` | `lawId`, `option`: `yea` or `ney` |
| `change-title` | `countryId`, `title` (not empty) |

Both send `_token`.

| Response field | Used by |
|----------------|---------|
| `error`, `message` | Both |
| `lawId`, `yea`, `ney` | `vote-law`: which tally to increase |

**Source:** `alliances/main.js` (version 1698060179), `voteLaw`, `changeTitle`.

---

## Upload the logo

`POST {SERVER_DATA.updateAllianceUrl}` · Auth required (role) · 🔍 From code

> [!WARNING]
> Replaces the alliance logo.

JPEG or PNG, one file, up to 5 MB.

1. `action=prepare-avatar`, `allianceId`, `params` = JSON `{name, type, size, allianceId}`, `_token`. The response's `formData` is attached to the upload.
2. The file goes to `SERVER_DATA.avatarEndpoint`; the client reads `ETag` from the XML reply.
3. `action=avatar`, `allianceId`, `params` = JSON `{etag, mimetype, allianceId}`, `_token`.

Neither URL is present on the page for an outsider.

**Source:** `alliances/main.js` (version 1698060179), `allianceProfile`.

---

## Open questions

| Topic | Notes |
|-------|-------|
| Which alliance `/en/main/alliances` picks | The rule is unknown |
| Creating an alliance | No request seen |
| Pending alliance proposals | The voting widgets (`.vote_option`) were not visible |
