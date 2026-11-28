# Daily objectives

[API docs](../README.md) › Home and daily routine › Daily objectives

The five reward chests at 20, 40, 60, 80 and 100 Activity Points, shown in the Daily Activities popup. Points come mainly from [daily missions](daily-missions.md). Everything resets at 00:00 game time.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Objective status](#objective-status) | POST | `/en/main/objective-status` | ✅ Verified 2026-11-27 |
| [Claim a chest](#claim-a-chest) | POST | `/en/main/objective-claim-reward` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Objective status

`POST /en/main/objective-status` · Auth required · ✅ Verified 2026-11-27

Progress, claimed chests, and the rewards of every chest. Read-only.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |

```bash
curl 'https://www.erepublik.com/en/main/objective-status' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

### Response

Shortened to one chest:

```json
{
  "status": {
    "progress": 65,
    "claimedObjectives": {
      "20": { "objective_cost": 20, "claimed": true, "claimed_at": "2026-11-27 00:21:59" }
    },
    "endDate": "Fri, 27 Nov 2026 23:59:59 -0800",
    "timeLeft": 51532
  },
  "data": {
    "20": { "objective_cost": 20, "rewards": [ { "type": "strength", "name": "Strength", "amount": 2 } ] }
  }
}
```

<details>
<summary>All five chests (2026-11-27)</summary>

| Chest | Rewards (`type`: amount) |
|-------|--------------------------|
| 20 | `strength`: 2, `overtime_points`: 1, `food_raw`: 10 *, `collection_part_3` (Barrel): 1, `currency`: 100 * |
| 40 | `speed_booster` (x2 Damage Accelerator, `minutes`: 3): 1, `overtime_points`: 1, `food_raw`: 15 *, `collection_part_5` (Projectile): 1, `currency`: 200 * |
| 60 | `ghost_booster` (`minutes`: 1): 1, `overtime_points`: 1, `weapon_raw`: 25 *, `collection_part_4` (Scope): 1, `currency`: 300 * |
| 80 | `fuel`: 1, `overtime_points`: 1, `house_raw`: 1, `collection_part_6` (Trigger Kit): 1, `currency`: 400 * |
| 100 | `energy_bars`: 1, `overtime_points`: 1, `aircraft_raw`: 1, `collection_part_2` (Stock): 1, `currency`: 500 * |

\* carries `new_player_ramp_up_factor: 0.1`.

</details>

| Field | Type | Description |
|-------|------|-------------|
| `status.progress` | number | Activity Points collected today |
| `status.claimedObjectives` | object | Keyed by chest cost: `objective_cost`, `claimed`, `claimed_at` (game time) |
| `status.endDate` | string | End of the game day |
| `status.timeLeft` | number | Seconds until reset |
| `data.{cost}.objective_cost` | number | Points needed |
| `data.{cost}.rewards[]` | array | `type`, `name`, `amount`; `minutes` on boosters |
| `data.{cost}.rewards[].new_player_ramp_up_factor` | number | Present on some rewards; appears to reduce them for new players |

The client treats a chest as claimable when `progress >= cost` and `cost` is not a key of `claimedObjectives`.

**Source:** real request, 2026-11-27; `DailyMissionsController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Claim a chest

`POST /en/main/objective-claim-reward` · Auth required · 🔍 From code

> [!WARNING]
> Claims the chest on your account. Each chest can be claimed once per day.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `objectiveCost` | number | Yes | `20`, `40`, `60`, `80` or `100` |

```bash
curl 'https://www.erepublik.com/en/main/objective-claim-reward' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Referer: https://www.erepublik.com/en' \
  --data-raw 'objectiveCost=20&_token=YOUR_CSRF_TOKEN'
```

Earlier notes say the `Referer` header is required.

### Response

Earlier notes (verified 2026-02-28 with an external bot):

```json
{
  "rewards": [
    { "type": "strength", "name": "Strength", "amount": 2 },
    { "type": "food_raw", "name": "Food Raw Materials", "amount": 1, "new_player_ramp_up_factor": 0.1 },
    { "type": "currency", "name": "Currency", "amount": 10, "new_player_ramp_up_factor": 0.1 }
  ]
}
```

### Errors

A truthy `error` means failure; the client shows `error.message`. Earlier notes: claiming a chest twice fails.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `DailyMissionsFactory.claimObjective`; example — earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Collection part IDs | 4 Projectile, 5 Scope, 7 Stock | 2 Stock, 3 Barrel, 4 Scope, 5 Projectile, 6 Trigger Kit | Live response |
| Booster type names | `damage_accelerator`, `energy_bar` | `speed_booster`, `energy_bars` | Live response |
| Claimed amounts | Claim example shows a tenth of the status amounts for ramp-up rewards | Status shows 100 currency, factor 0.1 | Not settled |
| Claim order | Lowest first, as the game UI works | Client sends whichever chest is clicked | — |
