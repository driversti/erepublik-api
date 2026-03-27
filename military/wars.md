# Wars

#erepublik #api #military #wars #resistance

[< Back to Military Module](README.md)

Endpoints and research related to war support and battle timing.

**Related:** [campaigns.md](campaigns.md) | [battle-info.md](battle-info.md) | [battlefield.md](battlefield.md)

---

## War Page

**Method:** GET
**URL:** `/en/wars/show/{warId}`
**Auth Required:** Yes (authenticated view includes citizen data; unauthenticated behavior unknown)

### Description

Displays the full war page between two countries. Shows the war header (countries, allies, start date, status), a warning message about the next expected attack, and a chronological list of all battle rounds with their outcomes (region contested, winner, battlefield link).

### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `warId` | number | Yes | War ID (e.g., `219946`) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/wars/show/219946' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

</details>

### Response (HTML)

The page is rendered HTML (not JSON). Key data is embedded in the DOM structure:

#### War Header (`.war_list_header`)

| Element | Description |
|---------|-------------|
| `.country.left_side h3` | Defender country name |
| `.country.left_side img` | Defender flag (`/images/flags_png/L/{Country}.png`) |
| `#defender_allies` | Defender allies list (or "no allies") |
| `.country.right_side h3` | Attacker country name |
| `.show_allies .allies_tooltip li` | Attacker allies (flag + name per `<li>`) |
| `.vs small` | War date range (e.g., `24 Mar 2025 - Still active`) |

#### Warning Message (`table.warning_message`)

Shows the next expected action, e.g., `"Latvia is about to attack."`.

#### Battle Listings (`.listing.done`)

Each completed battle round is a `<div class="listing done">` containing:

| Element | Description |
|---------|-------------|
| `small` (first) | Battle date (e.g., `27 Mar 2026`) |
| `a.region` | Region name with link to `/en/main/region/{regionName}` |
| `a[href*=battlefield]` | Link to battlefield page (`/en/military/battlefield/{battleId}`) |
| `a small` | Result text (e.g., `Secured by Latvia`) with winner flag |

<details>
<summary>Example Battle Listing (HTML)</summary>

```html
<div class="listing done">
    <small>27 Mar 2026</small>
    <a class="region" href="/en/main/region/Pacifica">Pacifica</a>
    <a href="//www.erepublik.com/en/military/battlefield/883049">
        <small>Secured by Latvia
            <img src="//www.erepublik.net/images/flags_png/M/Latvia.png" alt="" />
        </small>
    </a>
</div>
```

</details>

### Embedded JavaScript Data

The page includes the standard `erepublik` global object and `SERVER_DATA` (same structure as other pages). No war-specific JSON object was found embedded in the page.

### Notes

- **HTML-only endpoint** — no JSON variant is known for war history
- **All rounds listed** — the page displays every battle round for the war (324 listings observed for a year-long war), with no pagination
- **Battle status classes** — `.listing.done` for completed rounds; active battles may use a different class (not observed on this war)
- **Allies display** — allies are shown in a tooltip popup, with flag and country name
- **War date range** — shows start date and either `"Still active"` or the end date
- **Warning message** — appears when one side is expected to initiate the next battle
- **Related endpoints:**
  - [Battlefield Page](battlefield.md) — individual battle details
  - [Campaigns JSON](campaigns.md) — active campaigns with live data
  - [Military News RSS](wars.md#military-news-rss-feed) — RSS feed of military events per country

---

## Support Resistance War (Fund)

**Method:** POST
**URL:** `/en/military/rw-support?_nonce={nonce}`
**Auth Required:** Yes

### Description

Funds a resistance war (RW) in an occupied region. Citizens contribute gold to reach the funding threshold required to start the resistance war, which allows the occupied country's citizens to fight for liberation of the region.

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_nonce` | number | Yes | Client-generated timestamp nonce (e.g., `1772438609287`) |

### Body Parameters (form-encoded)

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `type` | string | Yes | Type of support action. Known value: `fund` |
| `regionId` | number | Yes | ID of the occupied region to fund the RW in |
| `_token` | string | Yes | CSRF token (from page or session) |
| `captcha-response` | string | Yes | Turnstile captcha response token |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded; charset=UTF-8` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/military/rw-support?_nonce=1772438609287' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  --data-raw 'type=fund&regionId=114&_token=YOUR_CSRF_TOKEN&captcha-response=YOUR_CAPTCHA_RESPONSE'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "message": "SUCCESS"
}
```

</details>

### Notes

- `type=fund` is the only observed value; other types may exist (e.g., for endorsing or voting on an RW)
- Requires Turnstile captcha -- cannot be fully automated without captcha solving
- The `_nonce` appears to be a client-generated timestamp (milliseconds since epoch)
- The region must be occupied by a foreign country for the RW option to be available
- See also: [Country Military Page](../API_TOC.md) (`/en/country/military/{countryName}`) which lists active resistance wars and determination levels

---

## Military News RSS Feed

**Method:** GET
**URL:** `/en/main/news/military/all/{countryName}/{page}/rss`
**Auth Required:** No

### Description

Returns an RSS/XML feed of automated military events for a specific country. Unlike the [player-written news RSS](../social/news.md), this feed contains game-generated events: battles started, regions secured, resistance wars, and congressional actions.

### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `countryName` | string | Yes | Country name (e.g., `Lithuania`, `USA`, `Poland`) or `all` for global feed |
| `page` | number | Yes | Page number (1–5). Page 0 is an alias for page 1. Pages 6+ return 404 |

> **Note:** The first `all` segment (between `military` and the country name) is fixed and required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/news/military/all/Lithuania/1/rss'
```

