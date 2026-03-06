# Wars

#erepublik #api #military #wars #resistance

[< Back to Military Module](README.md)

Endpoints and research related to war support and battle timing.

**Related:** [campaigns.md](campaigns.md) | [battle-info.md](battle-info.md) | [battlefield.md](battlefield.md)

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
