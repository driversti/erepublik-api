# Notifications and alerts

[API docs](../README.md) › Social › Notifications and alerts

Two systems: the **Notifications** screen (alerts and reports, header bell) and the **side notifications** that pop up on game pages. None of these requests was made for this page, so that nothing is marked as read.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Notifications screen](#notifications-screen) | GET | `/en/main/notifications` | 🔍 From code |
| [List alerts](#list-alerts) | GET | `/en/main/notificationsAjax/{category}/{page}` | 🔍 From code |
| [Delete alerts](#delete-alerts) | POST | `/en/main/notificationsAjax/{category}/{page}` | 🔍 From code |
| [Answer an alert](#answer-an-alert) | POST | `/en/main/alertActions` | 🔍 From code |
| [Side notifications](#side-notifications) | GET | `/en/main/citizenNotifications` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Notifications screen

`GET /en/main/notifications` · Auth required · 🔍 From code

> [!WARNING]
> A private screen; it loads and selects the first alert, which may affect read states.

The page sets `SERVER_DATA.notifications.category`, then loads [the list](#list-alerts). Switching tabs changes the address to `/en/main/notifications/{category}`. The header bell (classes `notifications nalert`) links here and shows its counter from a `data-unread-counter` attribute.

**Source:** header link; `erpk_angular_controllers.js` (version 1795779904), `ErpkNotificationsController`.

---

## List alerts

`GET /en/main/notificationsAjax/{category}/{page}` · Auth required · 🔍 From code

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `category` | path | string | Yes | Tab; `report` is the one known from earlier notes |
| `page` | path | number | Yes | 1-based |

```bash
curl 'https://www.erepublik.com/en/main/notificationsAjax/report/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

### Response

From earlier notes, shortened:

```json
{
  "page": 1,
  "maxPages": 10,
  "totalAlerts": 100,
  "alertsList": [
    { "id": 100000001, "isRead": 1, "identifierType": "holding_report", "identifierId": 1000001, "title": "Production Report", "body": "<div>…</div>" }
  ]
}
```

<details>
<summary>Full example response (earlier notes; IDs, dates and counts replaced)</summary>

```json
{
  "page": 1,
  "maxPages": 10,
  "totalAlerts": 100,
  "alertsList": [
    {
      "id": 100000001,
      "isRead": 1,
      "createdAt": "2000-01-01 12:00:00",
      "identifierType": "holding_report",
      "identifierId": 1000001,
      "title": "Production Report",
      "body": "<div class=\"rowWrapper headingRow\">...</div>",
      "timeAgo": "16 minutes ago",
      "isWebActionable": false,
      "actionsExpired": false,
      "actions": null,
      "alertTypeId": 2563,
      "isCustomizableReport": true
    },
    {
      "id": 100000002,
      "isRead": 1,
      "createdAt": "2000-01-01 08:00:00",
      "identifierType": "deployment_report",
      "identifierId": 1000002,
      "title": "Deployment Report",
      "body": "<div class=\"rowWrapper headingRow\">Deployment finished</div>...",
      "timeAgo": "5 hours ago",
      "isWebActionable": false,
      "actionsExpired": false,
      "actions": null,
      "alertTypeId": 2542,
      "isCustomizableReport": true
    }
  ]
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `page`, `maxPages` | number | Pagination; the client reads both |
| `totalAlerts` | number | All alerts |
| `alertsList[]` | array | One page (typically 10) |
| `alertsList[].id` | number | Alert ID, used to [delete](#delete-alerts) or [answer](#answer-an-alert) |
| `alertsList[].isRead` | number | `1` read, `0` unread |
| `alertsList[].createdAt`, `timeAgo` | string | `YYYY-MM-DD HH:MM:SS` and relative time |
| `alertsList[].identifierType` | string | Kind, see below |
| `alertsList[].identifierId` | number | Related entity |
| `alertsList[].title` | string | May contain special characters |
| `alertsList[].body` | string | HTML, with images and links |
| `alertsList[].isWebActionable`, `actionsExpired` | boolean | Whether it has actions, and whether they expired |
| `alertsList[].actions` | object \| null | Action data |
| `alertsList[].alertTypeId` | number | For example `2563` production, `2542` deployment |
| `alertsList[].isCustomizableReport` | boolean | Report type can be customized |

| `identifierType` | Meaning | Popup the client opens |
|------------------|---------|------------------------|
| `holding_report` | Daily production report | `holdingProductionLogPopup`, with the ID as `deploymentId` |
| `deployment_report` | Deployment finished | `deployReportPopup`, `deploymentId` |
| `battle_report` | Battle participation (earlier notes) | None in code |
| `political_report` | Political activity (earlier notes) | None in code |

The client selects an alert and counts it as read on screen without sending a request of its own.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `ErpkNotificationsController`, `ErpkNotificationsFactory`; response — earlier notes.

---

## Delete alerts

`POST /en/main/notificationsAjax/{category}/{page}` · Auth required · 🔍 From code

> [!WARNING]
> Deletes alerts.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `deleteAlerts[]` | number[] | One of the two | IDs to delete |
| `deleteAllReadAlerts` | number | One of the two | `1` deletes every read alert |

```bash
curl 'https://www.erepublik.com/en/main/notificationsAjax/report/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode '_token=YOUR_CSRF_TOKEN' \
  --data-urlencode 'deleteAlerts[]=100000001'
```

The client expects the refreshed list back: `alertsList`, `page`, `maxPages`.

**Source:** same script, `deleteAlerts`, `deleteOnce`.

---

## Answer an alert

`POST /en/main/alertActions` · Auth required · 🔍 From code

> [!WARNING]
> Performs the alert's action; what it changes depends on the alert.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `alertId` | number | Yes | `alertsList[].id` |
| `action` | string | Yes | Action offered by the alert |

```bash
curl 'https://www.erepublik.com/en/main/alertActions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&alertId=100000001&action=ACTION'
```

The client shows `message` and reloads the list.

**Source:** same script, `webAction`.

---

## Side notifications

`GET /en/main/citizenNotifications` · Auth required · 🔍 From code

Loaded by game pages for the pop-up notification widget (`SideNotificationController`).

```bash
curl 'https://www.erepublik.com/en/main/citizenNotifications' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Fields the client reads:

| Field | Description |
|-------|-------------|
| `notifications[]` | Notifications |
| `notifications[].id` | Sent when a [global alert is closed](../basics/framework.md#close-a-global-alert) |
| `notifications[].type` | See below; also selects the icon |
| `notifications[].title`, `body`, `cssClass` | Display |
| `notifications[].link` | Page to open |
| `notifications[].hasReward` | Opens a reward popup instead |
| `notifications[].details` | `achievementType`, `achievementAmount`, `achievement.ccValue`, `country_permalink`, `icon`, by type |
| `missions[]` | Appended to the list; type `mission` has `img` |

| `type` | Click does |
|--------|------------|
| `jackpot`, `empire`, `ongoingRw`, `partyAlerts`, `citizenAchievement`, `boxKeyReceived` | Closed on the server with [close a global alert](../basics/framework.md#close-a-global-alert); `citizenAchievement` normally opens the achievement popup first |
| `citizenAnniversaryReward`, `dailyTasks`, `dailyOrder`, `leagueOfAllies` | With `hasReward`: the matching reward popup |
| `mission` | Opens the mission |
| `awards` | Removed from the widget |

For `achievementTypeTruePatriot` with an amount of 2 or more, the client appends `(xN)` to the title and multiplies `ccValue`.

The response is cached in `localStorage` until the time given by an `erpk-expires`, `erpk-maxage` or `expires` response header; the key holds `SideNotification`, the citizen ID and the date.

Closing any other type only records it in `localStorage` (`seenNotifications`) for the current game day.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `SideNotificationController`, `SideNotificationFactory`; `erpk_angular_services.js`, `LocalCacheService`.

---

## Open questions

| Topic | Earlier notes | Game code | Page follows |
|-------|---------------|-----------|--------------|
| Categories | Only `report` | Category is a variable | Both |
| Page size | Typically 10 | Not in code | Earlier notes |
| Deployment report popup path | `/en/military/fightDeploy-deployReportPopup` | Popup `deployReportPopup` | — |
| When an alert becomes read on the server | Not described | No separate request | Not settled |
