# Newspapers and writing

[API docs](../README.md) › Media › Newspapers and writing

A citizen's newspaper: its page, subscriptions, the article editor and the newspaper settings. These are server-rendered HTML pages with forms, plus a few AJAX calls. Reading a single article is in [Articles](articles.md).

#erepublik #api #media #newspapers

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [My newspaper](#my-newspaper) | GET | `/en/main/my-newspaper` | ✅ Verified 2026-11-27 |
| [Newspaper page](#newspaper-page) | GET | `/en/newspaper/{slug}-{newspaperId}/{page}` | ✅ Verified 2026-11-27 |
| [Subscribe or unsubscribe](#subscribe-or-unsubscribe) | POST | `/en/main/newspaper-subscribe` | 🔍 From code |
| [Create a newspaper](#create-a-newspaper) | GET | `/en/create-newspaper` | ❔ Unconfirmed |
| [Edit newspaper form](#edit-newspaper) | GET | `/en/edit-newspaper/` | ✅ Verified 2026-11-27 |
| [Save newspaper settings](#edit-newspaper) | POST | `/en/edit-newspaper/` | 🔍 From code |
| [Change the logo](#change-the-logo) | POST | `/en/main/newspaper-update` | 🔍 From code |
| [Article editor](#write-an-article) | GET | `/en/main/write-article` | ✅ Verified 2026-11-27 |
| [Publish an article](#write-an-article) | POST | `/en/main/write-article` | 🔍 From code |
| [Preview BBCode](#preview-bbcode) | POST | `/en/main/preview-bbcode` | 🔍 From code |
| [Edit an article](#edit-an-article) | GET | `/en/main/edit-article/{articleId}` | 🔍 From code |
| [Delete an article](#delete-an-article) | GET | `/en/main/delete-article/{slug}-{articleId}/1` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## My newspaper

`GET /en/main/my-newspaper` · Auth required · ✅ Verified 2026-11-27

For an owner it redirects to `/en/main/newspaper/{slug}-{newspaperId}/1`, which then shows **Write article** and **Edit newspaper details**. Without a session it redirects to `/en`. The result for a citizen without a newspaper was not seen.

```bash
curl -L 'https://www.erepublik.com/en/main/my-newspaper' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real requests, 2026-11-27.

---

## Newspaper page

`GET /en/newspaper/{slug}-{newspaperId}/{page}` · Auth optional · ✅ Verified 2026-11-27

The newspaper header and its articles, newest first, five per page in the newspaper checked.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `newspaperId` | path | number | Yes | Newspaper ID |
| `slug` | path | string | No | Wrong or missing slugs redirect to the right one |
| `page` | path | number | No | Starts at `1`; past the last page returns 404 |

```bash
curl -L 'https://www.erepublik.com/en/newspaper/YOUR_NEWSPAPER_ID/1'
```

The final URL is `/en/main/newspaper/{slug}-{newspaperId}/{page}`.

| In the HTML | Meaning |
|-------------|---------|
| `.newspaper_head` | Logo, country, owner, rank and wiki links |
| `#newspaper_id` (hidden input) | Newspaper ID |
| `SERVER_DATA.newspaperData` | `citizenId`, `newspaperName` (slug), `numberOfArticles` |
| `em.subscribers` | Subscriber count |
| `div.post` | One article: vote count (`.numberOfVotes_{articleId}`), vote button (`data-article_id`), title link, comment count, date, country, [category](news.md#categories), excerpt |

The date is relative (`5 days ago`) for recent articles and `Day 5,389, 10:18` for old ones.

**Source:** real requests, 2026-11-27.

---

## Subscribe or unsubscribe

`POST /en/main/newspaper-subscribe` · Auth required · 🔍 From code

> [!WARNING]
> Changes your subscription list.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `action` | string | Yes | `subscribe` or `unsubscribe` |
| `newspaperId` | number | Yes | Newspaper ID |
| `captcha-response` | string | No | Sent with `subscribe` after a [captcha](../basics/captcha.md) was solved |

```bash
curl 'https://www.erepublik.com/en/main/newspaper-subscribe' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=subscribe&newspaperId=YOUR_NEWSPAPER_ID&_token=YOUR_CSRF_TOKEN'
```

### Response

| Field | Type | Description |
|-------|------|-------------|
| `status` | boolean | Success |
| `message` | string | Error text when `status` is false |
| `showCaptcha` | boolean | Solve the captcha and repeat the request |
| `subscribers` | number | New subscriber count |
| `medalReceived` | boolean | The client logs this as a Media Mogul medal |

To unsubscribe from several newspapers, the [subscriptions list](news.md#subscriptions) sends one request per newspaper.

**Source:** `subscribe.js` (version 1756118596), `media.js` (version 1698060179).

---

## Create a newspaper

`GET /en/create-newspaper` · Auth required · ❔ Unconfirmed

For an account that already owns a newspaper it redirects to that newspaper (seen 2026-11-27). The creation form, its fields and its price were therefore not seen. `/en/main/create-newspaper` returns 404.

**Source:** real requests, 2026-11-27. Where the path comes from was not recorded.

---

## Edit newspaper

`GET /en/edit-newspaper/` and `POST /en/edit-newspaper/` · Auth required · ✅ Form verified 2026-11-27 · submit 🔍 From code

The GET returns the settings form; `/en/edit-newspaper/{newspaperId}` showed the same form for your own ID.

> [!WARNING]
> Submitting changes your newspaper. The form carries a `newspaper_cost` value, so saving may cost money.

Form fields, `multipart/form-data`:

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `newspaper_cost` | number | Yes | Hidden value filled by the server (`1` on the form checked); meaning not confirmed |
| `newspaper_name` | string | Yes | 6–25 characters, according to the form hint |
| `about_me` | string | No | Description, up to 300 characters |
| `commit` | submit | No | Submit button |

**Source:** real GET, 2026-11-27.

---

## Change the logo

`POST /en/main/newspaper-update` · Auth required · 🔍 From code

> [!WARNING]
> Replaces the newspaper logo. The page shows an upload cost line.

A three-step upload. JPG or PNG, up to 5 MB.

**1. Prepare** — `POST /en/main/newspaper-update`

| Name | Type | Description |
|------|------|-------------|
| `_token` | string | CSRF token |
| `action` | string | `prepare-avatar` |
| `params` | string | JSON: `{"name": "logo.png", "type": "image/png", "size": 12345, "partyId": 0}` |

The response's `formData` object holds the upload fields. Errors carry `message`.

**2. Upload** — `multipart/form-data` POST of every `formData` entry plus the file to `https://erpk-static-avatars.s3.amazonaws.com/`. The XML response contains `PostResponse/ETag`.

**3. Apply** — `POST /en/main/newspaper-update`

| Name | Type | Description |
|------|------|-------------|
| `_token` | string | CSRF token |
| `action` | string | `avatar` |
| `params` | string | JSON: `{"etag": "...", "mimetype": "image/png", "partyId": 0}` |

`partyId` is `0` on the newspaper form; the script's naming suggests it is shared with party logos.

**Source:** `/js/newspaper/edit.js` (unversioned, read 2026-11-27); inline script of the edit form.

---

## Write an article

`GET /en/main/write-article` and `POST /en/main/write-article` · Auth required · ✅ Form verified 2026-11-27 · submit 🔍 From code

The editor. Without a session the GET redirects to `/en`.

> [!WARNING]
> Submitting publishes an article. Publishing in some countries **costs Gold**.

Form fields, `multipart/form-data`:

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `article_name` | string | Yes | Title, 1–80 characters |
| `article_body` | string | Yes | Text in [BBCode](#preview-bbcode) |
| `article_location` | number | Yes | [Country ID](../basics/id-tables.md) to publish in |
| `article_category` | number | Yes | `1`–`6`, see [categories](news.md#categories); `0` is the empty choice |
| `commit` | submit | No | Submit button |

```bash
curl 'https://www.erepublik.com/en/main/write-article' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -F '_token=YOUR_CSRF_TOKEN' \
  --form-string 'article_name=Title' \
  --form-string 'article_body=Text' \
  -F 'article_location=35' \
  -F 'article_category=6'
```

### Publishing cost

Each `article_location` option has a `data-publishcost` attribute, in Gold. On the form checked there were 74 options: one at `0`, 58 at `2` and 15 at `10`. The client compares the cost with your Gold, asks for confirmation when it is not zero, and selects your own country by default.

The editor autosaves the draft in the browser's local storage every 20 seconds; nothing is sent to the server.

### Response

Not seen.

**Source:** real GET, 2026-11-27, and the form's inline script.

---

## Preview BBCode

`POST /en/main/preview-bbcode` · Auth required · 🔍 From code

The editor's **Preview** button. The body is a single form field; the client sends no `_token`. The response is inserted into the preview as HTML.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `data` | string | Yes | BBCode text |

```bash
curl 'https://www.erepublik.com/en/main/preview-bbcode' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode 'data=[b]bold[/b]'
```

| Editor button | BBCode |
|---------------|--------|
| Bold, Italic, Underline, Strikethrough | `[b]`, `[i]`, `[u]`, `[s]` |
| Picture | `[img]URL[/img]` |
| Link | `[url=URL]text[/url]` |
| Line | `-----` |
| Email | `[email=ADDRESS]text[/email]` |
| YouTube | `[youtube]VIDEO_ID[/youtube]` |
| Video | `[video]MP4_URL[/video]` |

**Source:** editor settings on the write-article page; `jquery.markitup.js` (version 1712935465), `renderPreview`.

---

## Edit an article

`GET /en/main/edit-article/{articleId}` · Auth required · 🔍 From code

The **Edit** link on the article page, shown when [`canEdit`](articles.md#article-data) is true. Requested for articles that were not editable, it redirected to `/en`; the form itself was not seen.

**Source:** article page template; real requests, 2026-11-27.

---

## Delete an article

`GET /en/main/delete-article/{slug}-{articleId}/1` · Auth required · 🔍 From code

> [!WARNING]
> **A plain GET deletes the article.** Never crawl or prefetch this URL. Only the client asks for confirmation.

The client builds `slug` from the title (lower case, spaces replaced with hyphens) and navigates to the URL. The article page shows the button together with **Edit** (`canEdit`).

**Source:** `ErpkArticleController.deleteArticle` in `erpk_angular_controllers.js` (version 1795779904).

---

## Open questions

| Topic | Question |
|-------|----------|
| Create a newspaper | Form, fields, price and requirements |
| `newspaper_cost` | What the hidden value controls |
| Article submit | Success and error responses |
| Edit an article | Form and submit endpoint |
| Page size | Five articles per page was seen on one newspaper; its last page (7, with `numberOfArticles` 31) showed no articles |
