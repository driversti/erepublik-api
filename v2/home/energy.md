# Energy and food

[API docs](../README.md) › Home and daily routine › Energy and food

The **Add Energy** popup: restores energy from food and Energy Bars. The popup is opened from the energy bar in the page header.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Popup template](#popup-template) | GET | `/en/main/energyRefill-refillPopup` | ✅ Verified 2026-11-27 |
| [Energy inventory](#energy-inventory) | POST | `/en/economy/energyRefill-getInventory` | ✅ Verified 2026-11-27 |
| [Refill energy](#refill-energy) | POST | `/en/economy/refillEnergy` | 🔍 From code |
| [Eat button (legacy)](#eat-button-legacy) | POST | `/en/main/eat` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Popup template

`GET /en/main/energyRefill-refillPopup` · Auth required · ✅ Verified 2026-11-27

The HTML of the popup (`#recoverEnergyPopup`, `EnergyPopupController`). The data comes from the inventory request.

```bash
curl 'https://www.erepublik.com/en/main/energyRefill-refillPopup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

**Source:** real request, 2026-11-27; popup `recoverEnergy` / `energyRefillPopup` in `erepublik.js` (version 1795779896).

---

## Energy inventory

`POST /en/economy/energyRefill-getInventory` · Auth required · ✅ Verified 2026-11-27

Your energy pool and the items that can refill it. Read-only.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |

```bash
curl 'https://www.erepublik.com/en/economy/energyRefill-getInventory' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

### Response

```json
{
  "poolEnergy": 8692, "poolLimit": 10620, "recoverableEnergy": 0, "recoverableEnergyBuyFood": 0,
  "minEnergy": 8742, "maxEnergy": 10620, "energyPerInterval": 84,
  "energySources": [ { "type": "energy_bar", "quality": 16, "amount": 60, "energy": 3000, "energyPerItem": 50 } ],
  "buyFood": false, "buyEnergyBars": false,
  "energyStatus": "recovering",
  "energyRecoveryTooltip": "+84 Energy every 6 minutes", "energyRecoveryUntil": "Until Day 6,948, 06:07",
  "energyPoolTooltip": "10,620 Energy", "energyPoolUntil": "Until Day 6,948, 06:07",
  "foodResetAt": 263, "secondsToFullEnergy": 8183
}
```

| Field | Type | Description |
|-------|------|-------------|
| `poolEnergy`, `poolLimit` | number | Current energy and the pool size |
| `energyPerInterval` | number | Passive recovery (every 6 minutes per the tooltip) |
| `recoverableEnergy` | number | Energy that food can restore now |
| `recoverableEnergyBuyFood` | number | Recoverable energy for which you lack food (the popup then offers the marketplace) |
| `minEnergy`, `maxEnergy` | number | Slider limits |
| `energySources[]` | array | `type` (`food`, `energy_bar`), `quality`, `amount`, `energy` (total of the stack) |
| `energySources[].energyPerItem` | number | Present in the response; the client also computes it as `energy / amount` |
| `buyFood`, `buyEnergyBars` | boolean | Show buy buttons |
| `energyStatus` | string | `recovering` observed |
| `foodResetAt` | number | Seconds shown as a countdown until the next food reset |
| `secondsToFullEnergy` | number | Time to a full pool |
| `*Tooltip`, `*Until` | string | Texts about temporary bonuses |

### Errors

A truthy `error` means failure; the client shows `message`.

**Source:** real request, 2026-11-27; `EnergyPopupFactory` and `EnergyPopupController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Refill energy

`POST /en/economy/refillEnergy` · Auth required · 🔍 From code

> [!WARNING]
> Consumes food or Energy Bars.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `energySources[i][quality]` | number | No | Quality of an Energy Bar stack to use |
| `energySources[i][amount]` | number | No | How many of it |

The client first spends food up to `recoverableEnergy` and lists **only Energy Bar stacks** in `energySources`. With food only, the list is empty and jQuery sends no `energySources` field.

```bash
curl 'https://www.erepublik.com/en/economy/refillEnergy' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode '_token=YOUR_CSRF_TOKEN' \
  --data-urlencode 'energySources[0][quality]=16' \
  --data-urlencode 'energySources[0][amount]=1'
```

### Response

The client reads `energyAdded` and adds it to the energy bar.

### Errors

A truthy `error` means failure; the client shows `message`.

### Limits (earlier notes)

- Food has a daily limit (`energyFromFoodRemaining`); Energy Bars bypass it.
- Special food (treats) restores 50 energy per use (`specialFoodAmount`).
- The pool limit is `energyPoolLimit`; `energyPerInterval` is the passive recovery.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `EnergyPopupController.recoverEnergy`; limits — earlier notes.

---

## Eat button (legacy)

`POST /en/main/eat` · Auth required · 🔍 From code

> [!WARNING]
> Consumes food or a treat.

Called by `energy.js` from `#DailyConsumtionTrigger`. That element is not on the current home page, so this may be unused.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `buttonColor` | string | Yes | `blue` for food, `orange` for a treat / Energy Bar |

The client reads `captcha` (opens [session verification](../basics/captcha.md)), `health`, `food_remaining`, `show_reset`, `food_time_reset`, `has_food_in_inventory`, `lowest_quality_food` (`q`, `use`), `hasSpecialFoodItem` and `specialFoodValue`.

**Source:** `energy.js` (version 1719825491).

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Energy Bar `quality` | Not described | `16`, 50 energy each | Live response |
| `foodResetAt` unit | Not described | Shown with a minutes:seconds countdown | Code |
| Order of food use | Not described | The client's estimate starts from the highest quality in the list | Not settled |
