# eRepublik API - Authentication

#erepublik #api #authentication

[< Back to Table of Contents](API_TOC.md)

---

## Overview

eRepublik uses cookie-based session authentication. After a successful login, the server returns a session cookie (`erpk`) that must be included in all subsequent authenticated requests.

---

## Login Flow

1. **POST** credentials to the login endpoint
2. **Receive** session cookie in response
3. **Include** cookie in all authenticated requests

---

## Login

**Method:** POST
**URL:** `/en/login`
**Auth Required:** No

### Description

Authenticates a user and establishes a session. On success, returns a 302 redirect to the homepage with session cookies. The client must handle the redirect and store the cookies for subsequent authenticated requests.

### Parameters (Form Data)

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token (obtain from login page HTML) |
| `citizen_email` | string | Yes | User's email address (URL-encoded) |
| `citizen_password` | string | Yes | User's password |
| `remember` | string | No | `on` to persist session with remember-me cookie |

### Required Cookies (Input)

| Cookie | Description |
|--------|-------------|
| `erpk_mid` | Machine/browser identifier |
| `cf_clearance` | Cloudflare clearance token (from browser challenge) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| User-Agent | Browser user agent | Yes |
| Origin | `https://www.erepublik.com` | Yes |
| Referer | `https://www.erepublik.com/en` | Yes |

### Example Request (cURL)

```bash
curl -X POST "https://www.erepublik.com/en/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" \
  -H "Origin: https://www.erepublik.com" \
  -H "Referer: https://www.erepublik.com/en" \
  -b "erpk_mid=YOUR_MACHINE_ID; cf_clearance=YOUR_CF_TOKEN" \
  -d "_token=YOUR_CSRF_TOKEN" \
  -d "citizen_email=your%40email.com" \
  -d "citizen_password=yourpassword" \
  -d "remember=on" \
  -c cookies.txt
```

### Example Response

On success, the response includes:
- HTTP 302 redirect to homepage
- Multiple `Set-Cookie` headers establishing the session

```
HTTP/2 302
Location: https://www.erepublik.com/en
Set-Cookie: erpk=abc123xyz...; expires=...; path=/; domain=.erepublik.com; secure; HttpOnly; SameSite=None
Set-Cookie: erpk_auth=1; path=/; domain=.erepublik.com; secure; SameSite=None
Set-Cookie: erpk_rm=def456...; expires=...; path=/; domain=.erepublik.com; secure; HttpOnly; SameSite=None
```

### Response Cookies

| Cookie | Description |
|--------|-------------|
| `erpk` | Primary session token (HttpOnly) |
| `erpk_auth` | Authentication flag (`1` = logged in) |
| `erpk_rm` | Remember-me token for persistent sessions (HttpOnly) |

### Notes

- The `_token` CSRF parameter must be extracted from the login page HTML before submitting
- The `erpk` cookie is HttpOnly and cannot be accessed via JavaScript
- Session duration depends on the `remember` parameter
- When `remember=on`, the `erpk_rm` cookie enables session restoration
- Failed login attempts may trigger CAPTCHA or temporary blocks
- The `cf_clearance` cookie requires passing Cloudflare's browser challenge first
- Consider rate limiting your requests to avoid being flagged

---

## Using the Session

Once authenticated, include the `erpk` cookie in all requests:

```bash
curl -X GET "https://www.erepublik.com/en/some-endpoint" \
  -H "Cookie: erpk=abc123xyz..." \
  -H "X-Requested-With: XMLHttpRequest"
```

---

## Session Validation

To check if a session is still valid, you can:
1. Request any authenticated endpoint
2. Check if the response contains user data or redirects to login

---

## Logout

**Method:** GET
**URL:** `/en/logout`
**Auth Required:** Yes

### Description

Terminates the current user session. The server responds with a 302 redirect to the homepage and sets the `erpk` cookie to expire shortly (15 minutes), effectively invalidating the session.

### Parameters

None

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| User-Agent | Browser user agent | Recommended |

### Example Request (cURL)

```bash
curl -X GET "https://www.erepublik.com/en/logout" \
  -H "Cookie: erpk=YOUR_SESSION_TOKEN" \
  -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"
```

### Example Response

```
HTTP/2 302
Location: https://www.erepublik.com/en
Set-Cookie: erpk=YOUR_SESSION_TOKEN; expires=Sun, 25 Jan 2026 16:45:56 GMT; Max-Age=900; path=/; domain=.erepublik.com; secure; HttpOnly; SameSite=None
```

### Notes

- Returns HTTP 302 redirect to the homepage (`/en`)
- The session cookie is set to expire in 15 minutes (Max-Age=900)
- No JSON response - this is a browser navigation endpoint
- After logout, any subsequent requests with the old session cookie will be treated as unauthenticated

---

## Security Considerations

- Never store credentials in plain text
- Use environment variables for sensitive data
- Implement proper error handling for expired sessions
- Respect rate limits and terms of service

---

## Related

- [API Table of Contents](API_TOC.md)
- [Military API](API_MILITARY.md)
- [Social API](API_SOCIAL.md)
- [Economy API](API_ECONOMY.md)
