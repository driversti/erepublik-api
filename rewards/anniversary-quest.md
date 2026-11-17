# Anniversary Quest

Endpoints for the seasonal **Anniversary Quest** event -- a world map of cities (nodes) that the player unlocks one after another along neighbour links. Each node costs travel miles and quest tokens, takes real time to unlock, and pays out two regular plus two "extra" rewards.

#erepublik #api #rewards #anniversary #event

[< Back to Rewards](README.md) | [< Back to Table of Contents](../README.md)

---

## Get Anniversary Quest Data

**Method:** GET
**URL:** `/en/main/anniversaryQuestData`
**Auth Required:** Yes

### Description

Returns the full state of the Anniversary Quest in one JSON document: the player's unlock queue and per-node progress, the static definition of every map node (cost, unlock time, rewards), the neighbour graph that defines which nodes can be unlocked next, and a rewards counter. Called by the event page `/en/main/anniversaryQuest` (see [framework/endpoints.md](../framework/endpoints.md)).

### Parameters

None.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Recommended |
| Referer | `https://www.erepublik.com/en/main/anniversaryQuest` | No (sent by the page) |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/anniversaryQuestData' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

</details>

<details>
<summary>Example Response (trimmed -- full body ~57 KB: 92 cities, 119 neighbour links)</summary>

```json
{
  "status": {
    "inventory": {
      "distanceNeeded": 1568000,
      "tokensNeeded": 2300,
      "timeNeeded": 1202400
    },
    "queue": [
      {
        "nodeId": 80423,
        "startUnlockTime": "Tue, 17 Nov 2026 01:03:19 -0800",
        "finishUnlockTime": "Tue, 17 Nov 2026 03:03:19 -0800",
        "waitTime": 7200,
        "remainingTime": 241,
        "progress": 3.35
      },
      {
        "nodeId": 80422,
        "startUnlockTime": "Tue, 17 Nov 2026 03:03:20 -0800",
        "finishUnlockTime": "Tue, 17 Nov 2026 15:03:20 -0800",
        "waitTime": 43200,
        "remainingTime": 43442,
        "progress": 0
      }
    ],
    "progress": {
      "80423": {
        "nodeId": 80423,
        "startUnlockTime": "Tue, 17 Nov 2026 01:03:19 -0800",
        "finishUnlockTime": "Tue, 17 Nov 2026 03:03:19 -0800",
        "claimedAt": null,
        "extraClaimedAt": null,
        "nodeStatus": "unlocking",
        "extraStatus": "e_unavailable"
      }
    }
  },
  "cities": {
    "80008": {
      "id": 80008,
      "latitude": -15.7801482,
      "longitude": -47.9291698,
      "cityId": 8,
      "miles": 5000,
      "energy": 0,
      "tokens": 10,
      "waitTime": 3600,
      "skipCost": 40000,
      "rewards": [
        {
          "quality": 2,
          "minutes": 10,
          "ttl": 600,
          "name": "x5 Damage Accelerator for 10 minutes",
          "type": "speed_booster_2",
          "amount": 6
        },
        {
          "name": "Blueprint",
          "skinId": 44,
          "type": "ground_vehicle_blueprint",
          "amount": 1
        }
      ],
      "extraRewards": [
        {
          "name": "Energy Center",
          "ttl": 1641600,
          "type": "energy_house",
          "amount": 260
        },
        {
          "name": "Overtime Points",
          "type": "overtime_points",
          "amount": 76
        }
      ],
      "name": "Brasilia",
      "countryId": 9
    }
  },
  "neighbors": [
    { "nodeId": 80010, "neighborId": 80011 },
    { "nodeId": 80010, "neighborId": 80013 },
    { "nodeId": 80422, "neighborId": 80423 }
  ],
  "rewards": {
    "total": 368,
    "collected": 0,
    "hasExtra": true,
    "canCollectExtra": false
  }
}
```

</details>

### Response Fields

#### `status` -- player state

