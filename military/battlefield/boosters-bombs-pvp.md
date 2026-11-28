# Boosters, bombs and PvP

[API docs](../../README.md) › Military › Battlefield › Boosters, bombs and PvP

The item buttons of the battlefield: boosters, bombs and missiles, seasonal battle effects, the older player-versus-player (PvP) duel screen with its own equipment, and combat order subscriptions. The item lists come from the [battlefield page](page.md#battlefield-page).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Activate booster](#activate-booster) | POST | `/en/military/fight-activateBooster` | 🔍 From code |
| [Deploy bomb](#deploy-bomb) | POST | `/en/military/deploy-bomb` | 🔍 From code |
| [Activate battle effect](#activate-battle-effect) | POST | `/en/main/fight-activateBattleEffect` | 🔍 From code |
| [Join PvP queue](#join-pvp-queue) | POST | `/en/main/pvp-queue` | 🔍 From code |
| [PvP inventory](#pvp-inventory) | GET | `/en/military/pvp-inventory/` | ✅ Verified 2026-11-27 |
| [PvP inventory actions](#pvp-inventory-actions) | POST | `/en/main/pvp-inventory-actions` | 🔍 From code |
| [Subscribe to combat order](#subscribe-to-combat-order) | POST | `/en/main/mercenary-campaign/subscribe/{id}` | 🔍 From code |
| [Unsubscribe from combat order](#unsubscribe-from-combat-order) | POST | `/en/main/mercenary-campaign/unsubscribe/{id}` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../../guides/requests.md#common-headers). Status labels are explained [there too](../../guides/requests.md#status-labels).

---

## Boosters on the battlefield page

`SERVER_DATA.boosters`:

| Field | Description |
|-------|-------------|
| `active` | Boosters running now; empty array when none |
| `inactive` | Boosters you can activate: category → items |
| `events` | Event boosters |
| `canUseCatchupBooster`, `catchupBoosterTextDetails` | Whether the Ghost Booster can be used, and an extra tooltip when it cannot |

`SERVER_DATA` also carries `canUseCatchupBooster`, `regimentBoosters`, `regimentBooster`, `eventDamageBoosters` and `eventDamageBooster`.

### Categories

| Category | Effect | Value attribute | Observed on |
|----------|--------|-----------------|-------------|
| `damage` | Ground damage | `damageBoost` | ground |
| `air_damage` | Air damage | `damageBoost` | air |
| `speed` | Faster hits | `damageAcceleration` (`"x2"`) | ground, air |
| `catchup` | Ghost Booster: adds a share of the best damage | `damageBoost` | ground |
| `prestige_points` | Extra prestige per hit | `prestigeBoost` | ground, air |
| `deploy_size` | Bigger [deployment](deployment.md) | `deploySizeBoost` | ground, air |
| `ground_deploy_influence`, `air_deploy_influence` | Deployment influence | `deployInfluenceBoost` | own battle type |
| `ground_deploy_rank_points`, `air_deploy_rank_points` | Deployment rank points | `deployRankPointsBoost` | own battle type |
| `shadow_fighter`, `event_damage`, `hero_reward` | Named in the client only | `damageBoost`, `heroRewardBoost` | not observed |

Any other category is reported by the client as unknown. What `hero_reward` does: see [Battle effects](live-data.md#battle-effects).

### Booster item

<details>
<summary>Example item</summary>

```json
{
  "name": "x2 Damage Accelerator",
  "id": "100_speed_1_180",
  "industryId": 100,
  "quality": 1,
  "amount": 0,
  "activable": 1,
  "deactivable": 0,
  "activableFromInventory": 1,
  "activableFromBattlefield": 1,
  "activationData": {
    "tooltip": "Hit 2 times faster for 3 minutes",
    "url": "/en/military/fight-activateBooster",
    "params": { "type": "speed", "quality": 1, "duration": 180 }
  },
  "active": 0,
  "icon": "https://www.erepublik.net/images/icons/boosters/52px/speed.png?1702893011",
  "tooltip": "Hit 2 times faster for 3 minutes",
  "token": "",
  "attributes": {
    "damageAcceleration": { "id": "damageAcceleration", "name": "Hit 2 times faster for 3 minutes", "type": "use", "value": "x2" },
    "duration": { "id": "duration", "name": "Duration", "type": "minutes", "value": 180 }
  },
  "isRaw": 0,
  "isPartial": 0,
  "isBooster": 1,
  "isBomb": 0,
  "isPackBooster": 0,
  "isExpirable": 0,
  "type": "speed",
  "duration": 180,
  "canActivateBooster": 1
}
```

</details>

- The ID is `{industryId}_{type}_{quality}_{duration}`.
- `duration` is in seconds, even where the attribute says `type: "minutes"`.
- `activationData` holds the URL and parameters to send.
- `canActivateBooster` is the server's permission flag. The client also disables an item whose `quality` is lower than the active one of the same category.

### Known boosters

<details>
<summary>ID list</summary>

| ID | Name | Effect |
|----|------|--------|
| `100_catchup_30_60` | +30% Ghost Booster | +30% of best damage for 1 min |
| `100_damage_5_86400` | +50% Ground Damage | +50% for 24h |
| `100_damage_5_28800` | +50% Ground Damage | +50% for 8h |
| `100_damage_5_7200` | +50% Ground Damage | +50% for 2h |
| `100_damage_10_86400` | +100% Ground Damage | +100% for 24h |
| `100_damage_10_28800` | +100% Ground Damage | +100% for 8h |
| `100_damage_10_7200` | +100% Ground Damage | +100% for 2h |
| `100_damage_10_3600` | +100% Ground Damage | +100% for 1h |
| `100_damage_10_600` | +100% Ground Damage | +100% for 10 min |
| `100_damage_15_600` | +150% Ground Damage | +150% for 10 min |
| `100_speed_1_180` | x2 Damage Accelerator | x2 speed for 3 min |
| `100_speed_2_600` | x5 Damage Accelerator | x5 speed for 10 min |
| `100_speed_2_300` | x5 Damage Accelerator | x5 speed for 5 min |
| `100_speed_3_600` | x9 Damage Accelerator | x9 speed for 10 min |
| `100_prestige_points_1_600` | +1 Prestige Points | +1 prestige for 10 min |
| `100_deploy_size_100_600` | +100% Deploy Size | +100% for 10 min |
| `100_deploy_size_200_1200` | +200% Deploy Size | +200% for 20 min |
| `100_deploy_size_200_600` | +200% Deploy Size | +200% for 10 min |
| `100_deploy_size_500_600` | +500% Deploy Size | +500% for 10 min |
| `100_ground_deploy_influence_1_600` | +10% Ground Influence | +10% for 10 min |
| `100_ground_deploy_influence_2_1200` | +20% Ground Influence | +20% for 20 min |
| `100_ground_deploy_influence_2_600` | +20% Ground Influence | +20% for 10 min |
| `100_ground_deploy_influence_3_600` | +30% Ground Influence | +30% for 10 min |
| `100_ground_deploy_rank_points_1_600` | +10% Ground Rank Points | +10% for 10 min |
| `100_ground_deploy_rank_points_2_1200` | +20% Ground Rank Points | +20% for 20 min |
| `100_ground_deploy_rank_points_2_600` | +20% Ground Rank Points | +20% for 10 min |
| `100_ground_deploy_rank_points_3_600` | +30% Ground Rank Points | +30% for 10 min |
| `100_air_damage_2_600` | +20% Air Damage | +20% for 10 min |
| `100_air_damage_5_600` | +50% Air Damage | +50% for 10 min |
| `100_air_damage_5_300` | +50% Air Damage | +50% for 5 min |
| `100_air_damage_10_600` | +100% Air Damage | +100% for 10 min |
| `100_air_damage_10_300` | +100% Air Damage | +100% for 5 min |
| `100_air_deploy_influence_1_600` | +10% Air Influence | +10% for 10 min |
| `100_air_deploy_influence_2_1200` | +20% Air Influence | +20% for 20 min |
| `100_air_deploy_influence_2_600` | +20% Air Influence | +20% for 10 min |
| `100_air_deploy_influence_3_600` | +30% Air Influence | +30% for 10 min |
| `100_air_deploy_rank_points_1_600` | +10% Air Rank Points | +10% for 10 min |
| `100_air_deploy_rank_points_2_1200` | +20% Air Rank Points | +20% for 20 min |
| `100_air_deploy_rank_points_2_600` | +20% Air Rank Points | +20% for 10 min |
| `100_air_deploy_rank_points_3_600` | +30% Air Rank Points | +30% for 10 min |

</details>

Collected from earlier notes and the 2026-11-27 pages; not a complete catalogue.

---

## Activate booster

`POST /en/military/fight-activateBooster` · Auth required · 🔍 From code

> [!WARNING]
> Consumes a booster.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../../guides/requests.md#csrf-token) |
| `type` | string | Yes | [Category](#categories), from `activationData.params` |
| `quality` | number | Yes | From `activationData.params` |
| `duration` | number | Yes | Seconds, from `activationData.params` |
| `battleId` | number | Yes | Battle ID |
| `battleZoneId` | number | Yes | Zone ID |
| `sideId` | number | Yes | `SERVER_DATA.mySideCountryId` |
| `confirmExtendBooster` | string | No | `yes` after confirming that a booster of this type is already active |

```bash
curl 'https://www.erepublik.com/en/military/fight-activateBooster' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&type=speed&quality=1&duration=180&battleId=958706&battleZoneId=42911251&sideId=72'
```

| Field | Type | Description |
|-------|------|-------------|
| `status` | string | `success`, otherwise a failure (`error`) |
| `boosters` | object | On success: new `active` and `inactive` lists |
| `message` | string | Error key or text |
| `confirmationRequired` | boolean | Resend with `confirmExtendBooster=yes` after confirming |
| `confirmationText` | string | Question to show; optional |

The inventory screen activates items through their own `actionable.url` and `actionable.params`, with the same confirmation flow.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `SpecialItemsController.activateBooster`, `SpecialItemsFactory`; item structure from battlefield pages, 2026-11-27.

---

## Bombs on the battlefield page

`SERVER_DATA.specialWeapons` is keyed `{industryId}_{quality}`; it is an empty **array** when there is nothing to show.

<details>
<summary>Example item</summary>

```json
{
  "2_22": {
    "name": "Big Bomb",
    "id": "2_22",
    "industryId": 100,
    "quality": 22,
    "amount": 0,
    "activable": 1,
    "deactivable": 0,
    "activableFromInventory": 0,
    "activableFromBattlefield": 1,
    "activationData": { "tooltip": "", "url": "/en/military/deploy-bomb", "params": { "bombId": 22 } },
    "active": 0,
    "icon": "/images/icons/industry/999/22.png",
    "tooltip": "Big Bomb",
    "token": "booster_q22",
    "attributes": {
      "firePower": { "id": "firePower", "name": "Fire power", "type": "use", "value": 5000000 },
      "durability": { "id": "durability", "name": "Durability", "type": "uses", "value": 1 },
      "expirationInfo": { "id": "expirationInfo", "type": "expirationInfo", "value": ["Expires on Day 6,969, 07:34"] }
    },
    "isRaw": 0,
    "isPartial": 0,
    "isBooster": 0,
    "isBomb": 1,
    "isPackBooster": 0,
    "isExpirable": 1,
    "type": "bomb",
    "is_expirable": 1
  }
}
```

</details>

| Quality (`bombId`) | Item | Damage | Disabled by |
|--------------------|------|--------|-------------|
| `21` | Small Bomb | 1,500,000 | `specialWeaponsDisabled` |
| `22` | Big Bomb | 5,000,000 | `bigBombsDisabled` |
| `215` | Cruise Missile | 1,500,000 | `cruiseMissilesDisabled` |
| `211`–`213` | Handled like the Small Bomb | not observed | `specialWeaponsDisabled` |
| `216` | Handled like a big bomb | not observed | `bigBombsDisabled` |
| `26`–`29`, `291` | "Factory rockets" | not observed | `specialWeaponsDisabled` |

All three flags were `true` on an air zone and `false` on ground zones. `rocket_damage` (1500000) and `bomb_damage` (5000000) are sent too.

---

## Deploy bomb

`POST /en/military/deploy-bomb` · Auth required · 🔍 From code

> [!WARNING]
> Consumes a bomb or missile.

One request launches one item. "Carpet bombing" repeats it for the amount you pick, one request at a time and at least 1.1 s apart.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../../guides/requests.md#csrf-token) |
| `battleId` | number | Yes | Battle ID |
| `battleZoneId` | number | Yes | Zone ID |
| `bombId` | number | Yes | From `activationData.params` |
| `sideId`, `sideCountryId` | number | See note | `SERVER_DATA.mySideCountryId`; sent by the current panel, not by the older handler |

```bash
curl 'https://www.erepublik.com/en/military/deploy-bomb' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&battleId=958706&battleZoneId=42911251&bombId=22&sideId=72&sideCountryId=72'
```

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | Failure flag |
| `message` | string | Error code |
| `bomb.damage` | number | Damage done |
| `bomb.type` | string | `rocket`, `small_bombs`, `cruise_missle` (sic) or `big_bomb` |
| `bomb.uses` | number | Remaining count shown on the button |
| `bomb.booster_id`, `bomb.old_damage` | number | Used when another item than the requested one was launched |
| `reward` | object | `status`, `reward`; added to the reward counter |
| `details.currency` | number | New currency balance, read when `reward` is present |
| `hits` | number | Prestige points gained |
| `specialWeapons` | array \| object | Empty array when nothing is left |
| `fuelLeft` | number | New fuel value, when sent |

### Errors

| `message` | Client reaction |
|-----------|-----------------|
| `BATTLE_WON`, `INVALID_BATTLE_ID` | Stops: invalid battle |
| `NON_BELLIGERENT`, `ZONE_INACTIVE` | Stops: invalid zone |
| `UNKNOWN_SIDE`, `CHANGE_LOCATION` | Stops: invalid side |
| `BOMBS_DISABLED` | Stops: bombs disabled |
| `INVALID_BOMB` | Stops: invalid bomb |
| `LOCKED` | Ignored; the next attempt follows |

**Source:** `erpk_angular_controllers.js` (version 1795779904), `SpecialItemsController.carpetBomb`, `deployBomb`; `battle_new.js` (version 1795779886), `hit_bomb`, `handleHitBombResponse`; damage values from battlefield pages, 2026-11-27.

---

## Activate battle effect

`POST /en/main/fight-activateBattleEffect` · Auth required · 🔍 From code

> [!WARNING]
> Activates a seasonal effect for the battle; it may consume an event item.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../../guides/requests.md#csrf-token) |
| `citizenId` | number | Yes | Your citizen ID |
| `battleId` | number | Yes | Battle ID |
| `type` | string | Yes | `snowFight` or `valentines` |

```bash
curl 'https://www.erepublik.com/en/main/fight-activateBattleEffect' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&citizenId=1000001&battleId=958706&type=snowFight'
```

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | Failure flag |
| `error_msg` | string | Error text |
| `snowFight` or `valentinesDay` | object | `is_active`, `citizen_id`, `citizen_name`, `time_left` |

The state is also in `SERVER_DATA.battle_effects` and [live data](live-data.md#battle-effects). Both effects were inactive and no button was found on the observed pages.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `SnowFightController`, `ValentinesController` and their factories.

---

## Join PvP queue

`POST /en/main/pvp-queue` · Auth required · 🔍 From code

> [!WARNING]
> Puts you into the duel queue; a duel starts when an opponent is found.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../../guides/requests.md#csrf-token) |
| `battleId` | number | Yes | Battle ID |
| `battleZoneId` | number | Yes | Zone ID |
| `action` | string | Yes | `join` |

```bash
curl 'https://www.erepublik.com/en/main/pvp-queue' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&battleId=958706&battleZoneId=42911251&action=join'
```

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | `true`: the client shows the "not enough energy" hint |
| `message` | string | `SUCCESS` or `JOIN` start the duel connection |
| `rid`, `sid`, `jid` | string | Chat (XMPP) session, when sent |
| `XMPP_BOSH_URL`, `XMPP_MUC_URL` | string | Chat server addresses |
| `extra` | mixed | Passed to the duel start |

Conditions in the client:

- At least 100 energy.
- The button is enabled with `has_jid` and `pvpKillCount` of 2 or more.
- Disabled once `SERVER_DATA.points.attacker` or `.defender` reaches 1400.
- The queue state is polled with [battle statistics](live-data.md#battle-stats) (`isInQueue`, `opponentsInQueue`).

No queue button was found on the observed pages (all spectator views).

**Source:** `battle_new.js` (version 1795779886), `#joinPVPQ` handler, `battleFX.checkBattlePoints`, `checkPvpQueue`.

---

## PvP inventory

`GET /en/military/pvp-inventory/` · Auth required · ✅ Verified 2026-11-27

The soldier equipment screen of PvP duels. Returns an HTML fragment (about 35 KB) with a script that sets `SERVER_DATA.pvpInventory`.

```bash
curl 'https://www.erepublik.com/en/military/pvp-inventory/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

`SERVER_DATA.pvpInventory`, shortened:

```json
{
  "texts": { "attributes": { "damage": "Damage" }, "level": "Level", "errors": {} },
  "ownedItems": {
    "1000001": {
      "id": 1000001, "name": "Type X Armor", "slug": "type_x_armor", "type": "body", "level": 1,
      "armor": 12, "accuracy": 1, "agility": -3, "durability": 3,
      "equipped": false, "in_use": false, "is_used": false,
      "stack": [1000002], "stock": 2
    }
  },
  "stackedItems": { "type_x_armor": 1000001 },
  "shopItems": {
    "1": { "name": "Mx3 Storm Pistol", "slug": "mx3_storm_pistol", "level": 1,
           "damage": 6, "accuracy": 1, "agility": -1, "ammo": 6, "charges": 2, "range": 100 }
  }
}
```

| Field | Description |
|-------|-------------|
| `ownedItems` | Item ID → item. Weapons have `damage`, `ammo`, `base_ammo`, `charges` (`-1` observed), `range`; protection has `armor`, `base_armor`, `durability` |
| `ownedItems.*.type` | Slot, for example `primary`, `body`, `helmet`, `boots` |
| `ownedItems.*.stack`, `stock` | IDs of identical items grouped under this one, and their count |
| `stackedItems` | Item slug → ID of the item representing the stack |
| `shopItems` | Shop item ID → attributes |

**Source:** real request, 2026-11-27; opened by `battle_new.js` (version 1795779886), `#go_inventory`. That button was not found on the observed pages.

---

## PvP inventory actions

`POST /en/main/pvp-inventory-actions` · Auth required · 🔍 From code

> [!WARNING]
> `buy` spends resources and `delete` destroys an item.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../../guides/requests.md#csrf-token) |
| `action` | string | Yes | See below |
| `id` | number \| string | Yes | Item ID; `{draggedId}-{replacedId}` when two items are involved |

| `action` | Meaning (from the name and screen logic) |
|----------|------------------------------------------|
| `buy` | Buy a shop item |
| `delete` | Delete an item |
| `equip`, `equip-use` | Put an item into a slot or into use |
| `equip-replace`, `equip-switch` | Replace or swap two items |
| `unequip` | Remove from a slot |

```bash
curl 'https://www.erepublik.com/en/main/pvp-inventory-actions' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action=unequip&id=1000001'
```

| Field | Type | Description |
|-------|------|-------------|
| `error` | boolean | Failure flag |
| `message` | string | Text shown on failure |
| `id`, `params` | mixed | After `buy`: new item ID and item data keyed by it |

After other actions the client reloads the [inventory](#pvp-inventory).

**Source:** `pvp/inventory.js` (version 1698060179), `pvpInventory.doAction`.

---

## Combat order subscriptions

An older mission list in which you subscribe to a combat order. `battle_new.js` defines it (`mercenaryMissions`, list `#list_missions_details`), but no call to `mercenaryMissions.init` was found there and the list was not on the observed pages. The current console says you are subscribed automatically while [deployed](deployment.md); see [Combat orders](live-data.md#combat-orders).

## Subscribe to combat order

`POST /en/main/mercenary-campaign/subscribe/{id}` · Auth required · 🔍 From code

> [!WARNING]
> Changes your subscription; the client asks for confirmation when another order is already selected.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `id` | path | number | Yes | The `data` attribute of the order's label; its meaning is not confirmed |
| `_token` | body | string | Yes | [CSRF token](../../guides/requests.md#csrf-token) |

```bash
curl 'https://www.erepublik.com/en/main/mercenary-campaign/subscribe/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

| Field | Type | Description |
|-------|------|-------------|
| `status` | string | `success` on success |
| `errors` | array | Error keys |

### Errors

`captcha`, `invalid_country`, `invalid_military_unit`, `invalid_division`, `campaign_not_active`, `campaign_not_found`.

**Source:** `battle_new.js` (version 1795779886), `mercenaryMissions.subscribe` and the combat order list handlers.

---

## Unsubscribe from combat order

`POST /en/main/mercenary-campaign/unsubscribe/{id}` · Auth required · 🔍 From code

> [!WARNING]
> Removes your subscription.

Parameters as for [subscribing](#subscribe-to-combat-order).

```bash
curl 'https://www.erepublik.com/en/main/mercenary-campaign/unsubscribe/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

The response has `status`; `success` on success.

**Source:** `battle_new.js` (version 1795779886), `mercenaryMissions.unsubscribe`.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Booster success status | `succes` | The request service accepts only `success`; `succes` appears in a controller branch it cannot reach | Service code |
| Valentine's effect `type` | `valentinesDay` | Sends `valentines`; the state key is `valentinesDay` | Code |
| Leaving the PvP queue | `action=leave` | No such call found | Code |
| PvP limit | "campaign points" over 1400 | Checks [`SERVER_DATA.points`](page.md#battle), the button says "Over 1400 points" | Not settled which score reaches 1400 |
| `pvpShowLock` | Gates PvP availability | It marks that you are in the queue | Code |
| Is PvP still playable? | Described as active | Buttons absent from spectator pages | Not settled |
