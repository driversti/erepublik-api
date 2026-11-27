# Military units

[API docs](../README.md) › [Military](../../military/README.md) › Military units

The "My Military Unit" screen: the unit's overview and daily orders, member list, finances and combat orders, management, civil war and invitations. One HTML page hosts all tabs; each tab loads its data as JSON.

#erepublik #api #military

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [My military unit](#my-military-unit) | GET | `/en/main/group-home/military` | ✅ Verified 2026-11-27 |
| [Unit page](#unit-page) | GET | `/en/military/military-unit/{groupId}` | ✅ Verified 2026-11-27 |
| [Unit panels](#unit-panels) | GET | `/en/military/military-unit-data/` | ✅ Verified 2026-11-27 |
| [Finances](#finances) | GET | `/en/military/military-unit-data/{groupId}/finances` | ✅ Verified 2026-11-27 |
| [Combat orders history](#combat-orders-history) | GET | `/en/military/military-unit-data/{groupId}/combat-orders-history/finances` | ✅ Verified 2026-11-27 |
| [Join](#join) | POST | `/en/military/military-unit-apply` | 🔍 From code |
| [Resign](#resign) | POST | `/en/military/military-unit-resign` | 🔍 From code |
| [Manage members](#manage-members) | POST | `/en/military/military-unit-management/` | 🔍 From code |
| [Donate money](#donate-money) | POST | `/en/military/military-unit-donate-money` | 🔍 From code |
| [Combat order actions](#combat-order-actions) | POST | `/en/military/military-unit-combat-orders` | 🔍 From code |

[Officer actions](#officer-actions) (daily orders, unit details, recruitment, invitations, civil war) post to URLs that only the server provides.

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## My military unit

`GET /en/main/group-home/military` · Auth required · ✅ Verified 2026-11-27

Target of the "My Military Unit" menu item. For a member it redirects to `/en/military/military-unit/{groupId}`.

**Source:** real request (member account), 2026-11-27.

---

## Unit page

`GET /en/military/military-unit/{groupId}[/{tab}]` · Auth required · ✅ Verified 2026-11-27

Returns the HTML page. The client rewrites the address to `.../{groupId}/{tab}` when you switch tabs. An unknown ID returns HTTP 404.

The page defines `SERVER_DATA`:

| Field | Description |
|-------|-------------|
| `groupId` | Unit ID |
| `militaryUnitInfo` | [Unit info](#common-fields) plus `currencyName` and `awards[]` |
| `citizenInfo` | [Your role](#common-fields) in this unit |
| `initialTab` | Number of the tab to open |
| `confirmationMessages` | Confirmation texts, keyed by [member action](#manage-members) |
| `avatarEndpoint` | Upload target for the unit avatar |

`awards[]` items: `type`, `title`, `description`, `icon`, `amount`, `awardedOn` (`false` when not awarded).

### Tabs

| No. | Tab (URL part) | Panel request | Client shows it to |
|:---:|----------------|---------------|--------------------|
| 1 | `overview` | [overview](#overview) | everyone |
| 2 | `members` | [members](#members) | everyone |
| 3 | `combat-orders` | [finances](#finances) | members |
| 4 | `daily-orders` | [daily-orders](#officer-panels) | commanding officers |
| 5 | `management` | [management](#officer-panels) | everyone (not limited by role) |
| 6 | `civil-war` | [civil-war](#officer-panels) | commander |
| 7 | `invite-members` | [invite-members](#officer-panels) | commanding officers |
| 8 | `combat-orders-history` | [history](#combat-orders-history) | everyone (not limited by role) |

**Source:** real request and `erpk_angular_controllers.1795779904.js`, `MuController`, 2026-11-27.

---

## Unit panels

`GET /en/military/military-unit-data/?groupId={groupId}&panel={panel}` · Auth required · ✅ Verified 2026-11-27

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `groupId` | number | Yes | Unit ID |
| `panel` | string | Yes | `overview`, `members`, `daily-orders`, `management`, `civil-war`, `invite-members`; `finances` also works here |
| `widget` | string | No | Sent empty by the client for `daily-orders` and `management` |

```bash
curl 'https://www.erepublik.com/en/military/military-unit-data/?groupId=1234&panel=overview' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Common fields

```json
{
  "militaryUnitInfo": {
    "groupId": 1234, "name": "Example Unit", "countryId": 42,
    "leaderId": 1000001, "isElite": 0, "avatar": "//cdnt.erepublik.net/..."
  },
  "citizenInfo": {
    "isLoggedIn": 1, "isMuMember": 1, "isMuCommander": 0, "isMu2ndCommander": 0,
    "isRegimentCaptain": 0, "isMuCommandingOfficer": 0,
    "commandingOfficerRoleId": 0, "isCaptainForRegimentId": 0
  },
  "panelContents": {},
  "panelUrl": "//www.erepublik.com/en/military/military-unit/1234/overview"
}
```

Role IDs: `7` Commander, `6` 2nd Commander, `5` Captain (from code), `4` Soldier.

### Errors

| HTTP | Response | Cause |
|------|----------|-------|
| 200 | `{"status":false,"message":"Invalid Military Unit","error":true}` | Unknown `groupId` |
| 404 | `{"status":false,"error":true,"code":404,"message":"404 Not Found"}` | Unknown `panel` |

### Overview

Shortened `panelContents`:

```json
{
  "columnLeft": {
    "dailyOrders": [
      {
        "title": "Fight for Lithuania in Zaporozhia",
        "battleId": 900000, "regionId": 100, "sideCountryId": 72, "isCivilWar": false,
        "battleLink": "//www.erepublik.com/en/military/battlefield-choose-side/900000/72",
        "progress": 0, "required": 25, "completed": false,
        "setBy": {
          "id": 0, "name": "...", "role": "Military Unit's priorities",
          "description": "Set by Military Unit's priorities"
        }
      }
    ]
  },
  "columnRight": {
    "quickInfo": {
      "motto": "...", "numMembers": 49, "numRegiments": 10, "foundedOn": "Day 1,368",
      "memberInfo": "You are <b>Member</b> in this Military Unit",
      "resignMilitaryUnit": { "action": "resign", "url": "/en/military/military-unit-resign" },
      "resignText": "Are you sure you wish to resign from Example Unit?"
    },
    "commandStructure": [
      {
        "id": 1000001, "name": "ExampleCitizen", "avatar": "...", "role": "Commander", "roleId": 7,
        "isCommander": true, "isSecondCommander": false, "yrc": 0
      }
    ]
  }
}
```

`battleLink` is the [choose side](battlefield/page.md#choose-side-link) link.

For a unit you are **not** a member of, `columnLeft` holds `joinMilitaryUnit` instead of `dailyOrders`:

```json
{
  "canJoin": 1, "isLocked": false, "joinButtonText": "Join",
  "recruitmentType": "open", "recruitmentTypeId": 0,
  "recruitmentDescription": "Any player can join the Military unit as a recruit",
  "requirements": [],
  "joinMilitaryUnit": { "action": "apply", "url": "//www.erepublik.com/en/military/military-unit-apply" }
}
```

The page code reads `requirements.tankRank` and `requirements.aircraftRank` (`rankName`, `rankIcon`, `completed`); not observed.

### Members

`GET /en/military/military-unit-data/?groupId={groupId}&panel=members` plus optional filters, 10 members per page.

| Name | Type | Description |
|------|------|-------------|
| `currentPage` | number | Page, from 1. Past the last page the list is empty |
| `search` | string | Member name search |
| `sortBy` | string | `strength`, `perception`, `tankDamageDone`, `aircraftDamageDone`, `yesterdayKills`, `dailyOrdersCompleted`, `medalsWon`, `tankRankPoints`, `aircraftRankPoints` |
| `sortAsc` | number | `0` descending, `1` ascending |
| `regimentFilter` | string | A key of `filters.regiments`, for example `regiment100` or `all` |
| `weekFilter` | string | `week0` (this week) to `week3` |
| `divisionFilter` | string | `all`, `division1` to `division4` |

Only `sortBy=strength` and the regiment and division filters were checked with real requests; the other values come from the client.

```json
{
  "members": [
    {
      "citizenId": 1000001, "name": "ExampleCitizen", "avatar": "...", "level": 100,
      "roleId": 4, "roleName": "Soldier",
      "regimentId": 100, "regimentNumber": 1, "regimentName": "Regiment 1",
      "citizenshipCountryId": 72, "locationCountryId": 72, "locationRegionId": 100,
      "division": 4, "strength": 0, "strengthGained": 0, "perception": 0, "perceptionGained": 0,
      "tankDamageDone": 0, "tankRankPoints": 0, "tankRankGained": 0, "tankRankNumber": 0,
      "tankRankName": "...", "tankRankIcon": "...",
      "aircraftDamageDone": 0, "aircraftRankPoints": 0, "aircraftRankGained": 0,
      "aircraftRankNumber": 0, "aircraftRankName": "...", "aircraftRankIcon": "...",
      "yesterdayKills": 0, "medalsWon": 0,
      "dailyOrdersCompleted": 0, "allDailyOrdersCompleted": 0,
      "lastFight": 1795000000, "isDead": null
    }
  ],
  "membersList": [1000001],
  "pages": 5,
  "pageParam": "currentPage",
  "searchParam": "search",
  "filters": {
    "regiments": { "all": { "text": "All regiments", "isCurrent": true }, "regiment100": { "text": "Regiment 1", "isCurrent": false } },
    "history": { "week0": { "text": "This week", "isCurrent": true } },
    "divisions": { "all": { "text": "All divisions", "isCurrent": true } }
  }
}
```

| Field | Description |
|-------|-------------|
| `membersList` | IDs of all members (its length matched `numMembers`) |
| `lastFight` | Unix time |

### Officer panels

Checked with a soldier account, so widgets reserved for officers came back as empty arrays. Their fields below come from the page templates.

| Panel | `panelContents` | Returned to a soldier |
|-------|-----------------|-----------------------|
| `daily-orders` | `columnLeft.setDailyOrder`, `columnRight.setBattlePriorities` | both `[]` |
| `management` | `columnLeft.editMuDetails` or `muDetails`; `columnRight.setRecruitmentPolicy` or `recruitmentPolicy` | read-only `muDetails`, `recruitmentPolicy` |
| `civil-war` | `columnLeft.civilWar` | `[]` |
| `invite-members` | `columnLeft.inviteFriends`, `columnRight.inviteCitizens` | both `[]` |

Read-only `management` widgets:

```json
{
  "columnLeft": {
    "muDetails": {
      "MilitaryUnitMotto": "...", "MilitaryUnitDescription": "...",
      "widgetId": "muDetails",
      "widgetUrl": "//www.erepublik.com/en/military/military-unit-data/1234/management/muDetails"
    }
  },
  "columnRight": {
    "recruitmentPolicy": {
      "accessPolicies": [
        { "policyType": 0, "policyText": "Open access", "policyDescription": "..." },
        { "policyType": 1, "policyText": "Strict access", "policyDescription": "..." },
        { "policyType": 2, "policyText": "Invite only access", "policyDescription": "..." }
      ],
      "tankRanks": [ { "rankName": "Private", "rankPoints": 15, "rankIcon": "..." } ],
      "aircraftRanks": [ { "rankName": "Airman 1st Class", "rankPoints": 10, "rankIcon": "..." } ],
      "currentAccessPolicyType": 0, "currentPolicyTankRank": 15, "currentPolicyAircraftRank": 0,
      "widgetId": "recruitmentPolicy", "widgetUrl": "..."
    }
  }
}
```

Fields used by the officer widgets (not observed):

| Widget | Fields |
|--------|--------|
| `setDailyOrder` | `availableDailyOrderBattles[]` (`battleId`, `regionName`, `invaderId`, `invaderName`, `defenderId`, `defenderName`, permalinks, `isCivilWar`, `isMpp`, `isRw`), `setDailyOrderText`, `action`, `actionUrl`, `widgetId` |
| `setBattlePriorities` | `numPriorities`, `currentPriorities[]` (`prioType`, `prioCountry`), `action`, `actionUrl`, `widgetId` |
| `civilWar` | `civilWarText`, `civilWarDescriptionText`, `civilWarActionText`, `civilWarCost`, `civilWarSupporters[]`, `supportTimeRemaining` (seconds), `civilWarStarted`, `civilWarBattleOngoing`, `isSupporter`, `action`, `actionUrl`, `widgetId` |
| `inviteFriends` | `citizenFriends`, `regiments`, `action`, `actionUrl`, `widgetId` |
| `inviteCitizens` | `citizens`, `regiments`, `searchAction`, `actionUrl`, `widgetId` |

**Source:** real requests and `erpk_angular_controllers.1795779904.js`, `MuFactory`, 2026-11-27.

---

## Finances

`GET /en/military/military-unit-data/{groupId}/finances?widget={widget}` · Auth required · ✅ Verified 2026-11-27

The "Combat orders" tab: unit funds and [combat orders](battlefield/boosters-bombs-pvp.md#subscribe-to-combat-order) the unit pays for.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `groupId` | path | number | Yes | Unit ID |
| `widget` | query | string | No | Empty for the whole panel; `activeCombatOrders` or `militaryUnitFunds` returns that widget alone, at the top level |
| `missionsPage` | query | number | No | Page of active orders |
| `missionsSearch` | query | string | No | Search in active orders |

```bash
curl 'https://www.erepublik.com/en/military/military-unit-data/1234/finances?widget=&missionsPage=1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

`panelContents`, as a soldier:

```json
{
  "columnLeft": {
    "militaryUnitFunds": {
      "value": 0, "widgetId": "militaryUnitFunds", "widgetUrl": "...",
      "actionUrl": "//www.erepublik.com/en/military/military-unit-donate-money"
    },
    "setCombatOrders": []
  },
  "columnRight": {
    "activeCombatOrders": {
      "list": [], "pages": 0, "search": false,
      "pageParam": "missionsPage", "searchParam": "missionsSearch",
      "widgetId": "activeCombatOrders", "widgetUrl": "...",
      "actionUrl": "//www.erepublik.com/en/military/military-unit-combat-orders"
    }
  }
}
```

For a unit you are not a member of, `militaryUnitFunds` is `[]`.

Fields of a `list[]` item, from the page templates (not observed): `combatOrderId`, `battleId`, `regionName`, `cityName`, `forCountryId`, `forCountryName`, `againstCountryId`, `againstCountryName`, permalinks, `isCivilWar`, `division`, `reward`, `budget`, `threshold`, `statusCode`. The response may also carry `activeBattles` for the new-order form.

**Source:** real requests and `erpk_angular_controllers.1795779904.js`, `MuFactory.getCombatOrders`, 2026-11-27.

---

## Combat orders history

`GET /en/military/military-unit-data/{groupId}/combat-orders-history/finances?widget={widget}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `groupId` | path | number | Yes | Unit ID |
| `widget` | query | string | No | `combatOrdersHistory`; empty also works |
| `historyPage` | query | number | No | Page, from 1 |
| `historySearch` | query | string | No | Search |

`panelContents.columnRight.combatOrdersHistory`, one item:

```json
{
  "list": [
    {
      "battleId": 955105,
      "battleInfo": {
        "battleId": 955105, "battleType": "mpp", "regionId": 219, "regionName": "Northern Ireland",
        "cityName": "Belfast", "createdAt": 1794691919, "isCivilWar": 0,
        "forCountryId": 40, "forCountryName": "Ukraine", "forCountryPermalink": "Ukraine",
        "againstCountryId": 42, "againstCountryName": "Bulgaria", "againstCountryPermalink": "Bulgaria",
        "total": 8130947
      },
      "divisions": [
        { "division": 11, "soldiers": 162, "total": 8130947 }
      ],
      "soldiers": 162,
      "total": 8130947
    }
  ],
  "pages": 1, "search": false,
  "pageParam": "historyPage", "searchParam": "historySearch",
  "widgetId": "combatOrdersHistory", "widgetUrl": "..."
}
```

`divisions` has an entry for each of 1, 2, 3, 4 and 11.

**Source:** real requests, 2026-11-27.

---

## Actions

All actions are form-encoded POSTs with `groupId` and `_token`. The client treats a response with `status: true` as success and shows `message`. None of them was sent while writing this page.

> [!WARNING]
> Every action below changes your membership or the unit, and some spend money or Gold.

## Join

`POST /en/military/military-unit-apply` · Auth required · 🔍 From code

> [!WARNING]
> Joins a military unit.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `groupId` | number | Yes | Unit ID |
| `action` | string | Yes | `apply` |
| `url` | string | No | The client posts the whole `joinMilitaryUnit` object, so its `url` is sent too |

**Source:** `joinMuAction`.

## Resign

`POST /en/military/military-unit-resign` · Auth required · 🔍 From code

> [!WARNING]
> Leaves the unit.

Parameters: `groupId`, `action=resign` (the client also sends `url`). The response may include `redirectUrl`.

**Source:** `muResignAction`.

## Manage members

`POST /en/military/military-unit-management/` · Auth required · 🔍 From code

> [!WARNING]
> Changes roles or regiments, or removes a member.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `groupId` | number | Yes | Unit ID |
| `action` | string | Yes | `manage-members` |
| `manageAction` | string | Yes | `appointCommander`, `appoint2ndCommander`, `remove2ndCommander`, `appointCaptain`, `removeCaptain`, `move`, `remove` |
| `citizenId` | number | Yes | Target member |
| `regimentId` | string | Yes | A key of `filters.regiments`, such as `regiment100`. For `removeCaptain` the member's own regiment, otherwise the selected one |

The client also sends `url`. Which actions the buttons offer by your role ID:

| Role | Actions |
|------|---------|
| 7 Commander | all |
| 6 2nd Commander | `appointCaptain`, `removeCaptain`, `move`, `remove` |
| 5 Captain | `remove` |

**Source:** `setMuRoles`, `doMuManageActions`.

## Donate money

`POST /en/military/military-unit-donate-money` · Auth required · 🔍 From code

> [!WARNING]
> Transfers your own currency to the unit. Irreversible in the client.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `groupId` | number | Yes | Unit ID |
| `action` | string | Yes | `donate-money` |
| `value` | number | Yes | Amount; the client caps it at your balance |

The client also sends `url` and reads `currencyAmount` (your new balance) from the response.

**Source:** `muDonateToCo`.

## Combat order actions

`POST /en/military/military-unit-combat-orders` · Auth required · 🔍 From code

> [!WARNING]
> Creating or raising an order spends unit funds.

Every call carries `groupId`, `action` and usually `url`.

| `action` | Other parameters |
|----------|------------------|
| `create` | `battleId`, `sideCountryId`, `division` (1–4, 11), `reward`, `budget`, `threshold`, `subscriberCountryId`, `subscriberMilitaryUnitId`, `tax` |
| `edit` | `combatOrderId`, `reward`, `budget`, `tax`, `updateBudget` (`0`/`1`) |
| `pause`, `resume`, `cancel` | `combatOrderId` |
| `search-mu` | `searchMu` (name); returns `searchResult` (`id`, `name`, `muAvatar`). Search only |

- `0` in `subscriberCountryId` or `subscriberMilitaryUnitId` means all countries or all units.
- Client limits: `reward` at least 10, `threshold` (maximum domination) 40–100, `budget` at least 100 and at most the unit funds.
- When editing, the client sets `tax` to 10% of the budget increase, rounded down. A new order form starts with `reward` 10, `threshold` 52, `budget` 100, `tax` 10.
- Responses may include `militaryUnitFunds.value`.

**Source:** `addNewCombatOrder`, `saveCombatOrderUpdate`, `editCombatOrder`, `searchForMilitaryUnits`.

## Officer actions

🔍 From code · the URL is the widget's `actionUrl`, which was not returned to the account used

> [!WARNING]
> These change the unit. Renaming it and starting a civil war may cost resources.

| Feature | `action` | Other parameters |
|---------|----------|------------------|
| Set daily order | from the widget | `battleId`; `sideCountryId` (elite units); `enableFeedAutoPost` `0`/`1` (commander) |
| Battle priorities | from the widget | `widget`, and per priority `n` from 0: `prioType_n` (`1` fight for, `2` against), `prioCountry_n` |
| Rename | from the widget | `updateAction=changeName`, `name`, `panel=management`. The response may include the new Gold amount |
| Motto | from the widget | `updateAction=updateMotto`, `motto`, `panel=management` |
| Description | from the widget | `updateAction=updateDescription`, `description`, `panel=management` |
| Avatar | from the widget | `updateAction=prepareAvatar` with `name`, `type`, `size` returns `S3Settings`; upload to `SERVER_DATA.avatarEndpoint`; then `updateAction=updateAvatar` with `etag`, `mimetype`. JPEG or PNG, up to 5 MB |
| Recruitment policy | from the widget | `policyType`, `tankRankPoints`, `aircraftRankPoints` |
| Find a citizen to invite | `searchAction` | `citizenId` (the search box text); returns `data` |
| Invite | from `inviteFriends` | `widget`, `citizensToInvite[]`, `regimentId`; returns `inviteResult` (`message`, `type`) |
| Start or support civil war | from the widget | `widget` |

All also send `groupId` and `url`.

**Source:** `erpk_angular_controllers.1795779904.js`, `MuController` and the `dropzone` directive.

---

## Open questions

| Topic | Earlier notes | Game code | Page follows |
|-------|---------------|-----------|--------------|
| `group-home/military` without a unit | none | not covered | Unverified: only the member redirect is described |
| Creating a unit | none | no call found in the scripts or on the unit page | Not described |
| Officer widget responses and URLs | none | read from `actionUrl` | Fields from templates only |
| `yrc` in `commandStructure` | none | meaning unknown | Listed as is |
