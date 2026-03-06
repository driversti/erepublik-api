# VIP Points

Endpoint for claiming daily VIP points from the VIP Shop.

#erepublik #api #rewards #vip

[< Back to Rewards](README.md) | [< Back to Table of Contents](../API_TOC.md)

---

## Claim Daily VIP Points

**Method:** POST
**URL:** `/en/main/vip-claim`
**Auth Required:** Yes

### Description

Claims the daily VIP points reward available in the VIP Shop. This is a free daily claim tied to VIP membership. Can only be claimed once per eRepublik day, resetting at 00:00 PST.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Referer | `https://www.erepublik.com/en/main/vip-shop` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/vip-claim' \
  -H 'Cookie: erpk=<YOUR_ERPK_TOKEN>' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Referer: https://www.erepublik.com/en/main/vip-shop' \
  --data-raw '_token=<YOUR_CSRF_TOKEN>'
```

</details>

<details>
<summary>Example Response (Success)</summary>

```json
{
  "error": false,
  "success": true
}
```

</details>

<details>
<summary>Example Response (Already Claimed)</summary>

```json
{
  "error": true,
  "message": "Already claimed"
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| error | boolean | `false` on success, `true` on failure |
| success | boolean | `true` when the claim was successfully processed (only present on success) |
| message | string | Error message when `error` is `true` (e.g., "Already claimed") |

### Notes

- Can only be claimed once per eRepublik day; resets at 00:00 PST (America/Los_Angeles)
- The `Referer` header pointing to the VIP Shop page is recommended -- the game client always sends it
- Source discovered from ePlus userscript project (`client/src/plugins/free/claimVip.ts`)
- VIP Shop items (for context on available rewards) can be fetched via `/en/main/shopItemsJson`

---

## Related

- [Daily Objectives](daily-objectives.md)
- [Daily Missions](daily-missions.md)
- [Weekly Challenges](weekly-challenges.md)
