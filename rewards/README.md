# Rewards

Rewards endpoints cover daily objectives, missions, achievement rewards, weekly challenges, and VIP bonus systems. These endpoints track player progress toward daily goals and provide information about claimable rewards.

#erepublik #api #rewards #objectives #missions

[< Back to Table of Contents](../API_TOC.md)

---

## Endpoints

| File | Endpoints | Description |
|------|-----------|-------------|
| [daily-objectives.md](daily-objectives.md) | `objective-status`, `objective-claim-reward` | Daily objective progress and tier reward claiming |
| [daily-missions.md](daily-missions.md) | `daily-missions-data`, `mission-solve` | Daily mission list, progress, and reward claiming |
| [weekly-challenges.md](weekly-challenges.md) | `weekly-challenge-data`, `weekly-challenge-collect-reward` | Weekly challenge progress and reward collection |
| [vip.md](vip.md) | `vip-claim` | Daily VIP points claim |

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
- [Authentication](../auth/README.md)
- [Power Spin](../powerspin/README.md)
