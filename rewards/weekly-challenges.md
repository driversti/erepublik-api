# Weekly Challenges

Endpoints for tracking weekly challenge progress and collecting rewards. The weekly challenge tracks prestige points earned through daily missions and rewards players with items like energy bars and factory reset tokens at various milestones.

#erepublik #api #rewards #weekly-challenge

[< Back to Rewards](README.md) | [< Back to Table of Contents](../README.md)

---

## Get Weekly Challenge Data

**Method:** GET
**URL:** `/en/main/weekly-challenge-data`
**Auth Required:** Yes

### Description

Retrieves the player's progress in the weekly challenge system, including prestige point progression, unlocked/locked rewards at different thresholds, collected reward history, and time remaining until the next weekly reset. The weekly challenge tracks prestige points earned through [daily missions](daily-missions.md) and rewards players with items like energy bars and factory reset tokens at various milestones.

### Parameters

None (query parameters not required)

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X GET 'https://www.erepublik.com/en/main/weekly-challenge-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "enabled": true,
  "type": {
    "anniversary": false,
    "flavorPacks": false,
    "springChallenge": false,
    "summerChallenge": false,
    "tropicalChallenge": false,
    "blueElectric": false,
    "autumnChallenge": false,
    "shadowChallenge": false,
    "halloweenChallenge": false,
    "winterChallenge": false,
    "skinChallenge": false,
    "valentinesDay": false,
    "lunarNewYear": false,
    "childrensDay": false,
    "cryptoChallenge": false,
    "icarusChallenge": false
  },
  "title": "Weekly Challenge",
  "timeLeft": 146658,
  "nextReward": {
    "maxReward": false,
    "type": "icon_energy_bars",
    "text": "20 Energy Bars"
  },
  "maxRewardId": 0,
  "player": {
    "avatar": "https://cdnt.erepublik.net/enQ9lXJMVlMaSYRgyEsa10ndQwA=/55x55/smart/avatars/Citizens/2011/04/12/1907aee62f42c11beba21159840cdc16.png?fa70b874e2f337186b979af9404828eb",
    "name": "driver sti",
    "prestigePoints": 38346
  },
  "progress": 0.95865,
  "rewards": {
    "normal": [
      {
        "id": 115,
        "collectedBefore": 114,
        "percent": 0.75,
        "label": "You have already collected this reward",
        "tooltip": "20 Energy Bars",
        "status": "rewarded",
        "icon": "energy_bars"
      },
      {
        "id": 126,
        "collectedBefore": 0,
        "percent": 0.025,
        "label": "Reach 39,000 Prestige Points to unlock the following reward: 20 Energy Bars",
        "tooltip": "Reach 39000 Prestige Points to unlock the following reward: 20 Energy Bars",
        "status": "",
        "icon": "energy_bars"
      }
    ],
    "extra": []
  },
  "skinId": null,
  "weeklyId": 641
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| error | boolean | Whether an error occurred |
| enabled | boolean | Whether weekly challenge is enabled for this player |
| type | object | Active seasonal/themed challenge flags (most are false) |
| title | string | Challenge title (typically "Weekly Challenge") |
| timeLeft | number | Seconds remaining until weekly reset |
| nextReward | object | Details about the next available reward |
| nextReward.maxReward | boolean | Whether this is the final max reward |
| nextReward.type | string | Icon type for the reward (e.g., "icon_energy_bars") |
| nextReward.text | string | Reward description (e.g., "20 Energy Bars") |
| maxRewardId | number | ID of the maximum reward tier |
| player.avatar | string | Player's avatar image URL |
| player.name | string | Player's name |
| player.prestigePoints | number | Player's current prestige points |
| progress | number | Progress percentage to next reward (0.0-1.0) |
| rewards.normal | array | List of standard reward tiers |
| rewards.normal[].id | number | Unique reward tier ID |
| rewards.normal[].collectedBefore | number | How many times collected previously (for recurring rewards) |
| rewards.normal[].percent | number | Progress percentage needed for this tier (0.0-1.0) |
| rewards.normal[].label | string | Display label for this reward tier |
| rewards.normal[].tooltip | string | Tooltip text with detailed reward info |
| rewards.normal[].status | string | Reward status ("rewarded" if collected, "" if locked) |
| rewards.normal[].icon | string | Icon identifier (e.g., "energy_bars", "factory_reset_token") |
| rewards.extra | array | Additional special rewards (typically empty) |
| skinId | number/null | ID of active themed skin (null for standard challenge) |
| weeklyId | number | Unique identifier for this weekly challenge period |

