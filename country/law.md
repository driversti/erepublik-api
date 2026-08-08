# Law Detail Page

#erepublik #api #country #administration #law

[< Back to Country Overview](README.md)

---

Retrieves a single law proposal as HTML: who proposed it, what it asks, the live vote tally, the countdown to closing, and the individual votes cast so far. This is the page linked from every row of the [Country Administration Page](administration.md) law-proposals table.

**Method:** GET
**URL:** `/en/main/law/{countryName}/{lawId}`
**Auth Required:** **Yes** -- anonymous requests are redirected

## Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | Country permalink name (e.g., `Poland`, `USA`, `Republic-of-Moldova`) |
| lawId | integer | Yes | Law proposal ID. Globally sequential across all countries -- harvest from [administration.md](administration.md) rows |

## Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | **Yes** -- without it the request 302s |
| User-Agent | `Mozilla/5.0 ...` | Recommended (Cloudflare may challenge bot-like requests) |

## Authentication Behaviour

This is the **exception** among country pages -- every other country tab (society, economy, politics, military, administration) is readable while logged out, but the law detail page is not:

| Request | Result |
|---------|--------|
| Anonymous GET | **HTTP 302** -> `Location: https://www.erepublik.com/en` |
| Authenticated GET | HTTP 200, full law page |

<details>
<summary>Verification</summary>

```bash
curl -s -o /dev/null -w "%{http_code} %{redirect_url}\n" \
  'https://www.erepublik.com/en/main/law/Poland/331500' \
  -H 'User-Agent: Mozilla/5.0 ...'
# 302 https://www.erepublik.com/en
```

</details>

> **Scraper impact:** a law crawler cannot run cookie-less like an [administration](administration.md) or [economy](economy.md) crawler can. Detect the redirect (302 / landing on `/en`) as a **session-expired** signal rather than as "law not found".

## Example Request (cURL)

<details>
<summary>cURL example</summary>

```bash
curl -X GET 'https://www.erepublik.com/en/main/law/Poland/{lawId}' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

</details>

## Page Sections (parsed from HTML)

### 1. Proposal Header and Question -- `p.largepadded`

The page contains **exactly two** `p.largepadded` elements, in document order:

| Index | Content | Example text |
|-------|---------|--------------|
| `[0]` | Proposer + timestamp | `Proposed by {citizen} Proposed: Day 6,801, 14:23 (2 days ago)` |
| `[1]` | The law question, with an `<a>` to the subject of the law | `Do you agree on declaring war to Croatia` |

<details>
<summary>Structure</summary>

```html
<!-- element/selector level -- reconstructed from the confirmed selectors -->
<p class="largepadded">
    Proposed by <a href="//www.erepublik.com/en/citizen/profile/{citizenId}">{citizen}</a>
    Proposed: Day 6,801, 14:23 <small>(2 days ago)</small>
</p>
<p class="largepadded">
    Do you agree on declaring war to <a href="...">Croatia</a>
</p>
```

</details>

The `<a>` inside `p.largepadded[1]` points at whatever the law targets, which is the cheapest way to resolve the law's subject without regex-ing the sentence:

| Law type | Link target |
|----------|-------------|
| Donate | Citizen or organization profile |
| ResourceConcession | Region page (the conceded region) |
| MutualProtectionPact | Country page |
| DeclareWar / NaturalEnemy / Airstrike | Country page |

### 2. Vote Tally

| Data | CSS Selector | Notes |
|------|-------------|-------|
| Votes in favour | `span#numberOfVotesPro` | Integer, live |
| Votes against | `span#numberOfVotesAgainst` | Integer, live |

### 3. Countdown Timer

| Data | CSS Selector | Notes |
|------|-------------|-------|
| Time remaining | `div.lawtimer span#law_countdown` | Ticks down client-side; **24h voting window** from the proposal timestamp |

