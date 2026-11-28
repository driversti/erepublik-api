# Private messages

[API docs](../README.md) › Social › Private messages

The inbox and the compose screen. These screens were deliberately not opened, so that no message changes its read state; everything here comes from links and scripts of other pages.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Inbox](#inbox) | GET | `/en/main/messages-inbox` | 🔍 From code |
| [Compose](#compose) | GET | `/en/main/messages-compose/{citizenId}` | 🔍 From code |
| [First message popup](#first-message-popup) | GET | `/en/main/first-message-popup` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Inbox

`GET /en/main/messages-inbox` · Auth required · 🔍 From code

> [!WARNING]
> A private screen. Opening messages may mark them as read.

The envelope icon in the page header (classes `messages nmail`) links here.

**Source:** header link on game pages, 2026-11-27; not requested.

---

## Compose

`GET /en/main/messages-compose/{citizenId}` · Auth required · 🔍 From code

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `citizenId` | path | number | Yes | Recipient. The "New Message" shortcut uses `0` |

Linked from profiles, [hovercards](profiles.md#hovercard) (`sendMessage` button), your [friend list](friends.md#friend-list-page) and the `shift+N` keyboard shortcut ([dashboard data](../home/dashboard.md)). Organization profiles link it too.

The request that sends a message was not found in the scripts searched. The shared CAPTCHA callbacks in `erepublik.js` re-enable an element `#sendMessageButton`, which suggests that sending can require a [CAPTCHA](../basics/captcha.md) (not confirmed).

**Source:** page links, 2026-11-27; `erepublik.js` (version 1795779896), shortcuts and CAPTCHA callbacks; not requested.

---

## First message popup

`GET /en/main/first-message-popup` · Auth required · 🔍 From code

The popup `firstMessage` of the client's popup system. It is drawn with a darker overlay than other popups (opacity `0.95`, default `0.3`). Earlier notes call it an onboarding popup for a new player's first message.

```bash
curl 'https://www.erepublik.com/en/main/first-message-popup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

**Source:** `erepublik.js` (version 1795779896), `renderPopup`; purpose — earlier notes; not requested.

---

## Open questions

| Topic | What is unknown |
|-------|-----------------|
| Sending, reading, deleting | Paths and fields; they would live in scripts of the message screens |
| Unread counter | Which data request supplies the header counter |
| First message popup | What triggers it |