### Reward Icons

| Icon | Description |
|------|-------------|
| energy_bars | Energy Bar rewards (typically 20 bars) |
| factory_reset_token | Factory Reset Tokens (typically 2 tokens) |

### Notes

- The weekly challenge resets every week and rewards prestige points earned through [daily missions](daily-missions.md)
- `progress` is calculated as `(currentPrestigePoints % thresholdIncrement) / thresholdIncrement`
- In the example, progress is 0.95865 = (38346 % 39000) / (39000 - 38000) ~ 346/1000
- Rewards alternate between energy bars (75% and 2.5% thresholds) and factory reset tokens (0.25% thresholds)
- The `type` object contains flags for seasonal/themed challenges (anniversary, spring, summer, etc.)
- Most type flags are `false` during standard weekly challenges
- `collectedBefore` tracks how many times the same reward tier was collected in previous weeks
- `weeklyId` increments each week and can be used to track weekly challenge history
- Players can claim rewards as they reach each threshold
- The challenge continues indefinitely with repeating reward tiers
- Prestige points are earned primarily through [daily missions](daily-missions.md) (see "Get Daily Missions Data" endpoint)

---

## Collect Weekly Challenge Reward

**Method:** POST
**URL:** `/en/main/weekly-challenge-collect-reward`
**Auth Required:** Yes

### Description

Claims a specific reward from the weekly challenge system. When a player reaches a prestige point threshold, they can claim the associated reward (e.g., Energy Bars, Factory Reset Tokens). Returns an error if the reward has already been collected or if the threshold hasn't been reached yet.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| rewardId | number | Yes | The unique reward tier ID from the weekly challenge data |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/weekly-challenge-collect-reward' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'rewardId=53&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response (Already Collected)</summary>

```json
{
  "status": "error",
  "message": "Already collected"
}
```

</details>

<details>
<summary>Example Response (Success - Expected)</summary>

```json
{
  "status": "success",
  "message": "Reward collected"
}
```

</details>

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| status | string | Result status ("success" or "error") |
| message | string | Human-readable result message |

### Notes

- The `rewardId` must match a valid reward tier ID from the `/en/main/weekly-challenge-data` response
- Rewards can only be collected once per weekly challenge period
- The reward must be unlocked (player must have reached the required prestige point threshold)
- Returns `"Already collected"` error if the reward was previously claimed
- Returns error if the prestige point threshold hasn't been reached yet
- Works in conjunction with `weekly-challenge-data` endpoint which shows available rewards and their IDs
- Reward IDs correspond to the `rewards.normal[].id` field from the weekly challenge data
- Each `weeklyId` period has its own set of collectable rewards

---

## Collect All Weekly Challenge Rewards

**Method:** POST
**URL:** `/en/main/weekly-challenge-collect-all`
**Auth Required:** Yes

### Description