</details>

<details>
<summary>Example Response</summary>

```xml
<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0"
  xmlns:content="//purl.org/rss/1.0/modules/content/"
  xmlns:wfw="//wellformedweb.org/CommentAPI/">
  <channel>
    <title><![CDATA[eRepublik News from Lithuania]]></title>
    <link>https://erepublik.com/en/main/news/military/all/Lithuania/1/rss</link>
    <description><![CDATA[eRepublik News from Lithuania]]></description>
    <language>en-us</language>
    <pubDate>Fri, 27 Mar 2026 09:49:07 -0700</pubDate>
    <item>
      <title><![CDATA[Military Feed]]></title>
      <link>https://www.erepublik.com/en/wars/show/219946</link>
      <pubDate>Fri, 27 Mar 2026 08:52:04 -0700</pubDate>
      <description><![CDATA[<a href="//www.erepublik.com/en/wars/show/219946">Pacifica was secured by Latvia in the war versus Lithuania</a>]]></description>
    </item>
    <item>
      <title><![CDATA[Military Feed]]></title>
      <link>https://www.erepublik.com/en/military/battlefield/883261</link>
      <pubDate>Fri, 27 Mar 2026 02:08:25 -0700</pubDate>
      <description><![CDATA[<a href="//www.erepublik.com/en/military/battlefield/883261">Portugal attacked Gulf of Mexico, Lithuania</a>]]></description>
    </item>
  </channel>
</rss>
```

</details>

### Response Fields (per `<item>`)

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Always `Military Feed` |
| `link` | string | URL to the war page (`/en/wars/show/{warId}`) or battlefield (`/en/military/battlefield/{battleId}`) |
| `pubDate` | string | RFC 2822 event timestamp |
| `description` | string | HTML link with event text (CDATA-wrapped) |

### Known Event Types

| Event | Link Pattern | Description Example |
|-------|-------------|---------------------|
| Region secured | `/en/wars/show/{warId}` | "Pacifica was secured by Latvia in the war versus Lithuania" |
| Attack launched | `/en/military/battlefield/{battleId}` | "Portugal attacked Gulf of Mexico, Lithuania" |
| Congressional action | `/en/main/law/{countryName}/{lawId}` | "A congress donation to X was proposed" |

### Notes

- **Public endpoint** — works without authentication
- **Global or country-specific** — use `all` for countryName to get events from all countries, or a specific country name to filter. Title changes: `"eRepublik News"` (global) vs `"eRepublik News from Lithuania"` (country-specific)
- **Pagination** — 5 pages of unique content (1–5). Page 0 is an alias for page 1. Pages 6+ return 404
- **No `<guid>` element** — items lack a unique identifier (unlike article RSS items)
- **All titles are `Military Feed`** — differentiation is only in the description text
- **Related:** See [News RSS Feed](../social/news.md) for the player-written article RSS variant

---

## Determining Battle Finish Time

There is **no known endpoint** that returns an explicit "battle finished at" timestamp for a completed campaign. Live testing (Feb 2026) confirmed this gap and eliminated several hypotheses.

### Available Timing Data