| Field | Type | Description |
|-------|------|-------------|
| inventory.distanceNeeded | number | Miles needed to unlock **every** node (= sum of `cities[].miles`) |
| inventory.tokensNeeded | number | Quest tokens needed for every node (= sum of `cities[].tokens`) |
| inventory.timeNeeded | number | Seconds of unlock time for every node (= sum of `cities[].waitTime`) |
| queue | array | Nodes queued for unlocking, in order. Unlocks run **sequentially** |
| queue[].nodeId | number | Node ID (key in `cities`) |
| queue[].startUnlockTime / finishUnlockTime | string | RFC 2822 timestamps in PST (`-0800`) |
| queue[].waitTime | number | Unlock duration in seconds (= node's `waitTime`) |
| queue[].remainingTime | number | Seconds until this node finishes, **counting the queue ahead of it** |
| queue[].progress | number | Percent of `waitTime` **still remaining** for the active node (241 / 7200 = 3.35); `0` for nodes that have not started |
| progress | object | Map `nodeId -> state` for every node the player has touched |
| progress{}.claimedAt | string \| null | When the regular rewards were claimed |
| progress{}.extraClaimedAt | string \| null | When the extra rewards were claimed |
| progress{}.nodeStatus | string | Node state. Observed: `unlocking` |
| progress{}.extraStatus | string | Extra-reward state. Observed: `e_unavailable` |

#### `cities` -- map nodes (static)

Map keyed by node ID. **Node ID = 80000 + `cityId`** (eRepublik city ID).

| Field | Type | Description |
|-------|------|-------------|
| id | number | Node ID |
| cityId / countryId | number | eRepublik city / country IDs (see [reference/](../reference/)) |
| name | string | City name |
| latitude / longitude | number | Map position |
| miles | number | Travel miles spent to unlock |
| tokens | number | Quest tokens spent to unlock |
| energy | number | Energy cost (always `0` in the sample) |
| waitTime | number | Unlock duration in seconds |
| skipCost | number | Cost to skip the wait (currency not shown in the response) |
| rewards | array | 2 regular rewards |
| extraRewards | array | 2 extra rewards (see `rewards.hasExtra` / `canCollectExtra`) |

Reward objects always have `name`, `type`, `amount`; optional fields: `quality`, `minutes`, `ttl` (seconds, boosters and timed items), `skinId` (blueprints).

The 92 nodes fall into five cost tiers:

| Nodes | miles | tokens | waitTime | skipCost |
|-------|-------|--------|----------|----------|
| 28 | 5,000 | 10 | 1 h | 40,000 |
| 21 | 9,000 | 20 | 2 h | 100,000 |
| 28 | 19,000 | 30 | 4 h | 240,000 |
| 7 | 37,000 | 40 | 8 h | 560,000 |
| 8 | 56,000 | 60 | 12 h | 960,000 |

#### `neighbors` -- unlock graph

| Field | Type | Description |
|-------|------|-------------|
| nodeId / neighborId | number | An **undirected** edge between two nodes (each pair appears once) |

119 edges connect all 92 nodes; several leaf nodes have a single neighbour.

#### `rewards` -- counter

| Field | Type | Description |
|-------|------|-------------|
| total | number | Total reward items on the map (92 nodes x 4 = 368) |
| collected | number | Items collected so far |
| hasExtra | boolean | Whether the event has extra rewards |
| canCollectExtra | boolean | Whether the player can currently collect extra rewards (presumably a premium/paid unlock -- not confirmed) |

### Reward Types Observed (2026 event)

| `type` | Name | Nodes |
|--------|------|-------|
| `ground_deploy_influence_booster_30` | +30% Ground Influence for 10 minutes | 32 |
| `overtime_points` | Overtime Points | 31 |
| `energy_house` | Energy Center (`ttl` 19 days) | 24 |
| `energy_bars` | Energy Bars | 24 |
| `air_deploy_influence_booster_30` | +30% Air Influence for 10 minutes | 24 |
| `gold` | Gold | 24 |
| `winter_treat` | Treats | 22 |
| `stinger_missile` | Stingers | 18 |
| `d_gnd_bomb_q51` | SLG Peacemaker | 16 |
| `annihilator` | Annihilator | 16 |
| `damage_booster_150` | +150% Ground Damage for 10 minutes | 14 |
| `ground_deploy_rank_points_booster_30` | +30% Ground Rank Points for 10 minutes | 11 |
| `d_air_bomb_q51` | AIM-7 Grid-Lock | 11 |
| `ground_vehicle_blueprint` / `air_vehicle_blueprint` | Blueprint (with `skinId`) | 10 / 10 |
| `air_damage_booster_100` / `air_damage_booster_50` | +100% / +50% Air Damage for 10 minutes | 10 / 10 |
| `fuel` | Fuel | 10 |
| `air_deploy_rank_points_booster_30` | +30% Air Rank Points for 10 minutes | 10 |
| `energy_booster` | +19 Energy recovery for 19 days | 10 |
| `house_booster` | Days of +100% Energy pool and recovery from active houses | 8 |
| `speed_booster_2` | x5 Damage Accelerator for 10 minutes | 7 |
| `deploy_size_booster_500` / `deploy_size_booster_200` | +500% / +200% Deploy Size for 10 minutes | 6 / 6 |
| `vehicle_discharge_document` | Vehicle discharge document (Tallahassee, Salvador) | 2 |
| `anniversary_decoration` | Anniversary Decoration (Victoria) | 1 |
| `permanent_energy_house` | Permanent Energy Center (Wellington) | 1 |

### Notes

- **JSON response**, HTTP `200`. One call returns everything the map page needs; nothing here mutates state.
- `inventory.*Needed` values equal the **totals for the whole map**, even with three nodes already unlocking -- they are not "remaining" amounts. Unlocking the full map takes 1,568,000 miles, 2,300 tokens and 1,202,400 s (~13.9 days) of unlock time.
- The queue chains back-to-back: each `startUnlockTime` is the previous `finishUnlockTime` + 1 s.
- `progress` (percent remaining) counts **down**, unlike most eRepublik progress fields.
- Only `unlocking` / `e_unavailable` states have been observed; the values for unlocked, claimed and extra-available nodes are still unknown.
- The map, costs and rewards are event-specific (sampled 2026-11-17, during the 19th anniversary) and will differ in other years.
- Unlock, skip and claim actions use separate endpoints that are not documented yet.

---

## Related

- [Weekly Challenges](weekly-challenges.md)
- [Daily Missions](daily-missions.md)
- [framework/endpoints.md](../framework/endpoints.md) -- `anniversaryQuest`, `anniversaryQuestHelp`, `anniversary-popup` popups
