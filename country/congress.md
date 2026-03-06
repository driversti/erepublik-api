# Congressional Actions - Propose Donation

#erepublik #api #country #congress #donation

[< Back to Country Overview](README.md)

---

Submits a congressional law proposal to donate currency from the country's treasury to a specified organization or citizen. This action creates a proposal that other Congress members can vote on. The URL pattern differs from the `/en/country/` page endpoints -- it uses `/{countryName}/` directly.

**Method:** POST
**URL:** `/en/{countryName}/new-donation`
**Auth Required:** Yes (must be a Congress member)

## Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country name (e.g., "Lithuania", "USA") |

## Request Parameters (POST body)

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| requirments | number | Yes | Unknown flag (observed value: `1`). Note: the misspelling "requirments" is in the original API |
| currency | number | Yes | Currency type: `1` = local currency (e.g., LTL), possibly `0` = Gold |
| type_name | string | Yes | Name of the recipient organization/citizen (URL-encoded) |
| value | number | Yes | Amount of currency to donate |
| debate | string | No | Optional debate/discussion text for the proposal |
| commit | string | Yes | Action trigger, value: `Propose` |

## Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Referer | `https://www.erepublik.com/en/{countryName}/new-donation` | Recommended |
| Origin | `https://www.erepublik.com` | Recommended |

## Example Request (cURL)

<details>
<summary>cURL example</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/Lithuania/new-donation' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Origin: https://www.erepublik.com' \
  -H 'Referer: https://www.erepublik.com/en/Lithuania/new-donation' \
  --data-raw '_token=YOUR_CSRF_TOKEN&requirments=1&currency=1&type_name=Lietuvos+Taupomasis+Bankas&value=400000&debate=&commit=Propose'
```

</details>

## Example Response

This endpoint returns an **HTML redirect** (HTTP 302), not JSON:
- **On success:** Redirects to the country laws/proposals page where the new proposal appears
- **On failure (not authenticated):** Redirects to homepage (`/en`)

## Notes

- **Congress members only** - this action requires the authenticated user to be a Congress member of the specified country
- **CSRF token required** - must be obtained from the GET version of the same page (`/en/{countryName}/new-donation`)
- **Unauthenticated access** returns HTTP 302 redirect to `/en` (homepage)
- The `type_name` field is the **display name** of the recipient (organization or citizen), not an ID
- The `requirments` field has a **typo in the original API** (missing 'e') - must be sent as-is
- The `currency` field likely maps to: `1` = local currency, other values may represent Gold
- This is a **form submission endpoint** (not AJAX) - it follows the traditional POST-redirect-GET pattern
- The GET version of the same URL (`/en/{countryName}/new-donation`) shows the donation proposal form
- URL pattern `/{countryName}/new-donation` is different from other country pages which use `/country/{tab}/{countryName}`
- Similar congressional action endpoints likely exist for other proposal types (e.g., natural enemy, MPP, embargo, tax changes)

---

## See Also

- [Country Administration Page](administration.md) -- law proposals list and status
- [Country Economy Page](economy.md) -- treasury data and direct donation form
- [Country Politics Page](politics.md) -- Congress composition