| Source | Field | Type | Behavior When Finished | Contains Finish Time? |
|--------|-------|------|----------------------|----------------------|
| [`campaignsJson/list`](campaigns.md#list-active-campaigns) | `div.*.end` | Unix timestamp | Per-division finish timestamp | Yes -- but battle disappears from endpoint after finishing |
| [`battleConsole`](battle-info.md#get-battle-console-data) | `battle_time` | Seconds | Keeps ticking (`= serverTime - start_unix`) | No |
| [`battleConsole`](battle-info.md#get-battle-console-data) | `serverTime` | Unix timestamp | Current server time | No |
| [Battlefield page](battlefield.md#get-battlefield-page) | `battle_start_at` | Date string | Preserved (campaign start time) | No (start only) |
| [Battlefield page](battlefield.md#get-battlefield-page) | `battleFinished` | `0` / `1` | `1` when finished | No -- flag only, no timestamp |
| [Battlefield page](battlefield.md#get-battlefield-page) | `zoneElapsedTime` | `"H:M:S"` | Keeps ticking (approx. `battle_time`) | No |
| [`warHistory`](battle-info.md#get-war-history-previous-rounds) | *(same as battleConsole)* | -- | Identical to battleConsole response | No |

### Disproven: `battle_time` Is NOT Frozen for Finished Battles

Initial hypothesis: `battle_time` freezes at the final value when a battle finishes, enabling `finish_time = battle_start_at + battle_time`.

**Live test results (battle 864000, finished):**

| Call | `battle_time` | `serverTime` | `serverTime - battle_time` |
|------|--------------|-------------|--------------------------|
| Call 1 | 1,551,813 | 1,771,042,657 | **1,769,490,844** |
| Call 2 (19s later) | 1,551,832 | 1,771,042,676 | **1,769,490,844** |

`battle_time` increased by exactly 19 seconds between calls. The constant `serverTime - battle_time = 1,769,490,844` equals the campaign start time (Mon, 26 Jan 2026 21:14:04 -0800), confirmed against the page's `battle_start_at`.

**Conclusion:** `battle_time` is always computed as `serverTime - campaign_start_unix`. It never freezes.

> **Useful side-effect:** `battle_time` provides `battle_start_at` as a Unix timestamp: `start_unix = serverTime - battle_time`. This avoids parsing the date string.

### Disproven: `warHistory` Does Not Contain Round Timestamps

Initial hypothesis: `action=warHistory` returns per-round historical data with timestamps.

**Live test results:** The response is structurally and content-wise **identical** to `action=battleConsole`. It shows the current round's state plus cumulative `totalPointsByDivision` -- no per-round breakdown, no timestamps. The `zoneId` parameter is also ignored. See [Get War History](battle-info.md#get-war-history-previous-rounds) for full details.

### Only Working Approach: Capture `div.*.end` While Battle Is Active

The `campaignsJson/list` endpoint provides per-division `end` timestamps (Unix) as each division finishes within a round. The **last** division to report `division_end: true` in the final round effectively marks the campaign's conclusion:

```
finish_time = max(div.*.end for all divisions where division_end == true)
```

**Example** from battle 863509 (captured while still active):
```json
{ "div": 1, "end": 1769344144, "division_end": true },
{ "div": 2, "end": 1769342524, "division_end": true },
{ "div": 3, "end": 1769342524, "division_end": true },
{ "div": 4, "end": 1769342645, "division_end": true },
{ "div": 11, "end": 1769342645, "division_end": true }
```
-> `max(end) = 1769344144` (Div 1 finished last)

**Limitations:**
- **Ephemeral**: Once the campaign finishes completely, it disappears from `campaignsJson/list`. You must capture this data while the battle is still listed
- **Requires polling**: Best suited for bots or monitoring systems that periodically check active battles
- **Not retrospective**: Cannot determine finish time for battles that already ended without having cached data
- **Per-round only**: Shows the current round's division finish times, not previous rounds

### Summary

| Approach | Status | Works Retrospectively? |
|----------|--------|----------------------|
| Capture `max(div.*.end)` from `campaignsJson/list` | **Only working method** | No -- must capture during battle |
| Compute from `battle_start_at + battle_time` | **Disproven** -- `battle_time` never freezes | N/A |
| Extract from `warHistory` response | **Disproven** -- identical to `battleConsole` | N/A |

> **Open question:** There may be undiscovered endpoints (e.g., war history pages, citizen fight logs, or admin APIs) that contain historical battle timing data. The public-facing API currently provides no way to retrospectively determine when a battle finished.
