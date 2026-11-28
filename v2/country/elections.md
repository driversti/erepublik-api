# Elections

[API docs](../README.md) › Country and politics › Elections

Results and voting for the three monthly elections. Dates shown on [party pages](parties.md#party-page): country president on the 5th, party president on the 15th, Congress on the 25th. The same screen shows results and, during an election, the voting buttons.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Elections pages](#elections-pages) | GET | `/en/main/elections`, `/en/main/{type}-elections` | ✅ Verified 2026-11-27 |
| [Presidential results](#presidential-results) | GET | `/en/main/presidential-elections/{countryId}/{timestamp}` | ✅ Verified 2026-11-27 |
| [Congress results](#congress-results) | GET | `/en/main/congress-elections/{countryId}/{regionId}/{timestamp}` | ✅ Verified 2026-11-27 |
| [Party results](#party-results) | GET | `/en/main/party-elections/{countryId}/{partyId}/{timestamp}` | ✅ Verified 2026-11-27 |
| [Presidential candidates](#presidential-candidates) | GET | `/en/presidential-candidates/country-{countryId}` | ✅ Verified 2026-11-27 |
| [Parties of a country](#parties-of-a-country) | GET | `/party_list/{countryId}` | ✅ Verified 2026-11-27 |
| [Election months](#election-months) | GET | `/date_select/{type}` | ✅ Verified 2026-11-27 |
| [Vote](#vote) | POST | `/en/main/election-vote` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

Becoming a candidate: [Political parties](parties.md).

---

## Elections pages

`GET /en/main/elections` · `GET /en/main/presidential-elections` · `GET /en/main/congress-elections` · `GET /en/main/party-elections` · Auth required · ✅ Verified 2026-11-27

Without parameters each shows the latest election for your own country (and party). `/en/main/elections` showed the presidential screen.

```bash
curl 'https://www.erepublik.com/en/main/presidential-elections' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Element | Content |
|---------|---------|
| `.rightfilters .info` | Next election date and type |
| `#election_image`, `#election_filters` | Switch between the three types |
| `#countries_elections`, `#date_image`, `#party_image` | Country, month and party selectors |
| `#dateSelectDiv a[id="{timestamp}_time"]` | Months, as Unix timestamps |
| `#candidates_holder` | **Official Results** |
| `input#election_id` | Election ID, used to [vote](#vote) |

Choosing a filter navigates to the full results path. Scripts: `election/main.js` plus `votePresident.js`, `voteCongress.js` or `votePartyElections.js`.

**Source:** real requests, 2026-11-27.

---

## Presidential results

`GET /en/main/presidential-elections/{countryId}/{timestamp}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | [Country ID](society.md#country-selector) |
| `timestamp` | path | number | Yes | Election month, from the date filter or the [Politics tab](politics.md#president) |

```bash
curl 'https://www.erepublik.com/en/main/presidential-elections/35/1793865600' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

`ul.political_revamp`, one `li` per candidate:

| Data | Selector |
|------|----------|
| Candidate | `.candidate_info a.candidate_name` |
| Party | `a.president_candidate_party` → `/en/party/{slug}-{partyId}` |
| Support | `.support_parties` (`Supported by one party`, `No supporting parties`), list in `.supporting_parties` |
| Result | `.all_votes .percent`, `.votes` (`131 votes`) |
| Winner | `.all_votes.winner` |

Hidden inputs: `election_id`, `numberOfCandidates`.

**Source:** real request, 2026-11-27.

---

## Congress results

`GET /en/main/congress-elections/{countryId}/{regionId}/{timestamp}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | Country ID |
| `regionId` | path | number | Yes | `0` in the observed links |
| `timestamp` | path | number | Yes | Election month |

```bash
curl 'https://www.erepublik.com/en/main/congress-elections/35/0/1793516400' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

`ul.political_revamp`, one `li#party_li_{partyId}` per party:

| Data | Selector |
|------|----------|
| Party | `a.party_title`, `.party_orientation`, party president and national rank in `.party_details` |
| Result | `.all_votes .percent` (`43.66%`), `.votes` (`124 votes`) |
| Candidates | `a.party` (`Show candidates (17)`), table `#m_candidates_{partyId}` |
| One candidate | Position, citizen block, presentation (`No presentation`) |

Hidden inputs: `country_id`, `region_id`, `date_selected`, `election_id`, `_token`. `date_selected` held a different timestamp from the one in the requested path.

**Source:** real request, 2026-11-27.

---

## Party results

`GET /en/main/party-elections/{countryId}/{partyId}/{timestamp}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | Country ID |
| `partyId` | path | number | Yes | From [the party list](#parties-of-a-country) |
| `timestamp` | path | number | Yes | Election month |

```bash
curl 'https://www.erepublik.com/en/main/party-elections/35/2840/1794729600' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

`table.elections` with columns No. · Candidate · No. of votes · % of votes, then the total in `#number_of_votes`. Hidden inputs: `candidateId_{i}`, `party_id`, `date_selected`, `country_id`, `election_id`, `_token`. Inline: `var hasCaptcha`.

**Source:** real requests, 2026-11-27.

---

## Presidential candidates

`GET /en/presidential-candidates/country-{countryId}` · Auth required · ✅ Verified 2026-11-27

Candidates proposed for the next presidential election.

```bash
curl 'https://www.erepublik.com/en/presidential-candidates/country-35' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

`ul.political_revamp.pres_prop_candidates`, with the same candidate and support markup as [results](#presidential-results), without votes.

**Source:** real request, 2026-11-27.

---

## Parties of a country

`GET /party_list/{countryId}` · Auth not checked · ✅ Verified 2026-11-27

Fills the party filter. No `/en` prefix.

```bash
curl 'https://www.erepublik.com/party_list/35' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

JSON sent as `text/html`:

```json
[
  { "name": "Party name", "id": "1522", "avatar": "https://cdnt.erepublik.net/…" }
]
```

**Source:** real request, 2026-11-27; `election/main.js`, `populatePartyFilters`.

---

## Election months

`GET /date_select/{type}` · Auth not checked · ✅ Verified 2026-11-27 (`president`)

No `/en` prefix: `/en/date_select/president` returns `404`. `{type}` is the election type; only `president` was requested.

```bash
curl 'https://www.erepublik.com/date_select/president' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

```json
[
  { "year": "2008", "month": "Oct", "url": "2008-10" },
  { "year": "2008", "month": "Nov", "url": "2008-11" }
]
```

The script that calls it (`populateDateFilters`) is currently commented out of the filter menu; the pages render the months themselves.

**Source:** real request, 2026-11-27; `election/main.js`.

---

## Vote

`POST /en/main/election-vote` · Auth required · 🔍 From code

> [!WARNING]
> Casts your vote.

Triggered by `a.votePresidentElections`, `a.voteCongressElections` or `a.votePartyElections` in `#candidates_holder`.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `action` | string | Yes | `president`, `congress` or `party` |
| `candidate_id` | number | Yes | `data-candidate_id` of the button |
| `election_id` | number | Yes | `input#election_id` |
| `g-recaptcha-response` | string | When asked | Sent if the page sets `hasCaptcha` or the server asks |

```bash
curl 'https://www.erepublik.com/en/main/election-vote' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=party&candidate_id=1&election_id=1&_token=YOUR_CSRF_TOKEN'
```

### Response

| Field | Client behaviour |
|-------|------------------|
| `displayCaptcha` | Truthy: show or reset the [CAPTCHA](../basics/captcha.md) |
| `error`, `message` | Show the message |
| neither | Reload the page |

**Source:** `election/main.js` (version 1756119570), `sendVote`, `voteElectionCandidate`.

---

## Open questions

| Topic | Notes |
|-------|-------|
| Legacy filter URLs | `main.js` also builds `country-{id}-election-{type}-date-{value}` paths, apparently for older pages |
| `regionId` | A region filter exists in the scripts; no region selector was on the observed page |
| Voting view | The page during an open election, as an eligible voter, was not seen |
| `/js/election/main.js` | The candidates page loads this unversioned copy |
