# Login and logout

[API docs](../README.md) › Basics › Login and logout

How a session starts and ends. The API has no tokens of its own: after login the server sets cookies, and every later request sends them.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Log in](#log-in) | POST | `/en/login` | ❔ Unconfirmed |
| [Log out](#log-out) | GET | `/en/logout` | 🔍 From code |

Headers for authenticated requests: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Session cookies

| Cookie | Set by | Flags in earlier notes | Meaning |
|--------|--------|------------------------|---------|
| `erpk` | Login | `HttpOnly`, `Secure`, `SameSite=None` | Session token |
| `erpk_auth` | Login | `Secure`, `SameSite=None` | `1` = logged in |
| `erpk_rm` | Login with `remember` | `HttpOnly`, `Secure`, `SameSite=None` | Remember-me token that restores a session |
| `erpk_mid` | Before login | — | Machine or browser identifier |
| `cf_clearance` | Cloudflare | — | Result of the Cloudflare browser challenge |

All are set for `.erepublik.com`. A session lasts about 24 hours; with `remember=on` the `erpk_rm` cookie can restore it.

> [!WARNING]
> `erpk` and `erpk_rm` give full access to the account. Keep them and your password out of source code, logs and bug reports.

---

## Log in

`POST /en/login` · Auth not required · ❔ Unconfirmed

> [!WARNING]
> Starts a session on the account. Failed attempts may trigger a CAPTCHA or a temporary block.

### Request

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token taken from the login page HTML |
| `citizen_email` | string | Yes | Account email |
| `citizen_password` | string | Yes | Account password |
| `remember` | string | No | `on` to get the remember-me cookie |

| Header | Value |
|--------|-------|
| `Content-Type` | `application/x-www-form-urlencoded` |
| `User-Agent` | A browser user agent |
| `Origin` | `https://www.erepublik.com` |
| `Referer` | `https://www.erepublik.com/en` |

Input cookies: `erpk_mid` and `cf_clearance`. Passing the Cloudflare challenge in a browser comes first.

```bash
curl 'https://www.erepublik.com/en/login' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'User-Agent: YOUR_BROWSER_USER_AGENT' \
  -H 'Origin: https://www.erepublik.com' \
  -H 'Referer: https://www.erepublik.com/en' \
  -b 'erpk_mid=YOUR_MACHINE_ID; cf_clearance=YOUR_CF_TOKEN' \
  --data-urlencode '_token=YOUR_CSRF_TOKEN' \
  --data-urlencode 'citizen_email=YOUR_EMAIL' \
  --data-urlencode 'citizen_password=YOUR_PASSWORD' \
  --data-urlencode 'remember=on' \
  -c cookies.txt
```

### Response

A `302` redirect to the home page that sets the session cookies. Follow it and keep the cookies.

<details>
<summary>Response headers</summary>

```
HTTP/2 302
Location: https://www.erepublik.com/en
Set-Cookie: erpk=YOUR_SESSION_TOKEN; expires=...; path=/; domain=.erepublik.com; secure; HttpOnly; SameSite=None
Set-Cookie: erpk_auth=1; path=/; domain=.erepublik.com; secure; SameSite=None
Set-Cookie: erpk_rm=YOUR_REMEMBER_TOKEN; expires=...; path=/; domain=.erepublik.com; secure; HttpOnly; SameSite=None
```

</details>

**Source:** earlier notes. The request was not sent and no call was found in the scripts that were read.

---

## Log out

`GET /en/logout` · Auth required · 🔍 From code

> [!WARNING]
> Ends your session.

No parameters. This is a page navigation, not a JSON endpoint.

```bash
curl 'https://www.erepublik.com/en/logout' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'User-Agent: YOUR_BROWSER_USER_AGENT'
```

### Response

Earlier notes: a `302` redirect to `/en` that shortens the `erpk` cookie to 15 minutes. After logout, requests with the old cookie are treated as unauthenticated.

<details>
<summary>Response headers</summary>

```
HTTP/2 302
Location: https://www.erepublik.com/en
Set-Cookie: erpk=YOUR_SESSION_TOKEN; expires=Sun, 25 Jan 2026 16:45:56 GMT; Max-Age=900; path=/; domain=.erepublik.com; secure; HttpOnly; SameSite=None
```

</details>

**Source:** the header link on game pages, seen 2026-11-27 (not followed); response from earlier notes.

---

## Checking a session

Request an authenticated page and check whether it returns your data or redirects to login. On `/en`, real values in [`erepublik.citizen`](framework.md#erepublikcitizen) prove an authenticated session; the mere presence of an `erpk` cookie does not.

The server may also interrupt a valid session with a [verification challenge](captcha.md).

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Login request | Described as a form `POST` | Not checked: no login was performed | Earlier notes |
