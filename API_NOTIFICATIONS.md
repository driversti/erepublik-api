# eRepublik API - Notifications

#erepublik #api #notifications

[< Back to Table of Contents](API_TOC.md)

---

## Overview

Notification endpoints handle in-game alerts, reports, and notifications for players. This includes production reports, deployment reports, battle results, political updates, and other game events.

---

## Endpoints

### Get Notifications (Paginated)

**Method:** GET
**URL:** `/en/main/notificationsAjax/report/{page}`
**Auth Required:** Yes

#### Description

Retrieves paginated notifications/alerts for the authenticated user. Returns various notification types including production reports, deployment reports, battle results, and other in-game events. Each notification includes HTML-formatted content for display.

#### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| page | number | Yes | Page number (1-indexed) for pagination |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

#### Example Request

```bash
curl -X GET 'https://www.erepublik.com/en/main/notificationsAjax/report/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

#### Example Response

```json
{
  "page": 1,
  "maxPages": 115,
  "totalAlerts": 1144,
  "alertsList": [
    {
      "id": 868239527,
      "isRead": 1,
      "createdAt": "2026-01-25 06:25:31",
      "identifierType": "holding_report",
      "identifierId": 21928031,
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
      "id": 868213384,
      "isRead": 1,
      "createdAt": "2026-01-25 01:18:01",
      "identifierType": "deployment_report",
      "identifierId": 122111562,
      "title": "🛩️ Deployment Report",
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

#### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| page | number | Current page number |
| maxPages | number | Total number of pages available |
| totalAlerts | number | Total count of all notifications |
| alertsList | array | Array of notification objects for current page |

#### Alert Object Fields

| Field | Type | Description |
|-------|------|-------------|
| id | number | Unique notification ID |
| isRead | number | Read status (1 = read, 0 = unread) |
| createdAt | string | Timestamp of notification creation (format: YYYY-MM-DD HH:MM:SS) |
| identifierType | string | Type of notification (e.g., `holding_report`, `deployment_report`) |
| identifierId | number | ID referencing the related entity (holding, deployment, etc.) |
| title | string | Display title for the notification |
| body | string | HTML-formatted notification content |
| timeAgo | string | Human-readable relative time (e.g., "16 minutes ago") |
| isWebActionable | boolean | Whether notification has interactive web actions |
| actionsExpired | boolean | Whether associated actions have expired |
| actions | object/null | Action data if available, null otherwise |
| alertTypeId | number | Numeric identifier for alert type |
| isCustomizableReport | boolean | Whether this report type can be customized |

#### Common Notification Types

| identifierType | Description |
|----------------|-------------|
| `holding_report` | Daily production report from companies/holdings |
| `deployment_report` | Military deployment completion report |
| `battle_report` | Battle participation or completion report |
| `political_report` | Political activity notifications |

#### Notes

- Requires authentication - endpoint will fail without valid session cookie
- Pagination starts at page 1 (not 0)
- The `body` field contains HTML content ready for rendering in the UI
- `alertTypeId` values vary by notification type (e.g., 2563 for production, 2542 for deployment)
- The API typically shows 10 notifications per page
- `isRead` uses numeric boolean (1/0) instead of true/false
- HTML content in `body` may include inline images, links, and formatted data
- Some notifications include emojis in the `title` field (e.g., 🛩️, 🪖)

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
- [Social API](API_SOCIAL.md)
- [Military API](API_MILITARY.md)
- [Economy API](API_ECONOMY.md)