Claims **all currently unlocked, uncollected** weekly challenge rewards in a single request, up to the specified `maxRewardId` tier. This is the bulk counterpart to [`weekly-challenge-collect-reward`](#collect-weekly-challenge-reward) (which claims one tier at a time).

> **Important:** The response **does not report what was collected** -- the `rewards` array comes back empty even when a reward was actually granted (verified: collecting tiers 12 and 13 each flipped their `weekly-challenge-data` status from `completed` to `rewarded`, yet both calls returned `{"error":false,"rewards":[]}`). To learn what changed, re-fetch [`weekly-challenge-data`](#get-weekly-challenge-data) and compare tier statuses before/after.

### Request Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| maxRewardId | number | Yes | The highest reward tier ID to collect up to. Corresponds to `maxRewardId` from the [weekly-challenge-data](#get-weekly-challenge-data) response. All unlocked rewards up to this tier are claimed. |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/weekly-challenge-collect-all' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  --data-raw 'maxRewardId=10&_token=YOUR_CSRF_TOKEN'
```

</details>

This endpoint returns **two different JSON shapes**, both with HTTP `200`:

<details>
<summary>Example Response A -- valid request (most calls)</summary>

```json
{
  "error": false,
  "rewards": []
}
```

Returned whether or not a reward was actually granted. The `rewards` array was **empty in every observed call**, including calls that demonstrably collected a tier (see the Description warning). Treat `error: false` as "request accepted" -- not as proof that something was collected.

</details>

<details>
<summary>Example Response B -- "no_reward" error</summary>

```json
{
  "status": "error",
  "message": "no_reward"
}
```

</details>

### Response Fields

**Response A (valid request):**

| Field | Type | Description |
|-------|------|-------------|
| error | boolean | `false` on an accepted request |
| rewards | array | Always observed empty (`[]`), even when a tier was actually collected. Does **not** echo granted items. |

**Response B (error):**

| Field | Type | Description |
|-------|------|-------------|
| status | string | `"error"` |
| message | string | Error code (e.g. `no_reward`) |

### Notes

- **Bulk collection:** Unlike `weekly-challenge-collect-reward` (single `rewardId`), this endpoint sweeps all unlocked, uncollected tiers up to `maxRewardId` at once. Collection is confirmed only via the [`weekly-challenge-data`](#get-weekly-challenge-data) status flip (`completed` -> `rewarded`), not via the response body.
- `maxRewardId` corresponds to the `maxRewardId` field returned by [`/en/main/weekly-challenge-data`](#get-weekly-challenge-data) (observed value `13` for a fully-progressed weekly challenge). Tiers with `status: "completed"` are unlocked-but-uncollected and will be granted; `rewarded` tiers are already collected; `""` tiers are still locked.
- **The `rewards` array does not report collected items.** Confirmed across `maxRewardId` 11, 12, and 13: tiers 12 and 13 were genuinely collected (status flipped to `rewarded`, e.g. tier 13 = "5 Q5 Moving Tickets") yet every response was `{"error":false,"rewards":[]}`. To know what you received, diff the `weekly-challenge-data` tier statuses.
- **HTTP status is always `200`** -- even on the `no_reward` error. Detect failure by checking for the `status:"error"` shape; an accepted request uses the `error:false` (boolean) shape instead.
- **`no_reward` anomaly:** `maxRewardId=10` returned `{"status":"error","message":"no_reward"}`, while `maxRewardId=11/12/13` returned the `error:false` shape. The exact rule selecting between the two shapes is unclear from the available samples.
- This differs from the single-reward endpoint, which returns `{"status":..., "message":"Reward collected"/"Already collected"}`.

---

## Get Weekly Challenge Rewards List

**Method:** GET
**URL:** `/en/main/weekly-challenge-rewards`
**Auth Required:** Yes

### Description

Returns the **HTML fragment** for the "Weekly Challenge Rewards" popup (popup name `weeklyChallengeRewardsList`, see [framework/endpoints.md](../framework/endpoints.md#weekly-challenge-rewards)). It lists every prestige-point milestone of the current challenge week together with its reward, so it is the only place to read the **full reward ladder with absolute point thresholds** -- [`weekly-challenge-data`](#get-weekly-challenge-data) only exposes tiers as percentages (`percent`) and labels.

### Parameters

None.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Recommended (sent by the game's popup loader) |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/weekly-challenge-rewards' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

</details>

<details>
<summary>Example Response (HTML, trimmed -- full body ~170 KB, 257 rows)</summary>

```html
<div class="fadeInRight" id="WeeklyRewardsList">
    <a href="javascript:" class="close closeButton" title="Close">&#10006;</a>
    <div class="popup_title">
        <img src="//www.erepublik.net/images/modules/popups/weekly_challenge_rewards/prestige_booster.png" alt="">
        <h2>Weekly Challenge Rewards</h2>
    </div>
    <div class="rewardsWrapper">
        <div class="progressBar">
            <span class="currentProgress" style="height: calc(70px * 0);"></span>
        </div>
        <div class="rewardRow points_0 pending">
            <div class="rewardProgress">
                <span class="points pending">5</span>
                <img class="status" src="//www.erepublik.net/images/modules/popups/weekly_challenge_rewards/white_rhombus.png" alt="">
            </div>
            <div class="rewardWrapper pending">
                <div class="rewardIcon">
                    <img src="https://www.erepublik.net/images/icons/rewards/energy_booster.png?1698060173" alt="">
                </div>
                <b class="pending">+1 Energy recovery until the end of Day 6,943</b>
            </div>
        </div>
        <!-- ... rows points_1 .. points_255 ... -->
        <div class="rewardRow points_256 pending">
            <div class="rewardProgress">
                <span class="points pending">90000</span>
                <img class="status" src="//www.erepublik.net/images/modules/popups/weekly_challenge_rewards/white_rhombus.png" alt="">
            </div>
            <div class="rewardWrapper pending">
                <div class="rewardIcon">
                    <img src="https://www.erepublik.net/images/icons/rewards/128px/amazing_coin.png?1698060173" alt="">
                </div>
                <b class="pending">+10 Amazing Coins</b>
            </div>
        </div>
    </div>
    <div class="clear"></div>
</div>
<script>
    $j('.rewardsWrapper').mCustomScrollbar({ theme: 'gray-thick' });
    setTimeout(function () {
        $j('.rewardsWrapper').mCustomScrollbar('scrollTo', '.rewardRow.points_' + -1);
    }, 500);
</script>
```

</details>

### Parsing

| Selector | Meaning |
|----------|---------|
| `.rewardRow.points_{i}` | One milestone; `i` is a 0-based row index (**not** a point value) |
| `.rewardRow` 3rd class | Row state. Only `pending` (not reached) observed so far |
| `.rewardProgress .points` | Prestige-point threshold (plain integer, no thousands separator) |
| `.rewardProgress img.status` | State icon (`white_rhombus.png` for pending) |
| `.rewardIcon img[src]` | Reward type, by file name (see table below) |
| `.rewardWrapper b` | Human-readable reward text |
| `.currentProgress` `style` | `calc(70px * N)` -- progress-bar fill; rows are 70 px tall, so `N` appears to be the number of milestones reached |
| `<script>` `scrollTo ... points_' + X` | Index of the last reached row; `-1` when none reached |

Reward icon file names observed:

| Icon | Reward text pattern |
|------|---------------------|
| `energy_booster.png` | `+1 Energy recovery until the end of Day {N}` |
| `128px/amazing_coin.png` | `+10 Amazing Coins` / `+20 Amazing Coins` |
| `q10_128x128.png` | `{1,5,10,15,20} Energy Bars` |
| `fuel.png` | `+5 Fuel` |
| `damage.png` | `+150% Damage Booster for 10 minutes` |
| `air_damage.png` | `+50% Air Damage Booster for 5 minutes` |
| `q21_128x128.png` | `1 Small Bombs` |

### Reward Ladder (sample, week ending Day 6,943)

257 milestones from **5** to **90,000** prestige points:

| Points range | Spacing | Rewards |
|--------------|---------|---------|
| 5 -- 5,000 | irregular (5 -> 1,000 -> 250) | Mostly energy-recovery boosts and +20 Amazing Coins, plus +5 Fuel, damage / air-damage boosters, 1-5 Energy Bars, 1 Small Bomb |
| 5,000 -- 20,000 | ~250 | Energy-recovery boosts, +10 Amazing Coins, 10 Energy Bars (to 15k) then 15 Energy Bars |
| 20,000 -- 40,000 | 500 | Alternating 20 Energy Bars / +10 Amazing Coins, occasional +5 Fuel |
| 40,000 -- 80,000 | 500 | Alternating +10 Amazing Coins / 5 Energy Bars |
| 80,000 -- 90,000 | 1,000 | +10 Amazing Coins |

Totals across the whole ladder: **1,200 Amazing Coins** (90 x 10 + 15 x 20), **862 Energy Bars**, 50 energy-recovery boosts, 35 Fuel (7 x 5), 4 damage + 3 air-damage boosters, 1 Small Bomb.

### Notes

- **Response is HTML, not JSON**, despite `X-Requested-With`. HTTP `200` on success.
- Sampled on a weekly-reset Tuesday with 0 prestige points, so every row was `pending`. The class names for reached / collected rows are **not yet observed** -- likely they mirror the `completed` / `rewarded` statuses of [`weekly-challenge-data`](#get-weekly-challenge-data), but verify before relying on it.
- Energy-recovery rewards expire at the end of the challenge week: `Day 6,943` = Monday 2026-11-23, the last game day before the next Tuesday 00:00 PST reset. The day number therefore changes weekly.
- The ladder (and the quantities) is server-defined and may change between weeks; re-fetch rather than hardcode. Icon URLs carry cache-busting `?{timestamp}` suffixes.
- No collect action here -- use [`weekly-challenge-collect-reward`](#collect-weekly-challenge-reward) or [`weekly-challenge-collect-all`](#collect-all-weekly-challenge-rewards).

---

## Related

- [Daily Objectives](daily-objectives.md)
- [Daily Missions](daily-missions.md)
- [VIP Points](vip.md)
