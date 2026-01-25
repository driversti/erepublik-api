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

Authenticates a user and establishes a session. Returns a session cookie on success.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `citizen_email` | string | Yes | User's email address |
| `citizen_password` | string | Yes | User's password |
| `remember` | string | No | `on` to persist session |

### Headers

| Header | Value | Description |
|--------|-------|-------------|
| Content-Type | `application/x-www-form-urlencoded` | Form data encoding |
| User-Agent | Browser user agent | Required to avoid blocks |

### Example Request (cURL)

```bash
curl -X POST "https://www.erepublik.com/en/login" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" \
  -d "citizen_email=your@email.com" \
  -d "citizen_password=yourpassword" \
  -d "remember=on" \
  -c cookies.txt
```

### Example Response

On success, the response includes:
- HTTP 302 redirect to homepage
- `Set-Cookie` header with `erpk` session token

```
HTTP/1.1 302 Found
Set-Cookie: erpk=abc123xyz...; expires=...; path=/; domain=.erepublik.com; HttpOnly
Location: https://www.erepublik.com/en
```

### Notes

- The `erpk` cookie is HttpOnly and cannot be accessed via JavaScript
- Session duration depends on the `remember` parameter
- Failed login attempts may trigger CAPTCHA or temporary blocks
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
