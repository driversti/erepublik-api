# eRepublik API - Social

#erepublik #api #social #citizens

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Social endpoints cover citizen profiles, friends, messages, notifications, and community interactions.

---

## Endpoints

### Get Citizen Profile (Global JSON)

**Method:** GET
**URL:** `/en/main/citizen-profile-json-global/{citizenId}`
**Auth Required:** No (public endpoint, but works better with session cookie)

#### Description

Retrieves comprehensive profile data for a citizen including personal info, location, achievements, military stats, friends list, decorations, and more. This is the main endpoint used by the citizen profile page to load all data.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| citizenId | number | Yes | The unique citizen ID |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| X-Requested-With | `XMLHttpRequest` | Yes |
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No (enhances response with friendship data) |

#### Example Request (cURL)

```bash
curl -X GET "https://www.erepublik.com/en/main/citizen-profile-json-global/4690052" \
  -H "X-Requested-With: XMLHttpRequest" \
  -H "Cookie: erpk=YOUR_SESSION_TOKEN"
```

#### Example Response

```json
{
  "citizen": {
    "id": 4690052,
    "name": "driver sti",
    "created_at": "2011-04-12",
    "is_organization": false,
    "is_alive": true,
    "has_avatar": true,
    "avatar_version": "fa70b874e2f337186b979af9404828eb",
    "avatar_file": "png",
    "level": 1690,
    "avatar": "https://cdnt.erepublik.net/.../avatar.png",
    "profile_url": "//www.erepublik.com/en/citizen/profile/4690052",
    "onlineStatus": true,
    "banStatus": {
      "type": false,
      "reason": false
    },
    "nextLevelXp": 8330000
  },
  "citizenAttributes": {
    "citizen_id": 4690052,
    "experience_points": 8328880,
    "level": 1690
  },
  "isAdult": true,
  "aboutMe": "Profile description with HTML...",
  "location": {
    "citizenLocationInfo": {
      "citizen_id": 4690052,
      "citizenship_country_id": 72,
      "residence_country_id": 72,
      "residence_region_id": 663
    },
    "residenceCountry": {
      "id": 72,
      "name": "Lithuania",
      "permalink": "Lithuania",
      "empire": 0
    },
    "citizenshipCountry": {
      "id": 72,
      "name": "Lithuania",
      "permalink": "Lithuania",
      "empire": 0
    },
    "residenceRegion": {
      "id": 663,
      "name": "Lithuania Minor",
      "current_owner_country_id": 72,
      "original_owner_country_id": 72,
      "permalink": "Lithuania-Minor"
    }
  },
  "city": {
    "residenceCityId": 710,
    "residenceRoleName": "Resident",
    "residenceCity": {
      "id": 710,
      "name": "Klaipeda",
      "permalink": "Klaipeda",
      "region_id": 663,
      "city_type_id": 2,
      "country_id": 72
    }
  },
  "achievements": [
    {
      "name": "Freedom Fighter",
      "img": "achievement_freedomfighter",
      "descriptionBefore": "Liberate occupied regions...",
      "descriptionAfter": "Liberate occupied regions...",
      "count": 132
    }
  ],
  "isAmbassador": false,
  "isTopPlayer": true,
  "isPresident": false,
  "isCongressman": true,
  "isPartyPresident": false,
  "isPartyMember": true,
  "isDictator": false,
  "isModerator": false,
  "isBanned": false,
  "friends": {
    "number": 5293,
    "list": [
      {
        "id": 4630767,
        "name": "Sercom",
        "is_alive": 1,
        "avatar": "https://cdnt.erepublik.net/.../avatar.png"
      }
    ],
    "isFriend": false,
    "isFriendRequestPending": false
  },
  "military": {
    "militaryData": {
      "strength": 412825.469,
      "temporaryStrength": 412825.469,
      "divisionData": {
        "division": 4,
        "smallBombDamage": 1500000,
        "bazookaBoosterDamage": 40000,
        "battleHeroReward": 5
      },
      "name": "Legends of Ukraine: Laurentiu L Battalion XX",
      "stars": 19,
      "points": 210000000000,
      "rankNumber": 89,
      "icon": "//www.erepublik.net/images/modules/ranks/tanks/64px/070_legendary.png",
      "ground": { "...": "ground combat stats" },
      "aircraft": {
        "name": "Air Commodore ****",
        "stars": 4,
        "points": 55043706,
        "rankNumber": 60
      }
    },
    "militaryUnit": {
      "id": 893,
      "name": "Lithuanian Shadows",
      "country_id": 72,
      "member_count": 45
    },
    "bestDamageData": {
      "damage": 240418879999,
      "battle_id": 837316,
      "side_id": 72,
      "against_country": "Latvia",
      "region": "Samogitia",
      "won": true
    },
    "truePatriot": {
      "hasTruePatriotProgress": true,
      "progress": 75.31,
      "currentLevel": 4955100000000,
      "damage": 4955175310691,
      "citizenship_id": 72
    }
  },
  "nukes": {
    "bombs": 1782,
    "nukes": 11358,
    "last": {
      "for": { "id": 72, "name": "Lithuania" },
      "against": { "id": 53, "name": "Portugal" },
      "day": "6,640",
      "type": "bomb"
    }
  },
  "decorations": [
    {
      "class": "summerGames2024_SkyHero_Bronze",
      "icon": "summerGames2024_SkyHero_Bronze.png",
      "text": "Air Champion - Bronze Medal...",
      "amount": 1
    }
  ],
  "activePacks": {
    "combat_stash": { "...": "active enhancements" },
    "division_switch_pack": { "...": "pack details" }
  },
  "newspaper": {
    "id": 256891,
    "name": "Know the facts",
    "stripped_title": "know-the-facts"
  },
  "partyData": {
    "id": 3773,
    "name": "Lietuvos Tevynes Sajunga",
    "is_party_president": 0
  },
  "nationalRank": {
    "xp": 14
  },
  "pvpStats": {
    "matches_played": 10,
    "matches_won": 0,
    "matches_lost": 10
  },
  "isLoggedIn": false,
  "isBlocked": false,
  "isFallen": false,
  "activePacksAmount": 3
}
```

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| citizen | object | Basic citizen info (id, name, level, avatar, online status, ban status) |
| citizenAttributes | object | XP and level data |
| isAdult | boolean | Whether citizen is an adult player |
| aboutMe | string | Profile description (HTML encoded) |
| location | object | Citizenship and residence location data |
| city | object | City residence information |
| achievements | array | List of achievements with counts |
| isAmbassador/isTopPlayer/isPresident/etc | boolean | Various role flags |
| friends | object | Friend count and sample list |
| military | object | Military stats, rank, MU, true patriot progress |
| nukes | object | Bomb/nuke usage count and last deployment |
| decorations | array | Earned decorations and medals |
| activePacks | object | Active enhancement packs |
| newspaper | object | Owned newspaper info (if any) |
| partyData | object | Political party membership |
| nationalRank | object | National ranking position |
| pvpStats | object | PvP match statistics |

