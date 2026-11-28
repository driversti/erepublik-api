# Congress proposals and laws

[API docs](../README.md) › Country and politics › Congress proposals and laws

A law proposal is voted on for 24 hours by Congress members and the president. Proposals are listed on the [Administration](administration.md#law-proposals) tab; each has its own page.

```mermaid
flowchart LR
  P[Propose] --> L[Law page: vote for 24 h] --> R[Approved or Rejected]
```

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Law page](#law-page) | GET | `/en/main/law/{country}/{lawId}` | ✅ Verified 2026-11-27 |
| [Voting countdown](#voting-countdown) | GET | `/law_countdown/{countryId}/{lawId}` | ✅ Verified 2026-11-27 |
| [Vote on a law](#vote-on-a-law) | POST | `/vote-law` | 🔍 From code |
| [Propose a donation](#propose-a-donation) | POST | `/en/{country}/new-donation` | ❔ Unconfirmed |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Note that two of these paths have **no** `/en` prefix.

Alliance proposals and votes use their own endpoints: see [Alliances](alliances.md).

---

## Law types

| `taxStyle` class | Page heading | Meaning |
|------------------|--------------|---------|
| `Donate` | Donate | Transfer from the treasury |
| `NaturalEnemy` | Natural Enemy | Choose a natural enemy (earlier notes: declare or remove) |
| `Airstrike` | Airstrike | Attack a country without a shared border |
| `DeclareWar` | Declare War | Declare war (earlier notes: on a bordering country) |
| `ResourceConcession` | Resource Concession | Lease a region's resource for a share of industry GDP |
| `MutualProtectionPact` | Alliance | Mutual protection pact |
| `MinimumWage` | Minimum Wage | Change the minimum salary |

More types probably exist (embargo, tax changes, impeachment).

---

## Law page

`GET /en/main/law/{country}/{lawId}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `country` | path | string | Yes | Permalink |
| `lawId` | path | number | Yes | From the [law list](administration.md#law-proposals) |

```bash
curl -L 'https://www.erepublik.com/en/main/law/Poland/344511' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

> [!IMPORTANT]
> Unlike the country tabs, this page needs a session: an anonymous request gets `302` to `/en` (earlier notes, 2026-07-23). A crawler should read that as an expired session, not a missing law.

### Content

```html
<h2 class="special padded">Donate</h2>
<p class="largepadded">Proposed by <a href="…/en/citizen/profile/{citizenId}">PlayerName</a><br>
  <span>Proposed: Day 6,946, 12:48 (21 hours ago)</span></p>
<p class="largepadded">Do you agree to transfer 400000 PLN from the country accounts to <a href="…/en/citizen/profile/{citizenId}">Recipient</a>?</p>

<div class="lawtimer">
  <input type="hidden" id="isFinished" value="no">
  <span class="countdown" id="law_countdown"></span>
  <p>The law voting process takes 24 hours.</p>
</div>
<div class="lawvote"><span id="numberOfVotesPro">33</span> <span class="voteyesdisable">Yes</span></div>
<div class="lawvote last"><span id="numberOfVotesAgainst">0</span> <span class="votenodisable">No</span></div>
<div id="info" class="infoicon"><p>Only congress members and country presidents have the right to vote.</p></div>

<table class="laws largepadded">
  <tr><td><a hovercard="1" href="/en/citizen/profile/{citizenId}">PlayerName</a></td>
      <td><img src="/images/parts/icon-law_valid.gif" alt="Accepted" class="icon">Accepted</td></tr>
</table>
```

| Data | Selector | Notes |
|------|----------|-------|
| Law type | `h2.special` | |
| Proposer and time | `p.largepadded`, first | |
| Question | `p.largepadded`, second | Its links point to the subject |
| Finished | `#isFinished` | `no` or `yes` |
| Result | `.lawtimer span.accept` (`Approved`) or `span.reject` (`Rejected`) | Replaces the countdown |
| Votes | `#numberOfVotesPro`, `#numberOfVotesAgainst` | At page load |
| Extra rule | `#info p` | For Natural Enemy: `it needs 66% of the Congress votes` |
| Vote list | `table.laws.largepadded` | Not the same columns as the table with that class on the Administration tab |

Inline: `var countryId`, `var lawId`, `var canVote` (`0` or `1`), `var token`. Script: `law/showLaw.js`. A **Report law** link goes to `/en/main/tickets-report/{lawId}/law-proposal`.

No cost in gold is shown for any law, including a declaration of war.

### Question formats

| Type | Text |
|------|------|
| Donate | `Do you agree to transfer 400000 PLN from the country accounts to {recipient}?` |
| ResourceConcession | `Do you agree with the Resource Concession of Rubber from Lapland, Finland to Poland in exchange for 5% of Poland's daily Weapons industry GDP ?` |
| MutualProtectionPact | `President of Finland proposed a mutual protection pact with Poland` |
| DeclareWar | `Do you agree on declaring war to {country}` |
| NaturalEnemy | `{country} has been proposed as Natural Enemy.` |
| Airstrike | `{country} has been proposed as a target for an Airstrike.` |
| MinimumWage | `Minimum wage change from 1 LTL to 7 LTL` |

| Type | Link in the question |
|------|----------------------|
| Donate | Citizen or organization profile (often an organization such as a national bank) |
| ResourceConcession | The conceded region and the receiving country |
| MutualProtectionPact | Both countries |
| DeclareWar, NaturalEnemy, Airstrike | Target country (its politics tab) |
| MinimumWage | None |

The concession percentage is what produces the [`Resource Concession Fee`](economy.md#tax-revenue). Mind the space before its `?`.

> [!IMPORTANT]
> A law about another country's region can be rendered **in that country's language**, although the request used `/en/`: a Brazilian region gave `Cervo`, `Alimento`, `Polônia` and `/br/` links. Classify by the [list's class](#law-types), take subjects from link targets, and do not match English words.

**Source:** real requests, 2026-11-27; anonymous redirect — earlier notes.

---

## Voting countdown

`GET /law_countdown/{countryId}/{lawId}` · Auth not checked · ✅ Verified 2026-11-27

Called by the law page while `isFinished` is `no`. No `/en` prefix: with it the path returns `404`.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | Numeric country ID |
| `lawId` | path | number | Yes | Law ID |

```bash
curl 'https://www.erepublik.com/law_countdown/35/344511' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

JSON, although the `Content-Type` says `text/html`:

```json
{
  "begin_date": "Thu, 26 Nov 2026 12:48:37 -0800",
  "server_time": "Fri, 27 Nov 2026 09:50:40 -0800"
}
```

Voting ends at `begin_date` plus one day.

**Source:** real request, 2026-11-27; `law/showLaw.js` (version 1698060179).

---

## Vote on a law

`POST /vote-law` · Auth required (Congress member or president) · 🔍 From code

> [!WARNING]
> Casts your vote.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | `token` from the law page |
| `c` | number | Yes | `countryId` |
| `l` | number | Yes | `lawId` |
| `a` | string | Yes | The vote; its values come from page buttons that were not seen |

The client sends it only when `canVote` is `1` and reads `yes` and `no` (new tallies) from the JSON response.

**Source:** `law/showLaw.js` (version 1698060179), `voteNow`.

---

## Propose a donation

`POST /en/{country}/new-donation` · Auth required (Congress member) · ❔ Unconfirmed

> [!WARNING]
> Creates a law proposal to send treasury money to a citizen or organization.

From earlier notes only. For a citizen who is not in Congress, `GET` on the same path redirected to `/en` (2026-11-27), as does an anonymous request.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token, from the `GET` form |
| `requirments` | number | Yes | Observed `1`; the misspelling is the real field name |
| `currency` | number | Yes | `1` local currency; `0` possibly gold |
| `type_name` | string | Yes | Recipient's display name, not an ID |
| `value` | number | Yes | Amount |
| `debate` | string | No | Debate text |
| `commit` | string | Yes | `Propose` |

```bash
curl 'https://www.erepublik.com/en/Poland/new-donation' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Origin: https://www.erepublik.com' \
  -H 'Referer: https://www.erepublik.com/en/Poland/new-donation' \
  --data-raw '_token=YOUR_CSRF_TOKEN&requirments=1&currency=1&type_name=RECIPIENT_NAME&value=1&debate=&commit=Propose'
```

### Response

A form post followed by a redirect (`302`), not JSON:

```html
<html><head><meta http-equiv="refresh" content="0;url=https://www.erepublik.com/en/country-administration/Poland/1"/></head></html>
```

Success leads to the Administration page, where the proposal appears first; a request without rights leads to `/en`.

**Source:** earlier notes. No call was found in the scripts read.

---

## Open questions

| Topic | Notes |
|-------|-------|
| `a` values of a vote | Not known; the buttons are shown only to voters |
| Other proposal forms | Presumably exist for the other law types |
| Wrong country with a valid `lawId` | Not tested |
| Concession wording | Earlier notes: `to Poland … of Poland's`; localized pages differ only in language |
| MPP wording | Earlier notes show the proposer as the viewed country; on law 343289 it was the partner |
