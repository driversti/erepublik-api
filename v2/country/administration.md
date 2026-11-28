# Government administration

[API docs](../README.md) › Country and politics › Government administration

The **Administration** tab of a country (military priorities, organizations, law proposals) and the related government screens: citizenship applications and Congress membership. Most buttons work only for the president or Congress.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Administration page](#administration-page) | GET | `/en/country-administration/{country}/{page}` | ✅ Verified 2026-11-27 |
| [Campaign of the Day](#campaign-of-the-day) | POST | `/en/mark-battle` | 🔍 From code |
| [Citizenship applications](#citizenship-applications) | GET | `/en/country/{country}/citizenship/applications/{page}` | ✅ Verified 2026-11-27 |
| [Approved applications](#approved-applications) | GET | `/en/country/{country}/citizenship/applications/approved/{page}` | ✅ Verified 2026-11-27 |
| [Accept an application](#accept-an-application) | POST | URL from the page | 🔍 From code |
| [Cancel an application](#cancel-an-application) | GET | URL from the page | 🔍 From code |
| [Resign from Congress](#resign-from-congress) | POST | `/en/{country}/resign-congress` | ❔ Unconfirmed |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Path rules and page header: [Society](society.md#common-to-all-country-pages).

Related: [Congress proposals and laws](laws.md) · [Allied forces popup](military.md#allied-forces-popup)

---

## Administration page

`GET /en/country-administration/{country}/{page}` · No auth (earlier notes) · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `country` | path | string | Yes | Permalink |
| `page` | path | number | Yes | Law list page, from `1`; required even for the first page |

```bash
curl 'https://www.erepublik.com/en/country-administration/Poland/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

Earlier notes: readable without a session, with the same reduced view.

### Access message

`.infoholder`: `You are not a president or a congress member in this country.` Earlier notes expect extra controls for those roles; they have not been seen.

### Military priorities

```html
<div class="holder" id="militaryPriorities">
  <h2 class="section">Military Priorities</h2>
  <a class="std_global_btn smallSize orangeColor mppPriorities disabled" id="mppPriorityTrigger"><span>Deploy allied forces</span></a>
</div>
<div class="holder" id="manageOrganizations">
  <h2 class="section">Manage Organizations</h2>
  <a class="std_global_btn smallSize orangeColor mppPriorities disabled" id="manageOrganizationsTrigger"><span>Manage Organizations</span></a>
</div>
```

| Button | `disabled` when | Opens |
|--------|-----------------|-------|
| `#mppPriorityTrigger` | Viewer is not the president | [Allied forces popup](military.md#allied-forces-popup) |
| `#manageOrganizationsTrigger` | Viewer is not the president | [Manage organizations popup](../social/organizations.md#manage-organizations-popup) |

### Law proposals

`table.laws.lawDescription.largepadded`, with `ul.pager` below.

```html
<tr>
  <td><a href="//www.erepublik.com/en/main/law/Poland/344511" class="dotted taxStyle Donate">Donate <span class="lawIco"></span></a></td>
  <td>Day 6,946, 12:48 <br><small>(21 hours ago)</small></td>
  <td><img src="//www.erepublik.net/images/parts/icon-law_empty.gif" alt="Pending" class="icon">Pending</td>
  <td><a href="//www.erepublik.com/en/main/law/Poland/344511" class="std_global_btn tinySize blueColor"><span>Details</span></a></td>
</tr>
```

| Data | Selector |
|------|----------|
| Law type | Class after `taxStyle` on `a.dotted` (see [law types](laws.md#law-types)) |
| Law ID | Last segment of the link to the [law page](laws.md#law-page) |
| Date | Second cell |
| Status | `img.icon[alt]` or cell text |

| Status | Icon |
|--------|------|
| `Pending` | `icon-law_empty.gif` |
| `Accepted` | `icon-law_valid.gif` |
| `Rejected` | `icon-law_invalid.gif` |

Earlier notes: other statuses may exist (for example Expired), with their own icons.

Law IDs are one sequence for all countries. The first row's cells carry `class="first"` (styling only).

### Inline variables and scripts

| Variable | Value |
|----------|-------|
| `SERVER_culture` | `"en"` |
| `SERVER_token` | CSRF token |
| `txt_no_bod` | `No Campaign of the Day is currently set.` |
| `txt_set_success` | `Successfully set as Campaign of the Day` |
| `txt_unset_success` | `This campaign is no longer Campaign of the Day` |
| `COUNTRY_ADMINISTRATION_TEXTS` | `{set_text: 'Set', unset_text: 'Unset'}` |

Scripts: `battle_of_td.js`, `countryPage.js`, `countrySelector.js`.

**Source:** real request, 2026-11-27.

---

## Campaign of the Day

`POST /en/mark-battle` · Auth required · 🔍 From code

> [!WARNING]
> Sets or removes the country's Campaign of the Day.

Sent by `.mark-battle` buttons, which were not present for a citizen without the role.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | `SERVER_token` |
| `id` | number | Yes | `data-battle-id` of the button |
| `remove` | number | Yes | `1` to unset (button has class `unset`), else `0` |

The client reads `success` and `battleOfTheDay` (boolean: now set) from the JSON response.

**Source:** `battle_of_td.js` (version 1698060179).

---

## Citizenship applications

`GET /en/country/{country}/citizenship/applications/{page}` · Auth required · ✅ Verified 2026-11-27

Pending requests for citizenship, linked from the [Society](society.md#society-page) tab. Tabs: **Pending** and **Approved**.

```bash
curl 'https://www.erepublik.com/en/country/Poland/citizenship/applications/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

One `tbody.status-Pending` per request in `table.c_approved`:

```html
<tr class="citizenship-application citizenship-application-pending citizenshipApplication{applicationId}">
  <td rowspan="2" class="ca_citizen"><a href="/en/citizen/profile/{citizenId}">PlayerName</a></td>
  <td class="ca_reason"><p class="teaser">Applicant's message</p></td>
</tr>
<tr class="citizenshipApplication{applicationId}">
  <td colspan="2" class="ca_info">
    <a href="/en/country/society/{currentCountry}">Current country</a>
    <a class="report citizenshiprequest report-trigger" href="//www.erepublik.com/en/main/tickets-report/{applicationId}/citizenship-application">Report request</a>
  </td>
</tr>
```

A citizen without the role sees no accept button.

**Source:** real request, 2026-11-27.

---

## Approved applications

`GET /en/country/{country}/citizenship/applications/approved/{page}` · Auth required · ✅ Verified 2026-11-27

Table rows `tr.c_app` with columns **Citizen**, **Approved on** (`29 July 2009`) and **Approved by**. The first page showed 2009 entries.

**Source:** real request, 2026-11-27.

---

## Accept an application

`POST {data-accept-url}` · Auth required · 🔍 From code

> [!WARNING]
> Grants citizenship.

The URL is taken from `a.acceptApplication[data-accept-url]` in `td.ca_accept`; `data-application-id` names the row. The body is only `_token`. The client expects JSON with a truthy `status` and decrements a counter in `.c_stat strong`.

**Source:** `citizenship/listPending.js`.

---

## Cancel an application

`GET {href}` · Auth required · 🔍 From code

> [!WARNING]
> This `GET` changes state: it cancels a citizenship application after a browser confirmation. Do not follow the link while crawling.

The URL is the `href` of `a.ca_cancel`. The response body is ignored.

**Source:** `citizenship/listPending.js`.

---

## Resign from Congress

`POST /en/{country}/resign-congress` · Auth required (Congress member) · ❔ Unconfirmed

> [!WARNING]
> Gives up a seat in Congress.

Only the path is known. Its parameters were not seen; party resignation, a similar form, sends [`_token`](parties.md#resign-from-a-party).

**Source:** path list of the 2026-11-27 survey; not found again in the pages or scripts read for this document.

---

## Open questions

| Topic | Notes |
|-------|-------|
| Role-only controls | President and Congress views were not seen; proposing laws from them is not documented |
| `/en/country/resourceDraft` | Not an endpoint: it returns `404`. `country/resourceDraft` is a `countrySelector.urlTab` value that redirects to [`/en/main/resource-wars/{country}/1`](../military/wars.md) |
| Rejected status | Seen on 2026-11-27; earlier notes listed the icon only |
