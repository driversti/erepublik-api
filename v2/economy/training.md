# Training

[API docs](../README.md) › Economy › Training

The Training Grounds screen: four buildings that give strength once a day. A building can be upgraded or downgraded for Gold. Every 250 strength earns a Super Soldier medal (earlier notes: it rewards Gold). Training resets at 00:00 game time.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Training page](#training-page) | GET | `/en/economy/training-grounds` | ✅ Verified 2026-11-27 |
| [Grounds data](#grounds-data) | GET | `/en/main/training-grounds-json` | ✅ Verified 2026-11-27 |
| [Train](#train) | POST | `/en/economy/train` | 🔍 From code |
| [Upgrade options](#upgrade-options) | POST | `/en/main/upgrade-popup-json` | ✅ Verified 2026-11-27 |
| [Upgrade a building](#upgrade-a-building) | POST | `/en/economy/upgrade-train` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). New training grounds are built with [Build](companies.md#build).

---

## Training page

`GET /en/economy/training-grounds` · Auth required · ✅ Verified 2026-11-27

HTML shell; the buildings come from [Grounds data](#grounds-data). Page variables include `grounds`, `has_captcha`, `reCaptchaVersion`, `captcha_url` ([captcha](work.md#submit-a-captcha)), `train_url`, `upgrade_url`, `food_remaining`, `promoMultiplier`, `hasCondition`, `has_pin` and `TRAIN_TEXTS`.

```bash
curl 'https://www.erepublik.com/en/economy/training-grounds' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real request, 2026-11-27.

---

## Grounds data

`GET /en/main/training-grounds-json` · Auth required · ✅ Verified 2026-11-27

Your buildings, which of them you already used, costs, energy and Gold.

```bash
curl 'https://www.erepublik.com/en/main/training-grounds-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened to one building:

```json
{
  "message": "",
  "upgradeBanner": { "discount": 50, "timeLeft": 50995 },
  "bfUpgradeBanner": "",
  "contracts": false,
  "progress": 75,
  "can_train": false,
  "grounds": [
    {
      "id": 111111, "trained": true, "training": 50995, "anniversary": false,
      "name": "Climbing Center - Quality 4",
      "icon": "//www.erepublik.net/images/modules/myland/buildings/train_1_q4.png",
      "quality": 4, "cost": 0.19, "effectiveCost": 0, "strength": 10, "default": false
    }
  ],
  "page_details": {
    "health_tooltip": "Energy required",
    "gold_tooltip": "Gold cost",
    "health": 500,
    "recoverable_health": { "value": 0 },
    "health_warn": "Food will be consumed, make sure you have enough food in your Storage",
    "health_warn_limit": "Energy limit exceeded, you will be able to train only if you have Energy Bars",
    "gold": 0,
    "gold_warn": "Not enough Gold to train",
    "recoverable_health_in_food": 1000,
    "energy": 1000
  },
  "canTrain": false,
  "hasFreeTrain": true,
  "hasTrainingContract": false,
  "contractExpiresAt": 0,
  "status": true,
  "error": false
}
```

| Field | Type | Description |
|-------|------|-------------|
| `grounds[].id` | number | Building ID; unique per citizen |
| `grounds[].trained` | boolean | Already used |
| `grounds[].training` | number | The client counts it down in `H:M:S` as the time until the building is free again |
| `grounds[].anniversary` | boolean | Anniversary building (own timer) |
| `grounds[].quality` | number | 1–4 |
| `grounds[].cost`, `effectiveCost` | number | Gold price, and the price after discounts |
| `grounds[].strength` | number | Strength gained |
| `grounds[].default` | boolean | Preselected by the client |
| `progress` | number | Strength towards the next medal (250) |
| `can_train`, `canTrain` | boolean | Whether you can still train today |
| `upgradeBanner` | object \| string | Upgrade discount: `discount` (percent), `timeLeft` (seconds); `""` when none |
| `bfUpgradeBanner` | number \| string | Countdown of a Black Friday banner |
| `contracts` | array \| boolean | Discount contracts, or `false` |
| `contracts[].discount`, `active`, `timeLeft` | number, boolean, number | Percent off, whether active, seconds left |
| `hasFreeTrain` | boolean | A free training is available |
| `hasTrainingContract`, `contractExpiresAt` | boolean, number | Contract flag and Unix expiry (`0` if none) |
| `page_details.health` | number | Current energy |
| `page_details.recoverable_health_in_food` | number | Energy value of stored food |
| `page_details.energy` | number | Energy the client counts on top for energy bars |
| `page_details.gold` | number | Gold balance |
| `page_details.*_tooltip`, `*_warn*` | string | Interface texts |

Earlier notes (verified 2026-07-16): `effectiveCost = cost × (1 − discount / 100)`; cost `0.19` with a 90% contract gives `0.019`. Free building: Weights Room; the others cost 0.19, 0.89 and 1.79 Gold.

| Building | Observed `strength` (Q4) | `cost` |
|----------|--------------------------|--------|
| Weights Room | 20 | 0 |
| Climbing Center | 10 | 0.19 |
| Shooting Range | 20 | 0.89 |
| Special Forces Center | 40 | 1.79 |

**Source:** real request, 2026-11-27; `ErpkTrainingGroundController` in `erpk_angular_controllers.js` (version 1795779904); earlier notes.

---

## Train

`POST /en/economy/train` · Auth required · 🔍 From code

> [!WARNING]
> Spends 10 energy per building and may spend Gold. Every building in the request is trained.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `grounds[N][id]` | number | Yes | Building ID; `N` starts at 0 |
| `grounds[N][train]` | number | Yes | `1` |
| `useEnergyBar` | string | No | `yes` to eat energy bars |

> [!WARNING]
> Earlier notes (verified 2026-02-28): the server ignores `train=0`. Leave out the buildings you do not want to train.

```bash
curl 'https://www.erepublik.com/en/economy/train' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode 'grounds[0][id]=111111' \
  --data-urlencode 'grounds[0][train]=1' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

### Response

Earlier notes:

```json
{
  "status": true,
  "message": true,
  "result": {
    "strength_bonus": 90, "xp": 8, "health": 40, "gold": 0, "strength": 10000,
    "consumed_summary": [], "to_achievement": 83,
    "to_achievement_text": "You need an additional <span id=\"train_to_achievement\">83 Strength points</span> to get the Super Soldier Medal.",
    "daily_tasks_done": true
  },
  "error": false
}
```

| Field | Type | Description |
|-------|------|-------------|
| `result.strength_bonus` | number | Strength gained |
| `result.strength` | number | Total strength |
| `result.xp` | number | Experience gained |
| `result.health`, `result.gold` | number | Energy and Gold spent. Earlier notes: `gold` is `0` for a free training or with an active training contract, since contracts eliminate Gold costs |
| `result.consumed_summary` | array \| object | Food eaten; the client reads key `10` as energy consumed and other keys as food quality |
| `result.to_achievement` | number | Strength left to the next medal |
| `result.daily_tasks_done` | boolean | Daily tasks finished |

### Errors

| `message` | Meaning |
|-----------|---------|
| `captcha` | Resend through [Submit a captcha](work.md#submit-a-captcha) with `action_type=train` |
| `money` | Not enough money |
| `not_enough_health`, `not_enough_health_food` | `result.health_limit`, `result.health_needed` |
| `not_enough_gold_error` | `result.goldCost`, `result.gold` |
| `nothing_selected` | Raised by the client itself |

**Source:** `ErpkTrainingGroundController` (version 1795779904); `citizen/train.js` (version 1756118596), `checkResponse`; example and warning from earlier notes.

---

## Upgrade options

`POST /en/main/upgrade-popup-json` · Auth required · ✅ Verified 2026-11-27

The levels of one building, shown in the upgrade popup. Read-only.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `buildingId` | number | Yes | `grounds[].id` |
| `type` | string | Yes | `trainingGrounds` |

```bash
curl 'https://www.erepublik.com/en/main/upgrade-popup-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'buildingId=111111&type=trainingGrounds&_token=YOUR_CSRF_TOKEN'
```

### Response

A Q4 building during a 50% promotion:

```json
{
  "upgrades": [
    { "strength": 5,  "cost": 42.5, "initialCost": 85, "img": "//www.erepublik.net/images/modules/manager/train_upgrades/training_grounds_q1.png", "type": "downgrade" },
    { "strength": 10, "cost": 37.5, "initialCost": 75, "img": "…/training_grounds_q2.png", "type": "downgrade" },
    { "strength": 15, "cost": 25,   "initialCost": 50, "img": "…/training_grounds_q3.png", "type": "downgrade" },
    { "strength": 20, "cost": 0,    "initialCost": 0,  "img": "…/training_grounds_q4.png", "type": "current" }
  ]
}
```

| Field | Type | Description |
|-------|------|-------------|
| `upgrades[]` | array | One per level, lowest first |
| `strength` | number | Strength at that level |
| `cost`, `initialCost` | number | Gold amount shown, and the amount before the discount. For downgrades presumably the refund |
| `type` | string | `downgrade`, `current`; higher levels are selectable upgrades |

### Errors

A wrong parameter answers with `text/html` content type and a JSON body:

```json
{ "error": "Missing or incorrect building id" }
```

**Source:** real requests, 2026-11-27; `ErpkUpgradeLandsPopupFactory` (version 1795779904).

---

## Upgrade a building

`POST /en/economy/upgrade-train` · Auth required · 🔍 From code

> [!WARNING]
> Changes the building level; an upgrade spends Gold.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `type` | string | Yes | `upgrade_train` |
| `buildingId` | number | Yes | `grounds[].id` |
| `level` | number | Yes | Target level |
| `pin` | string \| boolean | Yes | Safety code for a downgrade when the account has one, otherwise `false` |
| `shownCost` | number | Yes | The price displayed to the player |

### Response

| `status` | `message` | Client behaviour |
|----------|-----------|------------------|
| `true` | Object with `remainingGold` (among what the success popup shows) | Refreshes the data and shows a success popup |
| `false` | `not_enough_gold` | Shows the not enough Gold error |
| — | `INVALID_PIN` | Wrong safety code |
| — | `PIN_ATTEMPTS` | Too many safety code attempts |

**Source:** `erpk_angular_directives.js` (version 1795779904), upgrade popup directive.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| `grounds[].training` | Game day of last use | Counted down as a timer | Code |
| `page_details.energy` | Maximum energy capacity | Added to recoverable energy in the energy bar check | Code |
| `result.health` | Consumed or restored | Shown as `-N` | Code |
| Free training energy | Free training consumes none | Client estimates 10 per building | Not settled |
