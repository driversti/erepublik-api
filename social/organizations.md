# Organizations and money donations

[API docs](../README.md) › Social › Organizations and money donations

Organizations are accounts without a player (national banks, holdings). They share the citizen ID space and the profile endpoints. This page also covers the popup for managing them and the request that answers a money donation.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Manage organizations popup](#manage-organizations-popup) | GET | `/en/main/manage-organizations-popup` | 🔍 From code |
| [Accept or reject a donation](#accept-or-reject-a-donation) | GET | `/en/main/money-donation/{action}/{donationId}` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Organization profiles

Read them with the [profile data](profiles.md#profile-data) endpoints. The response has `citizen.is_organization: true` and exposes the organization's balances, **also without a session** (checked 2026-11-27):

```json
{ "organization": { "accounts": [ { "type": "currency", "amount": 0 }, { "type": "gold", "amount": 0 } ] } }
```

Amounts are replaced with `0` here.

| Field | Type | Description |
|-------|------|-------------|
| `organization.accounts[].type` | string | `currency` (local currency) or `gold` |
| `organization.accounts[].amount` | number | Balance, 2 decimals |

For a citizen who is not an organization, `accounts` was an empty array.

| Field | Player | Organization (earlier notes) |
|-------|--------|------------------------------|
| `citizen.level` | Varies | `1` |
| `citizen.avatar_version`, `avatar_file` | Hash; `jpg` / `png` | `null` |
| `citizenAttributes.experience_points` | Varies | `0` |
| `military.militaryData.strength` | Varies | `35` (default) |
| `military.militaryUnit`, `bestDamageData`, `nukes`, `newspaper` | Object or `false` | `false` |
| `partyData` | Object | Absent |
| `city.residenceCityId` | Number | `null` (confirmed 2026-11-27) |
| `friends.number` | Varies | Typically very low |

<details>
<summary>Full example response (earlier notes; names, IDs, dates and balances replaced)</summary>

```json
{
  "citizen": {
    "id": 1000000,
    "name": "OrganizationName",
    "created_at": "2000-01-01",
    "is_organization": true,
    "is_alive": true,
    "has_avatar": true,
    "avatar_version": null,
    "avatar_file": null,
    "level": 1,
    "avatar": "https://cdnt.erepublik.net/.../avatar.jpg",
    "profile_url": "//www.erepublik.com/en/citizen/profile/1000000",
    "onlineStatus": false,
    "banStatus": { "type": false, "reason": false },
    "nextLevelXp": 10
  },
  "citizenAttributes": {
    "citizen_id": 1000000,
    "experience_points": 0,
    "level": 1
  },
  "isAdult": true,
  "aboutMe": "About text",
  "location": {
    "citizenLocationInfo": {
      "citizen_id": 1000000,
      "citizenship_country_id": 72,
      "residence_country_id": 23,
      "residence_region_id": 100
    },
    "residenceCountry": {
      "id": 23,
      "name": "Canada",
      "permalink": "Canada",
      "empire": 0
    },
    "citizenshipCountry": {
      "id": 72,
      "name": "Lithuania",
      "permalink": "Lithuania",
      "empire": 0
    },
    "residenceRegion": {
      "id": 100,
      "name": "Quebec",
      "current_owner_country_id": 23,
      "original_owner_country_id": 23,
      "permalink": "Quebec"
    }
  },
  "city": {
    "residenceCityId": null,
    "residenceRoleName": ""
  },
  "achievements": [
    {
      "name": "Freedom Fighter",
      "img": "achievement_freedomfighter",
      "descriptionBefore": "Liberate occupied regions by fighting in Resistance Wars.",
      "descriptionAfter": "Liberate occupied regions by fighting in Resistance Wars.",
      "moreInfo": "<a href='/en/main/latest-updates/0/74'><strong>More info</strong></a>",
      "count": 0
    }
  ],
  "isAmbassador": false,
  "isTopPlayer": false,
  "isPresident": false,
  "isCongressman": false,
  "isPartyPresident": false,
  "isPartyMember": false,
  "isAssociateMember": false,
  "isDictator": false,
  "isModerator": false,
  "isBanned": false,
  "freedomFighter": {
    "milestone": [],
    "progress": []
  },
  "friends": {
    "number": 1,
    "list": [
      {
        "id": 1000002,
        "name": "FriendName",
        "created_at": "2000-01-01 00:00:00",
        "is_alive": 1,
        "is_organization": 0,
        "has_avatar": 1,
        "avatar_version": "HASH",
        "avatar_file": "jpg",
        "avatar": "https://cdnt.erepublik.net/.../avatar.jpg?HASH"
      }
    ],
    "isFriend": false,
    "isFriendRequestPending": false
  },
  "military": {
    "militaryData": {
      "strength": 35,
      "temporaryStrength": 35,
      "divisionData": {
        "division": 1,
        "smallBombDamage": 75000,
        "bazookaBoosterDamage": 10000,
        "battleHeroReward": 2
      },
      "name": "Recruit ",
      "stars": 0,
      "points": 0,
      "rankNumber": 1,
      "icon": "//www.erepublik.net/images/modules/ranks/tanks/64px/001_recruit.png",
      "nextRankAt": 15,
      "progress": 0,
      "ground": {
        "strength": 35,
        "temporaryStrength": 35,
        "divisionData": {
          "division": 1,
          "smallBombDamage": 75000,
          "bazookaBoosterDamage": 10000,
          "battleHeroReward": 2
        },
        "name": "Recruit ",
        "stars": 0,
        "points": 0,
        "rankNumber": 1,
        "icon": "//www.erepublik.net/images/modules/ranks/tanks/64px/001_recruit.png",
        "nextRankAt": 15,
        "progress": 0
      },
      "aircraft": {
        "coordination": 0,
        "divisionData": {
          "division": 11,
          "battleHeroReward": 10
        },
        "name": "Airman ",
        "stars": 0,
        "points": 0,
        "rankNumber": 1,
        "icon": "//www.erepublik.net/images/modules/ranks/aircraft/64px/001_airman.png",
        "nextRankAt": 10,
        "progress": 0
      }
    },
    "militaryUnit": false,
    "bestDamageData": false,
    "bestDamageAchievementProgress": 0,
    "bestAirDamageAchievementProgress": 0,
    "truePatriot": {
      "hasTruePatriotProgress": false,
      "progress": 0,
      "currentLevel": 0,
      "nextLevel": 100000,
      "damage": 0,
      "citizenship_id": 72,
      "history": []
    }
  },
  "nukes": false,
  "title": [],
  "decorations": [],
  "activePacks": [],
  "isPlatoFoundationCurrentMember": false,
  "isPlatoFoundationFormerMember": false,
  "newspaper": false,
  "loggedIn": [],
  "pvpStats": {
    "matches_played": 0,
    "matches_won": 0
  },
  "isFallen": false,
  "organization": {
    "accounts": [
      { "type": "currency", "amount": 0 },
      { "type": "gold", "amount": 0 }
    ]
  },
  "isLoggedIn": false,
  "isBlocked": false,
  "nationalRank": { "xp": 0 },
  "events": [],
  "activePacksAmount": 0
}
```

</details>

The [hovercard](profiles.md#hovercard) of an organization has `isOrg: true`, `politicalTitle: "Organization"` and empty `badges`, `activity`, `fighterInfo` and `interactionButtons`.

On the profile page, organizations have no friend button; the report link ends in `/organization` (`/en/main/tickets-report/{id}/organization`). The page also builds links to the economy screens `/en/economy/donate-money/{id}` and `/en/economy/donate-items/{id}`.

---

## Manage organizations popup

`GET /en/main/manage-organizations-popup` · Auth required · 🔍 From code

The popup `manageOrganizations`, an HTML fragment for managing organization accounts linked to your citizen.

```bash
curl 'https://www.erepublik.com/en/main/manage-organizations-popup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

A request on 2026-11-27 returned HTTP `403` with an empty body. A likely reason is that the account has no right to manage organizations (not confirmed).

Related: the country administration screen shows a "Manage Organizations" button, disabled for anyone but the president ([administration](../country/administration.md)).

**Source:** `erepublik.js` (version 1795779896), `renderPopup`; real request, 2026-11-27; purpose — earlier notes.

---

## Accept or reject a donation

`GET /en/main/money-donation/{action}/{donationId}` · Auth required · 🔍 From code

> [!WARNING]
> A `GET` that accepts or rejects a pending money donation, so it moves or refuses money.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `action` | path | string | Yes | Earlier notes: `accept` or `reject` |
| `donationId` | path | number | Yes | The pending donation |
| `_token` | query | string | Yes | CSRF token (value of `#_token`) |

```bash
curl 'https://www.erepublik.com/en/main/money-donation/accept/123456?_token=YOUR_CSRF_TOKEN' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

### Response

The client parses JSON, shows `message` if it is not empty, and reloads the page.

```json
{ "message": "" }
```

**Source:** `erepublik.js` (version 1795779896), `erepublik.functions.acceptRejectDonation`; example — earlier notes. Not requested.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Donation actions | `accept`, `reject`, for example a treasury donation proposed by Congress | Action names come from the calling page, not seen | Earlier notes |
| Acting as an organization | Wall `post_as` values other than `0` may mean organizations | `postAs` was `null` | Not settled |
| Popup content | Manages organization accounts | `403` | — |
