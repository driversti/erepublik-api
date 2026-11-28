# Political parties

[API docs](../README.md) › Country and politics › Political parties

Party pages: description and members, the three elections as the party sees them, and candidate lists. Voting is on the [Elections](elections.md) pages.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [My party](#my-party) | GET | `/en/main/my-party` | ✅ Verified 2026-11-27 |
| [Party page](#party-page) | GET | `/en/party/{slug}/{page}` | ✅ Verified 2026-11-27 |
| [Members](#members) | GET | `/en/main/party-members/{partyId}` | ✅ Verified 2026-11-27 |
| [Party president candidates](#party-president-candidates) | GET | `/en/party-candidates/{slug}/{page}` | ✅ Verified 2026-11-27 |
| [Congress candidates](#congress-candidates) | GET | `/en/main/congress-selection/{partyId}` | ✅ Verified 2026-11-27 |
| [Candidacy page](#candidacy-page) | GET | `/en/candidate/{slug}` | 🔍 From code |
| [Resign from a party](#resign-from-a-party) | POST | `/en/resign-party/{slug}` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

`{slug}` is the party's name slug followed by its ID: `partia-imperialna-2840`.

---

## My party

`GET /en/main/my-party` · Auth required · ✅ Verified 2026-11-27

For a party member it ends on their [party page](#party-page) (`/en/party/{slug}/1`), which then also has the [resign form](#resign-from-a-party) and a link to the [candidacy page](#candidacy-page). The result for a citizen without a party was not seen.

```bash
curl -L 'https://www.erepublik.com/en/main/my-party' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real request, 2026-11-27.

---

## Party page

`GET /en/party/{slug}/{page}` · Auth required · ✅ Verified 2026-11-27

Observed with page `1`.

```bash
curl 'https://www.erepublik.com/en/party/partia-imperialna-2840/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Block | Content |
|-------|---------|
| `#profileholder` | Name, party president, country, `National rank: {n}` ([party rankings](../rankings/rankings.md#parties)), website |
| `.about_message` | Description, wiki link |
| Info | **Members**, **Associate Members**, **Orientation** (for example `Center-right, Authoritarian`) |
| Party presidency | President, `Next election in {n} days`, number of candidates; links to [results](elections.md#party-results) and the [candidate list](#party-president-candidates) |
| Congress | Number of congress members and share of seats; [results](elections.md#congress-results) and [proposed candidates](#congress-candidates) |
| Country presidency | Last winner, next candidate of the party, [candidate list](elections.md#presidential-candidates) |

**Source:** real request, 2026-11-27.

---

## Members

`GET /en/main/party-members/{partyId}` · Auth required · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/main/party-members/2840' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

All members come in one HTML page, split into hidden blocks `div.bordersep.pages.page_{n}` that the browser pages through. Each has the level, the citizen link and, where relevant, an office (`Country President`) in `p.smallholder`.

**Source:** real request, 2026-11-27.

---

## Party president candidates

`GET /en/party-candidates/{slug}/{page}` · Auth required · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/party-candidates/partia-imperialna-2840/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Party candidates**: one `.bordersep` per candidate with level, name, a message link and an optional note in `p.smallholder`.

**Source:** real request, 2026-11-27.

---

## Congress candidates

`GET /en/main/congress-selection/{partyId}` · Auth required · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/main/congress-selection/2840' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

Rules printed on the page:

- Members can apply for congressional candidature each month between the 16th and 23rd.
- The party president can change the final list only on the 24th.

`ul#congressCandidatesList`:

| Data | Selector |
|------|----------|
| Count | `Official candidates ({n})` |
| Candidate | `li.congressCandidate`: hidden `candidate_id_{i}`, `.number`, citizen block, presentation |
| Not qualified | Entries after the `li.subtitle` **Not qualified ({n})** |

The apply button and the president's editing controls were not visible to an outsider.

**Source:** real request, 2026-11-27.

---

## Candidacy page

`GET /en/candidate/{slug}` · Auth required · 🔍 From code

Linked from your own party page. With another party's slug the request ended on that party's page. The candidacy screen itself and the request that applies were not opened.

> [!WARNING]
> Leads to running for office; treat any button there as an action on your account.

**Source:** link on the party page, 2026-11-27.

---

## Resign from a party

`POST /en/resign-party/{slug}` · Auth required · 🔍 From code

> [!WARNING]
> Leaves your party.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |

A plain form on your own party page.

**Source:** form on the party page, 2026-11-27.

---

## Open questions

| Topic | Notes |
|-------|-------|
| Joining and founding | No join or create request was seen (the viewer was already a member) |
| Party president tools | Description, logo upload (`dropzone.js` is loaded) and list editing need the role |
| `{page}` | Only `1` was requested |
