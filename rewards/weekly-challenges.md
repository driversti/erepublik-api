# Weekly Challenges

Endpoints for tracking weekly challenge progress and collecting rewards. The weekly challenge tracks prestige points earned through daily missions and rewards players with items like energy bars and factory reset tokens at various milestones.

#erepublik #api #rewards #weekly-challenge

[< Back to Rewards](README.md) | [< Back to Table of Contents](../API_TOC.md)

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

## Related

- [Daily Objectives](daily-objectives.md)
- [Daily Missions](daily-missions.md)
- [VIP Points](vip.md)
