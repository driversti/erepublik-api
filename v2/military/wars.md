# Wars and resistance wars

[API docs](../README.md) › [Military](../../military/README.md) › Wars and resistance wars

The **War** screen shows one war: the two sides, their allies and every battle fought in it. A resistance war is a war started to liberate an occupied region. This page also covers funding one and finding out when a battle ended.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [War page](#war-page) | GET | `/en/wars/show/{warId}` | ✅ Verified 2026-11-27 |
| [Support a resistance war](#support-a-resistance-war) | POST | `/en/military/rw-support` | ❔ Unconfirmed |
| [Resource wars page](#resource-wars-page) | GET | `/en/main/resource-wars/{country}/{page}` | ❔ Unconfirmed |
| [Recover health link](#recover-health-link) | GET | `/en/get-gold/health` | ❔ Unconfirmed |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## War page

`GET /en/wars/show/{warId}` · Public · ✅ Verified 2026-11-27

An HTML page; no JSON variant is known. All battles are listed at once, without pagination: a war running since 2019 had 2766 rows in about 1.4 MB. Without a cookie the page still answers, but active battles have no **Join** link.

### Request

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `warId` | path | number | Yes | War ID: `war_id` in the [battle list](campaigns.md#list-battles) |

```bash
curl 'https://www.erepublik.com/en/wars/show/219946'
```

### Response

#### Header (`.war_list_header`)

| Selector | Description |
|----------|-------------|
| `.country.left_side h3`, `.country.right_side h3` | Side names (see below) |
| `.country img` | Flag, `/images/flags_png/L/{Country}.png`; `Resistance.png` for a resistance force |
| `.country a[href*="country/society"]` | Country link; `javascript:;` for a resistance force |
| `a.show_allies` | Number of allies; `.allies_tooltip li` holds flag and name of each |
| `a.none` | "no allies". The `id="defender_allies"` it carries appears on **both** sides |
| `.vs small` | `24 Oct 2026 - Still active`, or `24 Aug 2009 - 18 Oct 2010` for an ended war |

| War type | Left side | Right side |
|----------|-----------|------------|
| Direct | Country | Country |
| Resistance | `Resistance Force of {Country}` (`h3.resistance`, no allies element) | Occupying country |
| Civil war (dictatorship) | `Civil War of {Country}` | The same country |

In the active battles checked (one direct, two resistance), the left side was the `inv` country of the [battle list](campaigns.md#list-battles). [Earlier notes](#open-questions) call it the defender.

#### Status message

At most one of these tables is present; many active wars have neither.

| Selector | Observed text |
|----------|---------------|
| `table.info_message` | `This war is no longer active.` |
| `table.warning_message` | `Peru is about to attack.` |

#### Battle rows (`.listing`)

| Row | Class | Content |
|-----|-------|---------|
| Active | `listing` | `a.join` → `/en/military/battlefield/{battleId}`; `a.region`; `small` with `started on 27 November 2026, 07:25` (game time) |
| Active resistance war | `listing resistance` | `strong` region name (no link, no date); `a.join` and `a.reversed`, both → [choose side link](battlefield/page.md#choose-side-link) |
| Finished | `listing done` | `small` date (`25 Nov 2026`); `a.region`; battlefield link with the result text and the winner's flag |

Result texts observed: `Secured by {Country}`, `Conquered by {Country}`, `Liberated by {Country}`.

`a.region` points to the [region page](../world/map.md#region-page).

<details>
<summary>Example rows (active, resistance, finished)</summary>

```html
<div class="listing">
    <a href="//www.erepublik.com/en/military/battlefield/959046" class="join" title="Join"><span>Join</span></a>
    <a class="region" href="/en/main/region/North-Caucasus">North Caucasus</a>
    <small> started on 27 November 2026, 07:25</small>
</div>

<div class="listing resistance">
    <a href="//www.erepublik.com/en/military/battlefield-choose-side/958793/13" class="join" title="Join Resistance"><span>Join Resistance</span></a>
    <strong>Central Hungary</strong>
    <a href="//www.erepublik.com/en/military/battlefield-choose-side/958793/65" class="reversed" title="Fight for Serbia"><span>Fight for Serbia</span></a>
</div>

<div class="listing done">
    <small>25 Nov 2026</small>
    <a class="region" href="/en/main/region/Hrodzienskaya">Hrodzienskaya</a>
    <a href="//www.erepublik.com/en/military/battlefield/958418">
        <small>Secured by Belarus <img src="//www.erepublik.net/images/flags_png/M/Belarus.png" alt=""></small>
    </a>
</div>
```

</details>

The page also carries the standard `erepublik` and `SERVER_DATA` globals; no war-specific JSON object was found.

### Errors

An unknown `warId` returns HTTP `404` with an HTML page.

### Where war IDs come from

- `war_id` in the [battle list](campaigns.md#list-battles).
- Links on the [country military page](../country/military.md), which also lists a country's active resistance wars.
- Links in the [military news RSS feed](../media/news.md#military-news-rss).

**Source:** real requests, 2026-11-27 (direct, resistance, civil and ended wars).

---

## Support a resistance war

`POST /en/military/rw-support` · Auth required · ❔ Unconfirmed

> [!WARNING]
> Spends gold: funds a resistance war in an occupied region.

Citizens contribute until the funding threshold is reached and the resistance war starts. This section comes from earlier notes: the request was not repeated, and no caller was found in the current scripts.

### Request

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `_nonce` | query | number | Yes | Timestamp in milliseconds, generated by the client |
| `type` | body | string | Yes | `fund` is the only known value |
| `regionId` | body | number | Yes | Occupied region |
| `_token` | body | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `captcha-response` | body | string | Yes | Cloudflare Turnstile response |

```bash
curl 'https://www.erepublik.com/en/military/rw-support?_nonce=1772438609287' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'type=fund&regionId=114&_token=YOUR_CSRF_TOKEN&captcha-response=YOUR_CAPTCHA_RESPONSE'
```

### Response

```json
{ "error": false, "message": "SUCCESS" }
```

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | `false` on success |
| `message` | string | `SUCCESS` |

**Source:** earlier notes.

---

## Resource wars page

`GET /en/main/resource-wars/{country}/{page}` · ❔ Unconfirmed

Probably a screen of the Resource Wars feature. `/en/main/resource-wars/Romania/1` returned HTTP `404` on 2026-11-27, so parameters and response are unknown. The current scripts show extra country leaderboards when `erepublik.info.resourceWars` is set, but do not request this path.

**Source:** real request, 2026-11-27; `erpk_angular_controllers.js` (version 1795779904).

---

## Recover health link

`GET /en/get-gold/health` · Auth unknown · ❔ Unconfirmed

> [!CAUTION]
> The effect is unknown and it may spend resources. It was not requested.

The war page HTML contains a link to it (class `get_health_text energy_bar`) next to a "Recover … more in" timer. It looks like a leftover of an old health widget; current energy refills are on [Energy](../home/energy.md).

```bash
curl 'https://www.erepublik.com/en/get-gold/health' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** HTML of `/en/wars/show/{warId}`, real request 2026-11-28 (the link was not followed).

---

## When did a battle end?

The [war history](battlefield/live-data.md#war-history) action (`warList`) returns `result.end`, such as `2026-11-26 03:29:04`, for earlier battles of a war. This is the way to get finish times afterwards. Its time zone is not confirmed.

Earlier notes (February 2026) found no such source: they tested `warHistory`, not `warList`.

| Source | Field | After the battle ends | Gives the finish time? |
|--------|-------|-----------------------|------------------------|
| [War history](battlefield/live-data.md#war-history) | `list[].result.end` | Preserved | Yes |
| [Battle list](campaigns.md#list-battles) | `div.*.end` (Unix) | The battle disappears from the list | Yes, if captured before that |
| [War page](#war-page) | Date of a finished row | Preserved | Day only (❔ which moment the date marks) |
| [Battle console](battlefield/live-data.md#current-battle-zone) | `battle_time` (seconds) | Keeps growing | No |
| [Battle console](battlefield/live-data.md#current-battle-zone) | `serverTime` | Current time | No |
| [Battlefield page](battlefield/page.md#battlefield-page) | `battle_start_at` | Preserved | No, start only |
| [Battlefield page](battlefield/page.md#battlefield-page) | `battleFinished` (`0`/`1`) | `1` | No, flag only |
| [Battlefield page](battlefield/page.md#battlefield-page) | `zoneElapsedTime` (`H:M:S`) | Keeps growing | No |

### Division end times while the battle is listed

The last division to get `division_end: true` in the final round marks the end of the campaign:

```
finish_time = max(div.*.end where division_end == true)
```

Example, battle 863509, captured while still listed: divisions 1, 2, 3, 4 and 11 ended at `1769344144`, `1769342524`, `1769342524`, `1769342645` and `1769342645`, so the maximum is division 1's.

- This requires polling the list; it does not work for battles you did not capture (use war history for those).
- `end` covers the current round only, not earlier rounds.

### What does not work

**`battle_time` does not freeze.** Two calls for finished battle 864000, 19 seconds apart:

| Call | `battle_time` | `serverTime` | Difference |
|------|---------------|--------------|------------|
| 1 | 1551813 | 1771042657 | 1769490844 |
| 2 | 1551832 | 1771042676 | 1769490844 |

The difference is the campaign start (26 Jan 2026 21:14:04 -0800, matching `battle_start_at`), so `battle_time = serverTime - start`. This also gives the start as a Unix timestamp without parsing a date string.

**`action=warHistory` has no round timestamps.** Its response was identical to `battleConsole`, and `zoneId` was ignored.

**Source:** `warList` — real request described on [live data](battlefield/live-data.md#war-history), 2026-11-27; the rest — earlier notes (live tests, February 2026).

---

## Open questions

| Topic | Earlier notes | Observed 2026-11-27 | Page follows |
|-------|---------------|---------------------|--------------|
| Left side of the header | Defender | Matched `inv` in three active battles | Observation; the role over a whole war is unclear |
| Pagination | None (324 rows) | None (up to 2766 rows) | Both |
| Resistance funding | `POST rw-support` with Turnstile | No button or caller found on the war page, region page or in the main scripts | Earlier notes |
| Finish time of an ended battle | No endpoint returns it | `warList` returns `result.end` | Observation |
| Time zone of `result.end` | — | Not confirmed | Unknown |
| Other `type` values of `rw-support` | May exist | — | Unknown |
| Resource wars page | Not described | HTTP `404` | Unconfirmed |