> Once the window closes the law resolves to `Accepted` / `Rejected` -- the status shown in the [administration](administration.md) list.

### 4. Individual Votes -- `table.laws.largepadded`

One row per vote cast: voter name -> verdict (`Accepted`). Note this table reuses the `table.laws` class from the administration page's proposal list, so a shared parser must **not** key off `table.laws` alone -- on the law page it is `table.laws.largepadded` and the columns mean something completely different.

## Observed Question Formats

Confirmed on Poland, eDay 6,748--6,819 (crawled 2026-07-23):

| Law type ([CSS class](administration.md#3-law-proposals)) | Question text |
|-----------|---------------|
| `Donate` | `Do you agree to transfer 400000 PLN from the country accounts to Narodowy Bank Polski?` |
| `ResourceConcession` | `Do you agree with the Resource Concession of Rubber from Lapland, Finland to Poland in exchange for 5% of Poland's daily Weapons industry GDP ?` |
| `MutualProtectionPact` | `President of Poland proposed a mutual protection pact with Finland` |
| `DeclareWar` | `Do you agree on declaring war to Croatia` |

Notes on the wording:

- **Donate** carries the amount and currency inline (`400000 PLN`) and the recipient as the link -- recipients are frequently organizations (e.g. `Narodowy Bank Polski`), not citizens.
- **ResourceConcession** is the most information-dense: resource, source region, source country, destination country, and **the fee percentage plus the industry it is charged against**. That percentage is what later shows up as the negative `Resource Concession Fee` column in [`chartDataJSON`](economy.md#2-tax-revenue-60-days----javascript-variables). Mind the space before the `?`.
- **MutualProtectionPact** is phrased as a statement, not a question -- do not assume every law question starts with `Do you agree`.
- **DeclareWar** shows **no gold cost anywhere on the page**. Declaring war has an in-game cost, but it is not rendered here, in the vote block, or in the countdown block -- do not build a parser expecting to find it. The cost has to come from elsewhere (game rules / wiki).

## Gotcha: the page can render in a foreign locale

When the law's target is a **region belonging to another country**, the page may be served in **that country's locale**, regardless of the `/en/` prefix used in the request. Observed during the Poland crawl: a law targeting a Brazilian region returned a `/br/` URL with Portuguese wording -- `Cervo` (Deer), `Alimento` (Food), `Polônia` (Poland).

Consequences for parsers:

- **Do not match on English keywords** (`Do you agree`, `Resource Concession`, `in exchange for`) to classify a law -- use the `taxStyle {LawType}` CSS class from the [administration](administration.md) listing instead, and carry it into the detail fetch.
- Resource / country / industry names in the question text may be localized. Prefer the **`<a href>`** in `p.largepadded[1]` (permalinks stay in English) and the **resource icon ID** over the visible label.
- Follow redirects (`curl -L`) and expect the final URL prefix to differ from `/en/`.

## Notes

- **HTML page endpoint**, not a JSON API endpoint
- **Requires a session** -- the only country-tree page documented here that does (302 -> `/en` otherwise)
- Law IDs are **globally sequential across all countries**; a valid ID paired with the wrong `{countryName}` has not been tested
- Voting window is **24 hours**; `span#law_countdown` is the authoritative remaining-time source on this page
- Vote counts (`numberOfVotesPro` / `numberOfVotesAgainst`) are the tally *at page render* -- there is no push/refresh endpoint documented yet
- The proposal timestamp uses the same `Day {D}, {HH:MM}` + relative `(N hours ago)` format as the administration listing
- No gold cost is exposed for **any** law type on this page, `DeclareWar` included

---

## See Also

- [Country Administration Page](administration.md) -- law listing, law type CSS classes, law IDs
- [Country Economy Page](economy.md) -- resource concessions in force + the `Resource Concession Fee` they produce
- [Country Politics Page](politics.md)
- [Congressional Donations](congress.md) -- the endpoint that *creates* a Donate law
