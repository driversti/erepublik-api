# Citizen profiles

[API docs](../README.md) › Social › Citizen profiles

The profile screen of a citizen and the hovercard shown over names. The screen is an HTML shell that loads one JSON document; [organizations](organizations.md) use the same endpoints.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Profile page](#profile-page) | GET | `/en/citizen/profile/{citizenId}` | ✅ Verified 2026-11-27 |
| [Profile data](#profile-data) | GET | `/en/main/citizen-profile-json-global/{citizenId}` | ✅ Verified 2026-11-27 |
| [Base variant](#base-variant) | GET | `/en/main/citizen-profile-json/{citizenId}` | ✅ Verified 2026-11-27 |
| [Personal variant](#personal-variant) | GET | `/en/main/citizen-profile-json-personal/{citizenId}` | ✅ Verified 2026-11-27 |
| [Hovercard](#hovercard) | GET | `/en/main/citizen-hovercard/{citizenId}` | ✅ Verified 2026-11-27 |
| [Switch strength](#switch-strength) | GET | `/en/main/switch-strength` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Profile page

`GET /en/citizen/profile/{citizenId}` · Auth not checked · ✅ Verified 2026-11-27

An HTML page driven by `ErpkProfileController`, which fetches the [personal variant](#personal-variant) when logged in and the [global one](#profile-data) otherwise. Only the logged-in page was requested. Pages also link the form `/en/main/citizen/profile/{citizenId}`.

| Button or link | Target |
|----------------|--------|
| Friend, block | [Friends and blocking](friends.md) |
| Friend list | `/en/main/citizen-friends/{id}` ([friends](friends.md#friends-page)) |
| Send message | `/en/main/messages-compose/{id}` ([messages](messages.md#compose)) |
| Report | [`/en/main/tickets-report/{id}/citizen`](../basics/framework.md#report-content) or `/organization` |
| Donate money, items | `/en/economy/donate-money/{id}`, `/en/economy/donate-items/{id}` |
| Switch strength | [Event action](#switch-strength) |

```bash
curl 'https://www.erepublik.com/en/citizen/profile/2' -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real request, 2026-11-27, with the page's templates; `erpk_angular_controllers.js` (version 1795779904), `ProfileFactory`.

---

## Profile data

`GET /en/main/citizen-profile-json-global/{citizenId}` · No auth · ✅ Verified 2026-11-27

Everything the profile shows: personal data, location, achievements, military stats, a friend sample, decorations and more. About 50 KB for an active player (earlier notes); about 12 KB for citizen `2`.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `citizenId` | path | number | Yes | Citizen or organization ID |

```bash
curl 'https://www.erepublik.com/en/main/citizen-profile-json-global/2' \
  -H 'X-Requested-With: XMLHttpRequest'
```

With a session cookie this variant still answered `isLoggedIn: false`, `loggedIn: []` and `events: []`.

### Response

Shortened:

```json
{
  "citizen": { "id": 1234567, "name": "PlayerName", "level": 100, "is_organization": false },
  "location": { "citizenshipCountry": { "id": 1, "name": "CountryName" } },
  "military": { "militaryData": { "strength": 1000, "rankNumber": 50 } },
  "friends": { "number": 100, "list": [], "isFriend": false, "isFriendRequestPending": false },
  "loggedIn": [],
  "isLoggedIn": false
}
```

<details>
<summary>Full example response (earlier notes; synthetic values)</summary>

```json
{
  "citizen": {
    "id": 1234567,
    "name": "PlayerName",
    "created_at": "2010-01-01",
    "is_organization": false,
    "is_alive": true,
    "has_avatar": true,
    "avatar_version": "HASH",
    "avatar_file": "png",
    "level": 100,
    "avatar": "https://cdnt.erepublik.net/.../avatar.png",
    "profile_url": "//www.erepublik.com/en/citizen/profile/1234567",
    "onlineStatus": true,
    "banStatus": {
      "type": false,
      "reason": false
    },
    "nextLevelXp": 101000
  },
  "citizenAttributes": {
    "citizen_id": 1234567,
    "experience_points": 100000,
    "level": 100
  },
  "isAdult": true,
  "aboutMe": "Profile description with HTML...",
  "location": {
    "citizenLocationInfo": {
      "citizen_id": 1234567,
      "citizenship_country_id": 1,
      "residence_country_id": 1,
      "residence_region_id": 100
    },
    "residenceCountry": {
      "id": 1,
      "name": "CountryName",
      "permalink": "CountryName",
      "empire": 0
    },
    "citizenshipCountry": {
      "id": 1,
      "name": "CountryName",
      "permalink": "CountryName",
      "empire": 0
    },
    "residenceRegion": {
      "id": 100,
      "name": "RegionName",
      "current_owner_country_id": 1,
      "original_owner_country_id": 1,
      "permalink": "RegionName"
    }
  },
  "city": {
    "residenceCityId": 100,
    "residenceRoleName": "Resident",
    "residenceCity": {
      "id": 100,
      "name": "CityName",
      "permalink": "CityName",
      "region_id": 100,
      "city_type_id": 2,
      "country_id": 1
    }
  },
  "achievements": [
    {
      "name": "Freedom Fighter",
      "img": "achievement_freedomfighter",
      "descriptionBefore": "Liberate occupied regions...",
      "descriptionAfter": "Liberate occupied regions...",
      "count": 1
    }
  ],
  "isAmbassador": false,
  "isTopPlayer": false,
  "isPresident": false,
  "isCongressman": false,
  "isPartyPresident": false,
  "isPartyMember": true,
  "isDictator": false,
  "isModerator": false,
  "isBanned": false,
  "friends": {
    "number": 100,
    "list": [
      {
        "id": 1000002,
        "name": "FriendName",
        "is_alive": 1,
        "avatar": "https://cdnt.erepublik.net/.../avatar.png"
      }
    ],
    "isFriend": false,
    "isFriendRequestPending": false
  },
  "military": {
    "militaryData": {
      "strength": 1000,
      "temporaryStrength": 1000,
      "divisionData": {
        "division": 4,
        "smallBombDamage": 1500000,
        "bazookaBoosterDamage": 40000,
        "battleHeroReward": 5
      },
      "name": "RankName",
      "stars": 1,
      "points": 1000000,
      "rankNumber": 50,
      "icon": "//www.erepublik.net/images/modules/ranks/tanks/64px/RANK_ICON.png",
      "ground": { "...": "ground combat stats" },
      "aircraft": {
        "name": "AviationRankName",
        "stars": 1,
        "points": 100000,
        "rankNumber": 30
      }
    },
    "militaryUnit": {
      "id": 101,
      "name": "UnitName",
      "country_id": 1,
      "member_count": 10
    },
    "bestDamageData": {
      "damage": 1000000,
      "battle_id": 100000,
      "side_id": 1,
      "against_country": "OtherCountryName",
      "region": "RegionName",
      "won": true
    },
    "truePatriot": {
      "hasTruePatriotProgress": true,
      "progress": 50,
      "currentLevel": 1000000,
      "damage": 1500000,
      "citizenship_id": 1
    }
  },
  "nukes": {
    "bombs": 10,
    "nukes": 5,
    "last": {
      "for": { "id": 1, "name": "CountryName" },
      "against": { "id": 2, "name": "OtherCountryName" },
      "day": "1,000",
      "type": "bomb"
    }
  },
  "decorations": [
    {
      "class": "decorationClass",
      "icon": "decorationIcon.png",
      "text": "Decoration description...",
      "amount": 1
    }
  ],
  "activePacks": {
    "combat_stash": { "...": "active enhancements" },
    "division_switch_pack": { "...": "pack details" }
  },
  "newspaper": {
    "id": 100001,
    "name": "NewspaperName",
    "stripped_title": "newspapername"
  },
  "partyData": {
    "id": 1001,
    "name": "PartyName",
    "is_party_president": 0
  },
  "nationalRank": {
    "xp": 1
  },
  "pvpStats": {
    "matches_played": 10,
    "matches_won": 5,
    "matches_lost": 5
  },
  "isLoggedIn": false,
  "isBlocked": false,
  "isFallen": false,
  "activePacksAmount": 2
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `citizen` | object | Basic citizen info (id, name, level, avatar, online status, ban status) |
| `citizenAttributes` | object | XP and level data |
| `isAdult` | boolean | Whether citizen is an adult player |
| `aboutMe` | string | Profile description (HTML encoded) |
| `location` | object | Citizenship and residence location data |
| `city` | object | City residence information |
| `achievements` | array | List of achievements with counts |
| `isAmbassador`, `isTopPlayer`, `isPresident`, … | boolean | Various role flags |
| `isAssociateMember` | boolean | Whether citizen is an associate member |
| `isDictator` | boolean | Whether citizen is currently a dictator |
| `isModerator` | boolean | Whether citizen is a game moderator |
| `isBanned` | boolean | Whether citizen is banned |
| `isFallen` | boolean | Whether citizen account is "fallen" (permanently inactive) |
| `freedomFighter` | object | Freedom Fighter progress: `milestone` (array) and `progress` (array) |
| `friends` | object | Friend count and sample list |
| `military` | object | Military stats, rank, MU, true patriot progress |
| `nukes` | object/false | Bomb/nuke usage count and last deployment (`false` if none) |
| `title` | array | Earned titles (empty array if none) |
| `decorations` | array | Earned decorations and medals |
| `activePacks` | object | Active enhancement packs |
| `activePacksAmount` | number | Count of currently active packs |
| `newspaper` | object/false | Owned newspaper info (`false` if none) |
| `partyData` | object | Political party membership |
| `nationalRank` | object | National ranking position |
| `pvpStats` | object | PvP match statistics |
| `isPlatoFoundationCurrentMember` | boolean | Whether citizen is a current Plato Foundation member |
| `isPlatoFoundationFormerMember` | boolean | Whether citizen was a former Plato Foundation member |
| `organization` | object | `accounts[]`: [organization balances](organizations.md#organization-profiles); empty for players |
| `loggedIn` | array \| object | `[]`, or `hovercardData` in the [personal variant](#personal-variant) |
| `events` | array \| object | `[]`, or event flags in the personal variant |
| `isLoggedIn` | boolean | `true` only in the personal variant |
| `isBlocked` | boolean | Whether you [blocked](friends.md#block-or-unblock) this citizen |
| `penalties` | | Seen only on your own profile (see below) |

- `friends.list` is a sample of about 9; the [friend list](friends.md#friend-list-page) has everyone.
- `aboutMe` is HTML-encoded and may contain `<br />`.
- `decorations[].amount` can exceed 1.
- Absent `nukes`, `militaryUnit`, `bestDamageData` and `newspaper` are `false`, not `null`.
- Military data has separate ground and aircraft ranks.

### Errors

An unknown ID gave HTTP `404` with `{"error":true,"message":"404 Not Found","status":false,"code":404}` (checked on the base variant).

**Source:** real requests with and without a session, 2026-11-27; example and field descriptions — earlier notes.

---

## Base variant

`GET /en/main/citizen-profile-json/{citizenId}` · No auth · ✅ Verified 2026-11-27

The same 36 top-level keys as [profile data](#profile-data), also without a session. No call to it was found in the scripts searched.

```bash
curl 'https://www.erepublik.com/en/main/citizen-profile-json/2' \
  -H 'X-Requested-With: XMLHttpRequest'
```

**Source:** real requests, 2026-11-27.

---

## Personal variant

`GET /en/main/citizen-profile-json-personal/{citizenId}` · Auth required · ✅ Verified 2026-11-27

[Profile data](#profile-data) seen by the logged-in citizen: `isLoggedIn: true`, relationship flags, and filled `loggedIn` and `events`.

```bash
curl 'https://www.erepublik.com/en/main/citizen-profile-json-personal/2' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Additions

`events`:

```json
{
  "events": {
    "april1st2017": {
      "isActive": false
    },
    "strengthEnhancement": {
      "isActive": false
    },
    "anniversaryWeek": {
      "isActive": false
    }
  }
}
```

The keys depend on the events in the code at the time. `loggedIn.hovercardData` is the [hovercard](#hovercard) with partly different key names: `born_on`, `is_dead`, `is_banned`, `is_online`, `number_of_friends`, `is_friend`, `is_self`, `is_org`, `is_adult` (snake case), but `politicalTitle`, `fighterInfo`, `interactionButtons` (camel case).

<details>
<summary>loggedIn.hovercardData (earlier notes; synthetic values)</summary>

```json
{
  "loggedIn": {
    "hovercardData": {
      "name": "PlayerName",
      "avatar": "https://cdnt.erepublik.net/.../avatar.jpg",
      "level": 100,
      "born_on": "Jan 01, 2010",
      "is_dead": false,
      "is_banned": false,
      "is_online": false,
      "number_of_friends": 100,
      "is_friend": true,
      "is_self": false,
      "citizenship": {
        "id": 1,
        "name": "CountryName",
        "permalink": "CountryName"
      },
      "politicalTitle": "",
      "is_org": false,
      "is_adult": true,
      "badges": [
        {
          "type": "militaryRank",
          "name": "RankName",
          "icon": "//www.erepublik.net/images/modules/ranks/tanks/64px/RANK_ICON.png"
        },
        {
          "type": "aviationRank",
          "name": "AviationRankName",
          "icon": "//www.erepublik.net/images/modules/ranks/aircraft/64px/AVIATION_RANK_ICON.png"
        }
      ],
      "activity": [
        {
          "type": "residenceCity",
          "id": 100,
          "name": "CityName",
          "avatar": "//www.erepublik.net/images/modules/cities/128px/CityName.png",
          "title": "Resident",
          "permalink": "//www.erepublik.com/en/main/city/CityName"
        },
        {
          "type": "politicalParty",
          "id": 1002,
          "name": "PartyName",
          "avatar": "//cdnt.erepublik.net/.../party.jpg",
          "title": "Party Member",
          "economical_orientation": "EconomicOrientation",
          "social_orientation": "SocialOrientation",
          "permalink": "//www.erepublik.com/en/party/1002"
        },
        {
          "type": "militaryUnit",
          "id": 102,
          "name": "UnitName",
          "avatar": "//cdnt.erepublik.net/.../mu.jpg",
          "title": "Soldier",
          "permalink": "//www.erepublik.com/en/military/military-unit/102"
        }
      ],
      "fighterInfo": {
        "military": {
          "rank": 50,
          "strength": 1000,
          "division": 4
        },
        "aviation": {
          "rank": 30,
          "perception": 0,
          "division": 11
        }
      },
      "interactionButtons": [
        {
          "type": "viewProfile",
          "title": "View profile",
          "enabled": true,
          "action": "link",
          "href": "//www.erepublik.com/en/citizen/profile/1000001"
        },
        {
          "type": "sendMessage",
          "title": "Send message",
          "enabled": true,
          "action": "link",
          "href": "//www.erepublik.com/en/main/messages-compose/1000001"
        },
        {
          "type": "removeFriend",
          "title": "Remove friend",
          "enabled": true,
          "action": "removeFriend",
          "actionURL": "//www.erepublik.com/en/main/citizen-addRemoveFriend"
        }
      ]
    }
  }
}
```

</details>

On your own profile these were also seen:

| Field | Description |
|-------|-------------|
| `citizen.hasTutorial` | boolean |
| `citizen.hasPendingCitizenshipApplication`, `pendingCitizenshipApplicationPermalink` | Pending citizenship application |
| `penalties` | Not present on the other profile read |
| `partyData` | `id`, `party_id`, `name`, `stripped_title`, `avatar`, `has_avatar`, `created_at`, `economical_orientation`, `is_party_president` |

The client sets `isMyProfile` itself, and shows the block button for others except citizen `2`.

### Errors

Without a session: HTTP `200`, `{"error":"not_authorized"}`.

**Source:** real requests, 2026-11-27; `ProfileFactory.getData`; example — earlier notes.

---

## Hovercard

`GET /en/main/citizen-hovercard/{citizenId}` · Auth required · ✅ Verified 2026-11-27

The compact card shown when the pointer is over a profile link with a `hovercard` attribute. The client caches it per citizen for the page's lifetime.

```bash
curl 'https://www.erepublik.com/en/main/citizen-hovercard/2' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened:

```json
{
  "name": "Plato",
  "level": 1,
  "isOnline": false,
  "isFriend": false,
  "isSelf": false,
  "politicalTitle": "Official eRepublik account",
  "interactionButtons": [
    { "type": "addFriend", "title": "Add friend", "enabled": true, "action": "addFriend",
      "actionURL": "//www.erepublik.com/en/main/citizen-addRemoveFriend" }
  ]
}
```

<details>
<summary>Full example response (earlier notes, own card; synthetic values)</summary>

```json
{
  "name": "PlayerName",
  "avatar": "https://cdnt.erepublik.net/.../HASH.png?...",
  "level": 100,
  "bornOn": "Jan 01, 2010",
  "isDead": false,
  "isBanned": false,
  "isOnline": true,
  "numberOfFriends": 100,
  "isFriend": false,
  "isSelf": true,
  "citizenship": {
    "id": 1,
    "name": "CountryName",
    "permalink": "CountryName"
  },
  "politicalTitle": "",
  "isOrg": false,
  "isAdult": true,
  "badges": [
    {
      "type": "militaryRank",
      "name": "RankName",
      "icon": "//www.erepublik.net/images/modules/ranks/tanks/64px/RANK_ICON.png"
    },
    {
      "type": "aviationRank",
      "name": "AviationRankName",
      "icon": "//www.erepublik.net/images/modules/ranks/aircraft/64px/AVIATION_RANK_ICON.png"
    }
  ],
  "activity": [
    {
      "type": "residenceCity",
      "id": 100,
      "name": "CityName",
      "avatar": "//www.erepublik.net/images/modules/cities/128px/CityName.png",
      "title": "Resident",
      "permalink": "//www.erepublik.com/en/main/city/CityName"
    },
    {
      "type": "politicalParty",
      "id": 1001,
      "name": "PartyName",
      "avatar": "//cdnt.erepublik.net/.../HASH.jpg",
      "title": "Party Member",
      "economicalOrientation": "EconomicOrientation",
      "socialOrientation": "SocialOrientation",
      "permalink": "//www.erepublik.com/en/party/1001"
    },
    {
      "type": "militaryUnit",
      "id": 101,
      "name": "UnitName",
      "avatar": "//cdnt.erepublik.net/.../HASH.png?...",
      "title": "Soldier",
      "permalink": "//www.erepublik.com/en/military/military-unit/101"
    }
  ],
  "fighterInfo": {
    "military": {
      "rank": 50,
      "strength": 1000,
      "division": 4
    },
    "aviation": {
      "rank": 30,
      "perception": 0,
      "division": 11
    }
  },
  "interactionButtons": []
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Citizen display name |
| `avatar` | string | Avatar image URL |
| `level` | number | Experience level |
| `bornOn` | string | Registration date (e.g., "Jan 01, 2010") |
| `isDead` | boolean | Whether the citizen account is dead |
| `isBanned` | boolean | Whether the citizen is banned |
| `isOnline` | boolean | Whether the citizen is currently online |
| `numberOfFriends` | number | Total friend count |
| `isFriend` | boolean | Whether the authenticated citizen is friends with this citizen |
| `isSelf` | boolean | Whether this is the authenticated citizen's own profile |
| `citizenship` | object | Citizenship info: `id`, `name`, `permalink` |
| `politicalTitle` | string | Current political role (e.g., "Congress Member", "President") |
| `isOrg` | boolean | Whether this is an organization account (not a player) |
| `isAdult` | boolean | Whether the account meets age requirements |
| `badges` | array | Military and aviation rank badges |
| `badges[].type` | string | Badge type: `"militaryRank"` or `"aviationRank"` |
| `badges[].name` | string | Full rank name |
| `badges[].icon` | string | Rank icon image URL |
| `activity` | array | Active memberships (residence, party, military unit) |
| `activity[].type` | string | `"residenceCity"`, `"politicalParty"`, or `"militaryUnit"` |
| `activity[].id` | number | Entity ID |
| `activity[].name` | string | Entity name |
| `activity[].title` | string | Role title (e.g., "Resident", "Party Member", "Soldier") |
| `activity[].permalink` | string | Full URL to the entity page |
| `activity[].economicalOrientation` | string | Party economic orientation (party only) |
| `activity[].socialOrientation` | string | Party social orientation (party only) |
| `fighterInfo` | object | Military and aviation combat stats |
| `fighterInfo.military` | object | Ground combat: `rank`, `strength`, `division` |
| `fighterInfo.aviation` | object | Air combat: `rank`, `perception`, `division` |
| `interactionButtons` | array | Available interaction buttons -- empty when `isSelf: true`; populated when viewing another citizen |
| `interactionButtons[].type` | string | Button type: `"viewProfile"`, `"sendMessage"`, `"addFriend"`, `"removeFriend"`, etc. |
| `interactionButtons[].title` | string | Button label (e.g., "View profile", "Send message", "Remove friend") |
| `interactionButtons[].enabled` | boolean | Whether the button is actionable |
| `interactionButtons[].action` | string | Action kind: `"link"` (navigate to `href`) or an action name like `"removeFriend"` (POST to `actionURL`) |
| `interactionButtons[].href` | string | Target URL for `action: "link"` buttons |
| `interactionButtons[].actionURL` | string | Endpoint URL for non-link actions (e.g., add/remove friend) |

`badges[].type` can also be `ambassador` or `moderator`. For `isOnline` and the profile data's `citizen.onlineStatus`, see [Open questions](#open-questions).

<details>
<summary>interactionButtons when viewing a friend (earlier notes)</summary>

```json
{
"interactionButtons": [
  {
    "type": "viewProfile",
    "title": "View profile",
    "enabled": true,
    "action": "link",
    "href": "//www.erepublik.com/en/citizen/profile/1000001"
  },
  {
    "type": "sendMessage",
    "title": "Send message",
    "enabled": true,
    "action": "link",
    "href": "//www.erepublik.com/en/main/messages-compose/1000001"
  },
  {
    "type": "removeFriend",
    "title": "Remove friend",
    "enabled": true,
    "action": "removeFriend",
    "actionURL": "//www.erepublik.com/en/main/citizen-addRemoveFriend"
  }
]
}
```

</details>

- `interactionButtons` is empty on your own card and on [organizations](organizations.md#organization-profiles); otherwise `viewProfile`, `sendMessage` and `addFriend` or `removeFriend`.
- `activity` omits memberships the citizen does not have.
- Clicking a friend button posts to its `actionURL`: [add or remove a friend](friends.md#add-or-remove-a-friend).

### Errors

All with HTTP `200`.

| Response | Cause |
|----------|-------|
| `{"error":true,"message":"citizen error"}` | Invalid ID or system account |
| `{"error":"not_authorized"}` | No session |

**Source:** real requests, 2026-11-27; `HovercardFactory`, `hovercard` directives (version 1795779904); examples — earlier notes.

---

## Switch strength

`GET /en/main/switch-strength?action=offer&counterpart_id={citizenId}` · Auth required · 🔍 From code

> [!WARNING]
> A `GET` that sends another citizen an offer to switch strength.

An April 1st event action. The button shows only while `events.april1st2017.isActive` is `true` (it was `false` on 2026-11-27) and is enabled by `events.april1st2017.canOfferSwitch`.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `action` | query | string | Yes | `offer`; no other value found |
| `counterpart_id` | query | number | Yes | Citizen to invite |

The client shows `msg` and, without `error`, disables the button.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `switchStrength`; not requested.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Global variant with a cookie | Adds `isFriend`, `isFriendRequestPending` data | Keys present; `isLoggedIn` stays `false` | Live response |
| Online status | The hovercard's `isOnline` is not available from the profile JSON endpoints | The profile data example has `citizen.onlineStatus`; that both mean the same was not checked | Not settled |
| Base variant | Alias of `-global` | Same key set | Both |
| Personal variant without a session | "Returns error" | `not_authorized` | Live response |
| Revival sponsor button | Not described | `requestToBeRevivalSponsor` in `ProfileFactory` | [Dormant event code](../events/dormant-code.md#request-to-be-a-sponsor) |