#### Notes

- This is a **public endpoint** - no authentication required, but having a session cookie adds `isFriend` and `isFriendRequestPending` data
- The `friends.list` array contains only a sample of friends (first ~9), not the complete list
- `aboutMe` content is HTML-encoded and may contain `<br />` tags
- Military rank names and icons are included for both ground and aircraft combat
- `decorations` can have `amount > 1` indicating multiple instances of the same decoration
- The response is quite large (~50KB) due to the comprehensive data included

---

### Get Citizen Profile (Personal JSON)

**Method:** GET
**URL:** `/en/main/citizen-profile-json-personal/{citizenId}`
**Auth Required:** Yes

#### Description

Retrieves extended profile data for a citizen, including hovercard interaction data and event status. This endpoint requires authentication and returns additional data not available in the public global endpoint.

> **Note:** All fields from the [Global JSON endpoint](#get-citizen-profile-global-json) are also present. This documentation covers only the **additional/different fields** unique to the personal endpoint.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| citizenId | number | Yes | The unique citizen ID |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| X-Requested-With | `XMLHttpRequest` | Yes |
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |

#### Example Request (cURL)

```bash
curl -X GET "https://www.erepublik.com/en/main/citizen-profile-json-personal/4416327" \
  -H "X-Requested-With: XMLHttpRequest" \
  -H "Cookie: erpk=YOUR_SESSION_TOKEN"
```

#### Additional Response Fields (not in Global endpoint)

##### `loggedIn.hovercardData`

Rich hovercard data for UI rendering. In the global endpoint, `loggedIn` is an empty array `[]`.

```json
{
  "loggedIn": {
    "hovercardData": {
      "name": "Artem Kudryavtsev",
      "avatar": "https://cdnt.erepublik.net/.../avatar.jpg",
      "level": 114,
      "born_on": "Feb 05, 2011",
      "is_dead": false,
      "is_banned": false,
      "is_online": false,
      "number_of_friends": 268,
      "is_friend": true,
      "is_self": false,
      "citizenship": {
        "id": 40,
        "name": "Ukraine",
        "permalink": "Ukraine"
      },
      "politicalTitle": "",
      "is_org": false,
      "is_adult": true,
      "badges": [
        {
          "type": "militaryRank",
          "name": "Titan ",
          "icon": "//www.erepublik.net/images/modules/ranks/tanks/64px/066_titan.png"
        },
        {
          "type": "aviationRank",
          "name": "Wing Commander *****",
          "icon": "//www.erepublik.net/images/modules/ranks/aircraft/64px/049_wing_commander.png"
        }
      ],
      "activity": [
        {
          "type": "residenceCity",
          "id": 299,
          "name": "Donetsk",
          "avatar": "//www.erepublik.net/images/modules/cities/128px/Donetsk.png",
          "title": "Resident",
          "permalink": "//www.erepublik.com/en/main/city/Donetsk"
        },
        {
          "type": "politicalParty",
          "id": 3650,
          "name": "Republican Union",
          "avatar": "//cdnt.erepublik.net/.../party.jpg",
          "title": "Party Member",
          "economical_orientation": "Far-right",
          "social_orientation": "Authoritarian",
          "permalink": "//www.erepublik.com/en/party/3650"
        },
        {
          "type": "militaryUnit",
          "id": 239,
          "name": "_UArmy_",
          "avatar": "//cdnt.erepublik.net/.../mu.jpg",
          "title": "Soldier",
          "permalink": "//www.erepublik.com/en/military/military-unit/239"
        }
      ],
      "fighterInfo": {
        "military": {
          "rank": 66,
          "strength": 71295.477,
          "division": 4
        },
        "aviation": {
          "rank": 49,
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
          "href": "//www.erepublik.com/en/citizen/profile/4416327"
        },
        {
          "type": "sendMessage",
          "title": "Send message",
          "enabled": true,
          "action": "link",
          "href": "//www.erepublik.com/en/main/messages-compose/4416327"
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

##### `events`

Event status flags. In the global endpoint, `events` is an empty array `[]`.

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

#### Hovercard Data Fields

| Field | Type | Description |
|-------|------|-------------|
| born_on | string | Formatted registration date (e.g., "Feb 05, 2011") |
| is_friend | boolean | Whether the logged-in user is friends with this citizen |
| is_self | boolean | Whether viewing own profile |
| politicalTitle | string | Current political title (empty if none) |
| badges | array | Military and aviation rank badges with icons |
| activity | array | Current affiliations (city, party, military unit) with details |
| fighterInfo | object | Compact fighter stats for military and aviation |
| interactionButtons | array | Available UI actions (viewProfile, sendMessage, addFriend/removeFriend) |

#### Activity Types

| Type | Description |
|------|-------------|
| residenceCity | City where citizen resides |
| politicalParty | Party membership with orientation info |
| militaryUnit | Military unit membership |

#### Interaction Button Types

| Type | Action | Description |
|------|--------|-------------|
| viewProfile | link | Link to full profile page |
| sendMessage | link | Link to compose message |
| addFriend | addFriend | AJAX action to add friend |
| removeFriend | removeFriend | AJAX action to remove friend |

#### Notes

- Requires authentication - returns error without valid session cookie
- `interactionButtons` change based on relationship (addFriend vs removeFriend)
- `politicalTitle` includes roles like "Country President", "Congressman", etc.
- `activity[].social_orientation` only appears for politicalParty type
- Events object keys may vary depending on currently active game events

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

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/citizen-addRemoveFriend/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw '_token=YOUR_CSRF_TOKEN&citizenId=2886612&action=addFriend'
```

### Example Response (Success)

```json
{
  "status": true,
  "message": "Friend request sent successfully"
}
```

### Example Response (Error - Pending Request)

```json
{
  "message": "ghoghnooos already has a friendship request pending from you.",
  "status": false,
  "error": true
}
```

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

### Example Request (cURL)

```bash
curl -X POST 'https://www.erepublik.com/en/main/wall-post/create/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'post_as=0&post_message=Hello%20world&_token=YOUR_CSRF_TOKEN'
```

### Example Response (Success)

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
      "message": "Here is a more sophisticated post. <q class=\"emoji emoji_1f601\" alt=\"&#x1f601;\" draggable=\"false\">&#x1f601;</q><br />\nІ він містить кирилицю",
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

### Example Response (Error - Rate Limited)

```json
{
  "error": true,
  "message": "You must wait 4 minutes before posting again"
}
```

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
| reactions | object | Reaction counts by emoji unicode (e.g., "1f44d" = 👍) |
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

### Example Request (cURL)

```bash
curl -X POST 'https://www.erepublik.com/en/main/wall-post/delete/json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'postId=12345678&_token=YOUR_CSRF_TOKEN'
```

### Example Response (Success)

```json
{
  "status": true,
  "message": "Post deleted successfully"
}
```

### Example Response (Error - Post Not Found)

```json
{
  "error": true,
  "message": "This post is not available anymore"
}
```

### Example Response (Error - Not Authorized)

```json
{
  "error": true,
  "message": "You are not authorized to delete this post"
}
```

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

## Template

Use this template when documenting new endpoints:

```markdown
## Endpoint Name

**Method:** GET/POST
**URL:** `/en/path/to/endpoint`
**Auth Required:** Yes/No

### Description

Brief explanation of what this endpoint does.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| param1 | string | Yes | Description |

### Headers

| Header | Value | Description |
|--------|-------|-------------|
| Content-Type | application/json | ... |

### Example Request (cURL)

\`\`\`bash
curl -X GET "https://www.erepublik.com/en/..." \
  -H "Cookie: erpk=..."
\`\`\`

### Example Response

\`\`\`json
{
  "success": true,
  "data": {}
}
\`\`\`

### Notes

Any additional information, gotchas, or observations.
```

---

## Related

- [API Table of Contents](API_TOC.md)
- [Authentication](API_AUTH.md)
- [Country API](API_COUNTRY.md)
- [Notifications API](API_NOTIFICATIONS.md)
- [Military API](API_MILITARY.md)
- [Economy API](API_ECONOMY.md)
