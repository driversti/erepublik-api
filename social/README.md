# Social API

#erepublik #api #social #citizens

Social endpoints cover citizen profiles, friends, messages, notifications, and community interactions.

[< Back to Table of Contents](../API_TOC.md)

---

## Endpoints

| File | Endpoints | Description |
|------|-----------|-------------|
| [profiles.md](profiles.md) | Get Citizen Profile (Global JSON), Get Citizen Profile (Personal JSON), Get Citizen Hovercard | Citizen profile data and hovercard tooltips |
| [friends.md](friends.md) | Add/Remove Friend | Friendship management |
| [wall.md](wall.md) | Create Wall Post, Delete Wall Post | Wall post creation and deletion |
| [search.md](search.md) | Search Citizens | Citizen search by name |
| [news.md](news.md) | News RSS Feed | RSS feed of player-written news articles |

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

- [API Table of Contents](../API_TOC.md)
- [Authentication](../API_AUTH.md)
- [Country API](../API_COUNTRY.md)
- [Notifications API](../API_NOTIFICATIONS.md)
- [Military API](../API_MILITARY.md)
- [Economy API](../API_ECONOMY.md)
