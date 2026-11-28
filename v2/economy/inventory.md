# Inventory and boosters

[API docs](../README.md) › Economy › Inventory

The **Storage** screen: everything you own, activating boosters and houses, and assembling weapons from parts. Selling from storage is on [Marketplace](marketplace.md#post-or-change-an-offer). Boosters used on the battlefield are on [Boosters, bombs and PvP](../military/battlefield/boosters-bombs-pvp.md).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Inventory page](#inventory-page) | GET | `/en/economy/inventory`, `/en/main/inventory` | ✅ Verified 2026-11-27 |
| [Inventory data](#inventory-data) | GET | `/en/economy/inventory-json` | ✅ Verified 2026-11-27 |
| [Activate a booster](#activate-a-booster) | POST | `/en/main/activateBooster` | 🔍 From code |
| [Activate a house](#activate-a-house) | POST | `/en/economy/activateHouse` | 🔍 From code |
| [Assemble](#assemble) | POST | `/en/main/assemble` | 🔍 From code |
| [Build a rocket](#build-a-rocket) | POST | `/en/main/build-rocket` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Inventory page

`GET /en/economy/inventory` · Auth required · ✅ Verified 2026-11-27

HTML page (`StorageController`, `SellItemsController`). `/en/main/inventory` returns the same page. It embeds `SERVER_DATA.storageFilter` (filter tabs: `key` → `i18n`, `tooltip`, `attribute`) and [`SERVER_DATA.sellItems`](marketplace.md#sell-form-data).

**Source:** real request, 2026-11-27.

---

## Inventory data

`GET /en/economy/inventory-json` · Auth required · ✅ Verified 2026-11-27

The whole inventory in one answer, no paging. It can exceed 50 KB.

```bash
curl 'https://www.erepublik.com/en/economy/inventory-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

An array of sections:

| `id` | `title` | Contents |
|------|---------|----------|
| `mainStorage` | Storage | Currencies, goods, raw materials, tokens; has `status` |
| `boosters` | Boosters | Inactive boosters |
| `activeEnhancements` | Active Boosters | Active house and pack boosters |
| `inProduction` | In Production | Partly produced units |
| `assembly` | Assembly | Things you can build from parts |

Shortened:

```json
[
  {
    "id": "mainStorage",
    "title": "Storage",
    "items": [
      {
        "id": "2_1", "industryId": 2, "quality": 1, "name": "Ammunition Q1",
        "type": "groundWeapon", "rarity": "common",
        "icon": "https://www.erepublik.net/images/icons/industry/2/q1_128x128.png",
        "attributes": { "canBeSold": true, "groundWeapon": true, "manufactured": true, "storage": 3, "durability": "1 use", "battleDamageGnd": "+20%" },
        "tooltip": { "attributes": [ { "type": "quantity", "text": "Quantity", "value": 3 } ], "info": null, "help": null },
        "description": "Using Ammunition improves your damage in battles",
        "uses": 3, "amount": 3, "amountDisplay": "3", "amountForSale": 3
      }
    ],
    "status": { "usedStorage": 1000, "totalStorage": 5000, "availableStorage": 4000, "storagePercentage": "20.0%", "color": "green" }
  }
]
```

<details>
<summary>Other sections (sample amounts)</summary>

```json
[
  {
    "id": "boosters", "title": "Boosters",
    "items": [
      {
        "id": "100_air_damage_2_600", "industryId": 100, "quality": 2, "name": "+20% Air Damage",
        "type": "booster", "rarity": "uncommon",
        "attributes": { "booster": "air_damage", "duration": "10m", "durationSeconds": 600, "battleDamageAir": "+20%", "battleDamage": "+20%", "air_damage": 20, "actionable": true },
        "description": "Hit 20% stronger in Air Battles for 10 minutes",
        "amount": 5, "amountDisplay": "5",
        "actionable": {
          "url": "https://www.erepublik.com/en/main/activateBooster",
          "params": { "type": "air_damage", "quality": 2, "duration": 600, "amount": 1 },
          "maxAmount": 5, "confirmType": "inline"
        }
      }
    ]
  },
  {
    "id": "activeEnhancements", "title": "Active Boosters",
    "items": [
      {
        "id": "4_1_active", "industryId": 4, "quality": 1, "name": "House Q1", "type": "house",
        "attributes": {
          "energyPool": "+100",
          "active": { "activeTimeLeft": 100000, "activeUntil": 1700000000, "showProgress": true, "progress": 50, "state": null },
          "overtimePoints": "+1/h", "residenceBonus": "active"
        },
        "amount": 100000, "amountDisplay": "1d"
      }
    ]
  },
  {
    "id": "inProduction", "title": "In Production",
    "items": [
      { "id": "1_3_inProduction", "industryId": 1, "quality": 3, "name": "Food Q3", "type": "food",
        "attributes": { "food": true, "inProduction": true, "component": true, "energy": "+6", "refillValue": "+6" },
        "amount": 0.71, "amountDisplay": "71%" }
    ]
  },
  {
    "id": "assembly", "title": "Assembly",
    "items": [
      {
        "finalProduct": { "id": "2_10", "industryId": 2, "quality": 10, "name": "Bazooka", "type": "groundWeapon",
          "attributes": { "canBeSold": true, "groundWeapon": true, "durability": "3 uses", "fixedDamage": true, "firepowerGnd": "10k" }, "amount": 1 },
        "components": [
          { "id": "collection_part_4", "industryId": 1000, "quality": null, "name": "Bazooka Scope", "type": "miscellaneous",
            "attributes": { "miscellaneous": true, "token": true, "component": true }, "amount": 1 }
        ],
        "buttonText": "Build Bazooka", "canAssemble": true, "componentsPerItem": 1,
        "actionable": { "url": "https://www.erepublik.com/en/main/assemble", "params": { "itemLevel": 1, "isAjax": true } }
      }
    ]
  }
]
```

</details>

### Item fields

| Field | Type | Description |
|-------|------|-------------|
| `id` | string | See [Item IDs](#item-ids) |
| `industryId`, `quality` | number | Industry and quality; `quality` is `null` for tokens and country currency (Gold has `1`) |
| `name`, `description` | string | |
| `type` | string | `currency`, `food`, `groundWeapon`, `airWeapon`, `groundBomb`, `house`, `ticket`, `raw`, `booster`, `miscellaneous`, `vehicle_blueprint` |
| `rarity` | string | `common`, `uncommon`, `rare`, `epic`; earlier notes add `legendary` |
| `icon` | string | Image URL |
| `amount`, `amountDisplay` | number, string | Raw and short form (`2.33M`). In production: progress `0`–`1` |
| `amountForSale` | number | Quantity that can be listed |
| `uses` | number | Remaining uses of weapons |
| `tooltip` | object | `attributes[]` (`type`, `text`, `value`), `info`, `help` |
| `actionable` | object | `url`, `params`, `maxAmount`, `confirmType` — how to activate it |

In the `inProduction` section, earlier notes say production completes when `amount` reaches `1.0`.

### Assembly fields

| Field | Description |
|-------|-------------|
| `finalProduct` | The item that is built |
| `components` | Required parts with the quantities you hold |
| `canAssemble` | Earlier notes: `true` when all parts are available in sufficient quantity |
| `componentsPerItem` | Parts needed per built item |
| `buttonText` | Button label |
| `actionable` | `url` and `params` of the build request |

`mainStorage.status`: `usedStorage`, `totalStorage`, `availableStorage`, `storagePercentage`, `color` (`green`, `yellow`, `red`: low to nearly full).

### Item IDs

| Pattern | Examples |
|---------|----------|
| `{industryId}_{quality}` | `1_3`, `2_7`, `23_5`, `3_5` |
| `{industryId}_{quality}_temporary` | `1_17_temporary`, `2_51_temporary` (expiring items) |
| `{industryId}_{quality}_active`, `_inProduction` | `4_1_active` |
| `raw_{industryId}_1` | `raw_7_1` (FRM), `raw_12_1` (WRM), `raw_17_1` (HRM), `raw_24_1` (ARM) |
| Booster | `100_{booster}_{quality}_{seconds}`; active: `100_{booster}_{value}_active` |
| Currencies | `gold`, `currency`, `game_token`, `loyalty_points`, `amazing_coin` |
| Tokens and parts | `overtime_points`, `factory_reset_token`, `vehicle_unenroll_token`, `collection_part_{n}`, `1000_{n}` |

Industry IDs: 1 Food, 2 Weapons, 3 Moving Tickets, 4 Houses, 7 / 12 / 17 / 24 raw materials, 23 Air weapons, 100 Boosters, 1000 currencies and miscellaneous. Currency IDs (`attributes.currencyId`): 1 country currency in the earlier Lithuanian example, 62 Gold.

### Attributes

| Attribute | Meaning |
|-----------|---------|
| `canBeSold`, `canDonate` | Can be sold on the market; can be donated |
| `currency`, `currencyId` | Currency item |
| `food`, `groundWeapon`, `airWeapon`, `ticket`, `raw`, `miscellaneous`, `token`, `vehicle_blueprint`, `booster` | Category (for boosters, the booster type) |
| `manufactured` | Made in companies |
| `storage`, `highStorage` | Storage used; warning for large stacks |
| `energy`, `refillValue`, `energyRecovery`, `energyPool` | Food and house energy |
| `durability`, `fixedDamage`, `firepowerGnd`, `firepowerAir` | Weapons; fixed damage ignores boosters |
| `battleDamage`, `battleDamageGnd`, `battleDamageAir`, `damage`, `air_damage` | Damage boost |
| `battleInfluence`, `battleInfluenceGnd`, `battleInfluenceAir`, `ground_deploy_influence`, `air_deploy_influence` | Influence boost |
| `militaryRank`, `militaryRankGnd`, `militaryRankAir`, `ground_deploy_rank_points`, `air_deploy_rank_points` | Rank points boost |
| `prestigePoints` / `prestige_points`, `deploySize` / `deploy_size` | Both spellings appear |
| `acceleration`, `speed`, `distance`, `catchup`, `heroReward`, `overtimePoints`, `residenceBonus` | Speed, travel distance, Ghost booster, hero reward, house bonuses |
| `duration`, `durationSeconds` | Booster length |
| `temporary` | `expiresAt` (Unix), `expiresIn`, `progress`, `showProgress`, `state` |
| `active` | `activeTimeLeft`, `activeUntil` (Unix), `showProgress`, `progress` (can exceed 100), `state` |
| `actionable`, `component`, `inProduction`, `armory` | Can be activated; assembly part; being produced; armory item |
| `packBooster`, `power_pack`, `blitzkrieg_pack`, `division_switch_pack`, `catchup` | Pack boosters |

Booster types seen 2026-11-27: `damage`, `air_damage`, `speed`, `catchup`, `deploy_size`, `prestige_points`, `ground_deploy_influence`, `air_deploy_influence`, `ground_deploy_rank_points`, `air_deploy_rank_points`.

**Source:** real request, 2026-11-27; earlier notes.

---

## Activate a booster

`POST /en/main/activateBooster` · Auth required · 🔍 From code

> [!WARNING]
> Consumes the booster. It can replace an active one of the same type, losing the remaining time.

Send `actionable.params` of the item to `actionable.url`.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `type` | string | Yes | Booster type |
| `quality` | number | Yes | Strength |
| `duration` | number | Yes | Seconds |
| `amount` | number | Yes | `1` |
| `unit`, `value` | string, number | No | The client adds the duration for display: `s`, `m`, `h`, `d` |
| `confirmExtendBooster` | string | No | `yes` to confirm replacing or extending an active booster |

```bash
curl 'https://www.erepublik.com/en/main/activateBooster' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'type=speed&quality=2&duration=300&amount=1&_token=YOUR_CSRF_TOKEN'
```

### Response

The client treats a truthy `error` as failure. Earlier notes:

```json
{
  "error": true,
  "status": "error",
  "message": "Please confirm activating this booster",
  "confirmationRequired": true,
  "confirmationText": "You already have another active item of this type.<br><br>\n Are you sure you want to activate this item?"
}
```

```json
{ "status": "success", "message": "Booster activated" }
```

Earlier notes: boosters of different types run together, the same type does not stack. Quality: speed 1 = ×2, 2 = ×5, 3 = ×9; damage 5 = +50%, 10 = +100%, 15 = +150%. Durations from 60 to 86400 seconds.

**Source:** `StorageController.toggleConfirmation` in `erpk_angular_controllers.js` (version 1795779904); responses from earlier notes.

---

## Activate a house

`POST /en/economy/activateHouse` · Auth required · 🔍 From code

> [!WARNING]
> Consumes a house and charges an activation tax. Earlier notes: it replaces the active house and the remaining time is lost.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `type` | string | Yes | `house` |
| `quality` | number | Yes | 1–5 |
| `amount` | number | Yes | `1` |

```bash
curl 'https://www.erepublik.com/en/economy/activateHouse' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'type=house&quality=1&amount=1&_token=YOUR_CSRF_TOKEN'
```

### Response

Earlier notes:

```json
{ "status": true, "message": "Successfully activated house", "activationTax": 100, "currency": 0, "error": false }
```

`activationTax` is in country currency and depends on country and quality; `currency` is the balance afterwards (earlier notes give it in hundredths, see [Open questions](#open-questions)).

In the current scripts a house is activated from the city page's residence panel: the server supplies `activationData.url` and `activationData.params`, and the client posts them with `_token`, `url` and `panel`. No house in the observed inventory carried an `actionable` object.

Earlier notes on effects: Q1–Q5 give +100, +200, +300, +400, +500 energy pool (see [Open questions](#open-questions)), +1 overtime point per hour, and last from about 7 to 30 days.

### Errors

The client treats a falsy `status` as failure and shows `message`. Earlier notes list these cases, without response bodies:

- Not enough currency for the activation tax
- No house of that quality in the inventory
- Invalid CSRF token

**Source:** `erpk_angular_controllers.js` (version 1795779904), `activateHouse`; parameters, response and error cases from earlier notes.

---

## Assemble

`POST /en/main/assemble` · Auth required · 🔍 From code

> [!WARNING]
> Consumes the parts.

Builds a Bazooka from its five parts (`collection_part_2`–`6`: Stock, Barrel, Scope, Projectile, Trigger Kit), one of each (`componentsPerItem: 1`).

| Name | Type | Description |
|------|------|-------------|
| `_token` | string | CSRF token |
| `itemLevel` | number | From `actionable.params` (`1`) |
| `isAjax` | boolean | `true` |

A truthy `error` means failure; the client shows `message`.

**Source:** `inventory-json` `actionable` data, 2026-11-27; `StorageController.assembleItem`.

---

## Build a rocket

`POST /en/main/build-rocket` · Auth required · 🔍 From code

> [!WARNING]
> Consumes ammunition.

The observed entry builds "Rocket Q2" from Q1–Q6 ammunition, `componentsPerItem: 20`.

| Name | Type | Description |
|------|------|-------------|
| `_token` | string | CSRF token |
| `what` | string | `build_rocket` |
| `isAjax` | boolean | `true` |

**Source:** `inventory-json` `actionable` data, 2026-11-27; `StorageController.assembleItem`.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Booster confirmation | Parameter unknown | `confirmExtendBooster=yes` | Code |
| House energy pool | +100 … +500 | Marketplace data: Q2 160, Q3 200, Q4 300, Q5 400 ([table](marketplace.md#product-effects)) | Not settled |
| House activation | From the inventory | Residence panel; no `actionable` on houses | Not settled |
| Unit of `currency` after house activation | Hundredths | The client passes it to its currency display; unit not checked | Not settled |
| `expiresAt` in booster answers | Mentioned | Not in the examples | — |
