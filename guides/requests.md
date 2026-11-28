# Making requests

[API docs](../README.md) › Guides › Making requests

What every endpoint page assumes. Pages only mention what differs from this.

## Base URL

`https://www.erepublik.com`

Paths in these docs include the language segment `en`, for example `/en/military/battlefield/{battleId}`. The game's client builds it from `erepublik.settings.culture`.

## Session cookie

Authentication is cookie-based: send the session token as `erpk`.

```http
Cookie: erpk=YOUR_SESSION_TOKEN
```

How to log in: [Authentication](../basics/login.md). A request may also need a Cloudflare `cf_clearance` cookie and a browser `User-Agent`.

Never publish a real cookie or token.

## CSRF token

Most `POST` endpoints require a `_token` form field. On a logged-in game page it is available as the JavaScript global `csrfToken` or `SERVER_DATA.csrfToken`.

## Common headers

| Header | Value | When |
|--------|-------|------|
| `Cookie` | `erpk=YOUR_SESSION_TOKEN` | Authenticated requests |
| `X-Requested-With` | `XMLHttpRequest` | AJAX endpoints; always on `POST` |
| `Content-Type` | `application/x-www-form-urlencoded` | Form `POST` requests |

## Status labels

| Label | Meaning |
|-------|---------|
| ✅ Verified | Confirmed with a real request; the date is given |
| 🔍 From code | Derived from the game's JavaScript; not confirmed with a request |
| ❔ Unconfirmed | Origin unknown |

A "From code" endpoint may include response examples carried over from earlier notes. Its **Source** line says which parts the code actually shows.
