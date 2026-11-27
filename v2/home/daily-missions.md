# Daily missions

[API docs](../README.md) › Home and daily routine › Daily missions

The missions in the Daily Activities popup. Completing and claiming one gives Prestige Points (for the [weekly challenge](weekly-challenge.md)) and Activity Points (for [daily objectives](daily-objectives.md)).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Mission list](#mission-list) | POST | `/en/main/daily-missions-data` | ✅ Verified 2026-11-27 |
| [Claim a mission](#claim-a-mission) | POST | `/en/main/mission-solve` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Mission list

`POST /en/main/daily-missions-data` · Auth required · ✅ Verified 2026-11-27

Today's missions with requirements, progress and rewards. Read-only. The popup calls it together with [objective status](daily-objectives.md#objective-status).

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |

```bash
curl 'https://www.erepublik.com/en/main/daily-missions-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

### Response

Shortened to one mission:

```json
{ "missions": [ { "id": 100005, "title": "Spend energy", "type": "daily",
  "progress": [ { "completed": false, "requirement": "SpendEnergy", "partial": "400 / 1000" } ],
  "rewards": { "prestige_points": { "amount": 45 }, "daily_objective_progress": { "amount": 15 } } } ] }
```

<details>
<summary>Full mission object</summary>

```json
{
  "id": 100005,
  "title": "Spend energy",
  "description": "Consume 1000 Energy",
  "type": "daily",
  "priority": 100005,
  "is_active": 1,
  "starts_at": "2022-04-07 00:00:00",
  "ends_at": null,
  "created_at": 1649240438,
  "updated_at": "2022-04-06 03:20:38",
  "start_conditions": [],
  "gold_unlock": [],
  "citizen_data": { "mission_id": 100005, "started_at": 1795767606, "finished_at": null },
  "requirements": {
    "SpendEnergy": { "type": "SpendEnergy", "threshold": 1000, "displayThreshold": 1000, "text": "Spend 1000 Energy" }
  },
  "progress": [ { "completed": false, "requirement": "SpendEnergy", "partial": "400 / 1000" } ],
  "rewards": {
    "prestige_points": { "type": "prestige_points", "name": "Prestige Points",
      "iconUrl": "/images/modules/_icons/reward_prestige_points.png", "amount": 45, "displayValue": 45 },
    "daily_objective_progress": { "type": "daily_objective_progress", "name": "Activity Points",
      "amount": 15, "displayValue": 15 }
  },
  "hints": { "SpendEnergy": { "requirement": "SpendEnergy", "text": "", "link": "" } },
  "hint": { "hints": { "SpendEnergy": "" }, "links": { "SpendEnergy": false } }
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `missions[].id` | number | Sent as `missionId` |
| `missions[].title`, `description`, `type` | string | `type` is `daily` |
| `missions[].priority` | number | Sort order |
| `missions[].is_active` | number | `1` when active |
| `missions[].citizen_data` | object | `started_at`, `finished_at` (Unix time; `null` until claimed, per earlier notes) |
| `missions[].requirements` | object | Per requirement: `threshold`, `displayThreshold`, `text` |
| `missions[].progress[]` | array | `requirement`, `completed`, `partial` (`"2 / 3"`) |
| `missions[].rewards` | object | `prestige_points`, `daily_objective_progress` |
| `missions[].hints`, `hint` | object | Tips; `hint.links` holds navigation links (`url`, `link`, `is_land`, `is_battle`) or `false` |
| `missions[].gold_unlock` | array | Empty on standard missions |

The client offers **Claim** when every `progress[]` entry is `completed`.

### Missions observed (2026-11-27)

| ID | Requirement | Threshold | Prestige | Activity |
|----|-------------|-----------|----------|----------|
| 100005 | `SpendEnergy` | 1000 | 45 | 15 |
| 100006 | `SpendFuel` | 3 | 15 | 5 |
| 100007 | `DeployEnergyMeaningful` (Campaign of the Day or Daily Order) | 250 | 30 | 10 |
| 100008 | `WinHeroMedals` | 1 | 45 | 15 |
| 100009 | `ActivateBooster` | 1 | 15 | 5 |
| 100015 | `FoodRawProduced` | 10 | 6 | 2 |
| 100016 | `WeaponsRawProduced` | 10 | 9 | 3 |
| 100017 | `HousesRawProduced` | 1 | 15 | 5 |
| 100018 | `AircraftRawProduced` | 1 | 15 | 5 |
| 100019 | `FoodProduced` | 200 | 6 | 2 |
| 100020 | `WeaponsProduced` | 20 | 9 | 3 |
| 100021 | `HousesProduced` | 1 | 45 | 15 |
| 100022 | `AircraftWeaponsProduced` | 5 | 15 | 5 |
| 100023–100026 | `AssignEmployees` | 1, 2, 3, 5 | 6, 12, 18, 30 | 2, 4, 6, 10 |

The client also knows `Work`, `WorkDays`, `TrainedBuildings`, `FightSpecialWeapon` (bombs or rockets), `DeployWithMarketWeapon` (Q1–Q7 weapons), `Deploy250EnergyMeaningful`, `TravelTimes`, `BuyMarketplaceItems` and `TradeOnMonetaryMarketTimes`. Earlier notes: some missions exclude each other.

**Source:** real request, 2026-11-27; `DailyMissionsController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Claim a mission

`POST /en/main/mission-solve` · Auth required · 🔍 From code

> [!WARNING]
> Claims the reward on your account. Once per mission per day.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `missionId` | number | Yes | Mission `id` |

```bash
curl 'https://www.erepublik.com/en/main/mission-solve' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'missionId=100016&_token=YOUR_CSRF_TOKEN'
```

### Response

Earlier notes:

```json
{
  "finished": true,
  "rewards": {
    "prestige_points": { "name": "Prestige Points", "type": "prestige_points", "amount": 9 },
    "daily_objective_progress": { "name": "Activity Points", "type": "daily_objective_progress", "amount": 3 }
  }
}
```

An incomplete mission returns `{ "finished": false }`.

### Errors

A truthy `error` means failure; the client shows `errorMessage`.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `DailyMissionsFactory.claimMission`; examples — earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| `SpendEnergy` | Not listed | Present | Live response |
| Reward ranges | Prestige 6–45, Activity 2–15 | The same | — |
| `mission-check`, `mission-unlock` | Not described | Called by `missions.js`, which is not loaded on the home page; it also calls `mission-solve` | Not documented |
