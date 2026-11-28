# Dormant event code

[API docs](../README.md) › Events › Dormant event code

Calls that are still in the game's JavaScript but belong to events or features with no reachable screen on 2026-11-27: the revival event, two Bitcoin reward features, the Dye Hard event shop, the seasonal popups and the old mission script.

> [!IMPORTANT]
> Treat everything here as inactive. Only `GET` requests were tried; no `POST` was sent. An answer from the server does not mean the feature works.

Current event pages: [Black Friday](black-friday.md), [An Amazing Journey](amazing-journey.md), [Tournaments](tournaments.md).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Revival missions](#revival-missions) | POST | `/en/main/revival-missions-data` | 🔍 From code |
| [Revival objective status](#revival-objective-status) | POST | `/en/main/revival-objective-status` | 🔍 From code |
| [Claim a revival chest](#claim-a-revival-chest) | POST | `/en/main/revival-objective-claim-reward` | 🔍 From code |
| [Revival login rewards](#revival-login-rewards) | POST | `/en/main/revival-login-rewards-status` | 🔍 From code |
| [Claim a login reward](#claim-a-login-reward) | POST | `/en/main/revival-login-claim-reward` | 🔍 From code |
| [Request to be a sponsor](#request-to-be-a-sponsor) | POST | `/en/main/revival-sponsor-request` | 🔍 From code |
| [Zebedee claim](#zebedee-claim) | POST | `/en/main/zebedee-claim` | 🔍 From code |
| [Pool status](#pool-status) | POST | `/en/main/zebedee-pool-status` | 🔍 From code |
| [Xapo account](#xapo-account) | POST | `/en/main/xapo-check-user/` | 🔍 From code |
| [Claim raffle prizes](#claim-raffle-prizes) | POST | `/en/main/raffle-claim-prizes/` | 🔍 From code |
| [Dye Hard status](#dye-hard-status) | GET | `/en/main/dyeHard-eventShop/status` | ✅ Verified 2026-11-27 |
| [Dye Hard spend](#dye-hard-spend) | POST | `/en/main/dyeHard-eventShop/spend/{price}` | 🔍 From code |
| [Event popups](#event-popups) | GET | `/en/main/{popup}` | See the table |
| [Check a mission](#old-mission-calls) | GET | `/en/main/mission-check` | 🔍 From code |
| [Unlock a mission requirement](#old-mission-calls) | GET | `/en/main/mission-unlock` | 🔍 From code |
| [Awards popup](#awards-popup) | GET | `/en/main/awards-popup` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Every `POST` below sends the [CSRF token](../guides/requests.md#csrf-token) as `_token`; the parameter tables list only the rest.

---

## Revival event

A comeback event for returning citizens, with its own daily missions, objective chests and login rewards. It mirrors [Daily missions](../home/daily-missions.md) and [Daily objectives](../home/daily-objectives.md).

| Element | Value |
|---------|-------|
| Triggers | `#citizenRevivalPopupTrigger`, `#citizenRevivalLoginRewardsPopupTrigger` |
| Popups | `citizenRevivalPopup` (`CitizenRevivalController`, `/templates/CitizenRevival.tpl.html`), `citizenRevivalLoginRewardsPopup` (`LoginRewardsRevivalController`, `/templates/LoginRewardsRevival.tpl.html`) |
| Overlay opacity | 0.75 for the login rewards popup |

How an Angular template path is resolved:

```javascript
// ERPK.angular.$templatePath:
// 1. erepublik.angularTemplates[name], if defined
// 2. otherwise '/templates/{name}.tpl.html'
// name = controller name without the 'Erpk' prefix and 'Controller' suffix
```

---

## Revival missions

`POST /en/main/revival-missions-data` · Auth required · 🔍 From code

Mission list of the revival popup. No parameters besides `_token`.

| Field | Description |
|-------|-------------|
| `currentRevivalDay` | Event day; the client caps it at 5 |
| `missions[].id` | Mission ID, used to claim it |
| `missions[].progress[]` | `completed`, `requirement` |
| `missions[].hint.links.{requirement}.url` | Where the **Go** button leads |

A mission can be claimed when every `progress` entry is completed. It is claimed with the shared [Claim a mission](../home/daily-missions.md#claim-a-mission) endpoint (`/en/main/mission-solve`, `missionId`); the revival client reads a failure from `error` and `errorMessage`.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `CitizenRevivalController` and `CitizenRevivalFactory`.

---

## Revival objective status

`POST /en/main/revival-objective-status` · Auth required · 🔍 From code

Activity Points and chests of the revival popup. The login rewards popup calls it too, for the countdown.

| Field | Description |
|-------|-------------|
| `status.progress` | Activity Points |
| `status.claimedObjectives` | Keyed by chest cost |
| `status.timeLeft` | Seconds on the event countdown |
| `data.{cost}.objective_cost` | Points needed |
| `data.{cost}.rewards[]` | Chest rewards |

**Source:** `erpk_angular_controllers.js` (1795779904), `CitizenRevivalFactory.getObjectiveStatus`.

---

## Claim a revival chest

`POST /en/main/revival-objective-claim-reward` · Auth required · 🔍 From code

> [!WARNING]
> Claims a reward on your account.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `objectiveCost` | number | Yes | Cost of the chest |

A truthy `error` means failure; the client shows `error.message`.

**Source:** `erpk_angular_controllers.js` (1795779904), `CitizenRevivalFactory.claimObjective`.

---

## Revival login rewards

`POST /en/main/revival-login-rewards-status` · Auth required · 🔍 From code

No parameters besides `_token`.

| Field | Description |
|-------|-------------|
| `login_rewards_day` | Current day |
| `data.{key}.reward_day` | Day the rewards belong to |
| `data.{key}.rewards_json` | Rewards on offer |
| `data.{key}.claimed_rewards_json` | `null` until claimed; then shown instead of `rewards_json` |
| `data.{key}.can_claim` | Whether the day can be claimed |

On failure the client shows `message`.

**Source:** `erpk_angular_controllers.js` (1795779904), `LoginRewardsRevivalController`.

---

## Claim a login reward

`POST /en/main/revival-login-claim-reward` · Auth required · 🔍 From code

> [!WARNING]
> Claims a reward on your account.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `day` | number | Yes | Reward day |

A truthy `error` means failure (`error.message`).

**Source:** `erpk_angular_controllers.js` (1795779904), `LoginRewardsRevivalFactory.claimReward`.

---

## Request to be a sponsor

`POST /en/main/revival-sponsor-request` · Auth required · 🔍 From code

> [!WARNING]
> Sends a request to another citizen.

Offered on a citizen profile when `events.revivalEvent.canRequestToBeSponsor` is set in the profile data.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `action` | string | Yes | `request` |
| `counterpartId` | number | Yes | Citizen ID of the profile |

The client shows the response `message`.

**Source:** `erpk_angular_controllers.js` (1795779904), `ErpkProfileFactory.requestToBeRevivalSponsor`.

---

## Zebedee claim

`POST /en/main/zebedee-claim` · Auth required · 🔍 From code

> [!WARNING]
> Two of the three actions change state: accepting terms, and sending the Bitcoin reward to an external account.

Used by the **Crypto Assets** popup (`ErpkCryptoAssetsController`, opened from the inventory). Amounts are in satoshi (`SAT`).

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `action` | string | Yes | `getAmountAvailable`, `acceptTermsOfService` or `sendToGamerTag` |
| `gamertag` | string | For `sendToGamerTag` | ZEBEDEE gamertag |

| Field | Description |
|-------|-------------|
| `error`, `message` | Failure and its text |
| `showTermsOfService` | The terms must be shown first |
| `amount` | Amount available (`0` in these docs) |
| `unit` | Unit; `SAT` by default in the client |

The popup itself (`/en/main/cryptoAssetsPopup`) returned 404 on 2026-11-27.

**Source:** `erpk_angular_controllers.js` (1795779904), `ErpkCryptoAssetsController`.

---

## Pool status

`POST /en/main/zebedee-pool-status` · Auth required · 🔍 From code

Despite the name, it is called by the shop-item purchase popup: after a purchase, when the selected item has `poolStatus`, the client refreshes it. No parameters besides `_token`. The response field `poolStatus` replaces the item's value.

**Source:** `erpk_angular_controllers.js` (1795779904), `getPoolStatus` / `updatePoolStatus`.

---

## Xapo account

`POST /en/main/xapo-check-user/` · Auth required · 🔍 From code

> [!WARNING]
> Creates or changes a link to an external wallet, and can transfer a balance.

Used by `XapoController`. The parameters decide the operation. All variants send `id` (your citizen ID).

| Operation | Extra parameters |
|-----------|------------------|
| Read state | `checkInstance=1` |
| Create account | `createAccount=1`; the client's function receives the form value but does not send an e-mail address |
| Change e-mail | `newEmail` |
| Transfer | `transferDone=1` |

| Field | Operation | Description |
|-------|-----------|-------------|
| `mail_sent` | Read, create | Activation e-mail sent |
| `is_validated` | Read, create | Account validated |
| `current_email` | Read | E-mail on file |
| `current_amount` | Read, transfer | Bits available (`0` in these docs) |
| `transfered` | Read, transfer | Bits already in the wallet (the game's spelling) |
| `prizes` | Read | Prizes keyed by kind: `bitcoins`, `poster`, `gold`, `booster`, `damage_booster`; the client reads `value` |
| `can_claim_prize` | Read | `0` cannot claim, `1` can claim, `2` hide the button |
| `error`, `error_message` | Create, change | Result; a successful e-mail change also puts its text in `error_message` |
| `success`, `error_message` | Transfer | Result |

**Source:** `erpk_angular_controllers.js` (1795779904), `XapoController` and `XapoFactory`.

---

## Claim raffle prizes

`POST /en/main/raffle-claim-prizes/` · Auth required · 🔍 From code

> [!WARNING]
> Claims prizes on your account.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `id` | number | Yes | Your citizen ID |
| `claimPrize` | number | Yes | `1` |

| Field | Description |
|-------|-------------|
| `success`, `success_message` | Result and text |
| `error_message` | Text on failure |
| `current_amount`, `transfered` | Updated Bitcoin amounts, when present |
| `gold` | New gold balance, when present (`0` in these docs) |

**Source:** `erpk_angular_controllers.js` (1795779904), `XapoFactory.claimPrize`.

---

## Dye Hard status

`GET /en/main/dyeHard-eventShop/status` · Auth required · ✅ Verified 2026-11-27

Event token balance and prize tiers of the Dye Hard event shop. Still answers although the shop page returns 404.

```bash
curl 'https://www.erepublik.com/en/main/dyeHard-eventShop/status' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

```json
{
  "prestige": 0,
  "prizes": { "5": 1, "10": 1, "50": 1, "100": 1 }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `prestige` | number | Tokens available to spend |
| `prizes` | object | Keyed by tier price; for a value of `1` the client marks the tier as having no top prize |

Without a session the response was a short JSON object with an `error` key.

**Source:** real request, 2026-11-27; `dyeHard.getPrestige` in `erepublik.js` (version 1795779896).

---

## Dye Hard spend

`POST /en/main/dyeHard-eventShop/spend/{price}` · Auth required · 🔍 From code

> [!WARNING]
> Spends event tokens on a random prize.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `price` | path | number | Yes | Tier price, a key of `prizes` |
| `_token` | body | string | Yes | CSRF token |

| Field | Description |
|-------|-------------|
| `prizeId` | The prize won |
| `message` | Error text |

The client refuses locally when the balance is below the price.

`missions.js` (version 1753347317) also adds a mission's token reward to the counter in the `dyeHard_side_banner` sidebar banner.

**Source:** `dyeHard.getPrize` in `erepublik.js` (1795779896).

---

## Event popups

`GET /en/main/{popup}` · Auth required · HTML fragments

Loaded by `generalPopup.renderPopup(name)` into `#promoPopupHolder`. They are expected to work only during their event.

Result is what a logged-in `GET` returned on 2026-11-27. An empty body or a 404 confirms nothing about the popup, so those rows stay 🔍.

| Path (`/en/main/…`) | Popup name | Event | Result | Status |
|---------------------|------------|-------|--------|--------|
| `revival-popup` | `revivalPopup` | Account revival, for returning players (opacity 0.75) | 404, HTML page | 🔍 |
| `revivalEventPopup` | `revivalEventPopup` | Seasonal revival event | 404, JSON | 🔍 |
| `anniversary-popup` | `erepublikAnniversary` | Anniversary | 404, HTML page | 🔍 |
| `winterOlympicsPopup` | `winterOlympicsPopup` | Winter Olympics | 404, JSON | 🔍 |
| `halloweenPopup` | `halloweenPopup`, `halloween2017`–`halloween2020` | Halloween ([details](#halloween-challenge-popup)) | 404, JSON | 🔍 |
| `halloweenRewards` | `halloweenRewards` | Halloween rewards | 404, JSON | 🔍 |
| `easterRewards` | `easterRewards` | Easter rewards | 200, empty body | 🔍 |
| `christmas-gifts` | `christmasGifts` | Christmas gifts | 200, [old content](#christmas-gifts) | ✅ 2026-11-27 |
| `aprilsFoolsPopup` | `aprilsFools` | April Fools | 200, empty body | 🔍 |
| `summerChallengeTop5` | `summerChallengeTop5` | Summer Challenge | 200, empty body | 🔍 |
| `valentinesDayPopup` | `valentinesDayPopup` | Valentine's Day | 404, JSON | 🔍 |
| `childrensDayPopup` | `childrensDayPopup` | Children's Day | 404, JSON | 🔍 |
| `multipleAircraftZonesPopup` | `multipleAircraftZonesPopup` | Information about fighting in several aircraft zones | 200, empty body | 🔍 |
| `cryptoAssetsPopup` | `cryptoAssetsPopup` | Crypto asset holdings ([Zebedee claim](#zebedee-claim)) | 404, HTML page | 🔍 |
| `cryptoEventPopup` | `cryptoEventPopup` | Crypto event | 404, JSON | 🔍 |
| `deployLootPopup` | `deployLootPopup` | Loot earned from deployments | 404, JSON | 🔍 |
| `day6000Popup` | `day6000Popup` | Day 6,000 celebration | 404, JSON | 🔍 |
| `dyeHard-eventShop` | — | Dye Hard event shop (`dyeHard.eventShopUrl`) | 404, JSON | 🔍 |

```bash
curl 'https://www.erepublik.com/en/main/christmas-gifts' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

The JSON form of the 404:

```json
{ "error": true, "message": "404 Not Found", "status": false, "code": 404 }
```

Other places that open these popups: `ErpkApp` controllers have shortcuts for `halloweenRewards`, `christmasGifts`, `summerChallengeTop5`, `aprilsFools` and `easterRewards`; the leaderboard's event-energy link opens `winterOlympicsPopup`; a battlefield effect controller opens `childrensDayPopup`.

**Source:** real requests, 2026-11-27; `generalPopup.renderPopup` in `erepublik.js` (1795779896).

### Christmas gifts

On 2026-11-27 the response was the 2018 announcement: a `section.christmasPopup` about days 4,049–4,059 (gifting Strength between citizens, gift packs and gifts for the top 5 damage dealers). It loads `//www.erepublik.net/js/christmasPopup/script.1698060179.js`, which starts the countdown from `.countdown .number[data-until]`. Without a session the response was a short JSON object with an `error` key.

### Halloween Challenge popup

The event story, countdown, deployment loot rules, Power Spin bonuses and item expiry. The content is rewritten for each edition. The details are from earlier notes on the 2026 "Hexecutioner's Harvest" edition, recorded while it ran. The popup used overlay opacity 0.8.

<details>
<summary>Example response (HTTP 200, text/html, condensed)</summary>

```html
<section id="halloweenChallengePopup" class="halloweenPopup halloweenChallengePopup">
  <a href="javascript:" title="Close window" class="close">Close</a>
  <div class="title">Halloween Challenge</div>
  <div class="countdown">
    <span class="text">Time left</span>
    <span class="number" data-until="160644">00:00:00:00</span>
  </div>
  <div class="content" id="popupContent">
    <!-- p.defaultText and h2.defaultText blocks: story, "Deployment loot", "Power Spin Power-up", expiry -->
  </div>
</section>
<script>
  var eventPopupCountdownContainerId = '#halloweenChallengePopup';
  var eventPopupScrollableContentId = '#halloweenChallengePopup > #popupContent';
</script>
<script src="//www.erepublik.net/js/eventPopup.1729767356.js"></script>
<style>/* a.popupStyle link colours */</style>
```

</details>

| Selector or variable | Description |
|----------------------|-------------|
| `.countdown .number[data-until]` | Seconds remaining at the time of the response (not a Unix timestamp) |
| `#popupContent strong` | Event windows and thresholds, as display text only |
| `eventPopupCountdownContainerId` | Container in which `eventPopup.js` starts the countdown |
| `eventPopupScrollableContentId` | Element that `eventPopup.js` makes scrollable (300px high) |

2026 edition rules:

| Mechanic | Window | Details |
|----------|--------|---------|
| Deployment loot | Day 6,916 00:00 – day 6,929 23:59 | Deployments of at least 1500 energy can drop Dark Ghost Boosters, Pumpkins and Shadow Boosters. More energy improves the chances |
| Power Spin rewards | Day 6,916 00:00 – day 6,921 23:59 | Adds Dark Ghost Boosters and Shadow Boosters to the wheel |
| Battlefield Hero spins | Day 6,916 00:00 – day 6,921 23:59 | Battle Hero: 1 free spin; Sky Hero: 2; Campaign Hero: 8 |
| Event booster expiry | Until day 6,931 23:59 | The inventory shows the exact expiry of each item |

The Shadow Booster tooltip: it doubles damage but does not affect Rank Points or True Patriot progress.

- `eventPopup.js` shows the popup automatically when the page sets `eventPopupAutoShowName`. It stores the next show time in `localStorage` under `eventPopup.<name>` and `eventGrowl.<name>`.
- Clicking the "New Event!" growl delays both the growl and the popup by 24 hours. Otherwise the popup repeats after 3 hours, or 24 hours when `erepublik.citizen.isActive()` is true.
- Event rules are only display text, so reading them means scraping HTML.

---

## Old mission calls

`GET /en/main/mission-check`, `GET /en/main/mission-unlock` · Auth required · 🔍 From code

> [!WARNING]
> Both are `GET` requests that change state: unlocking can spend Gold, and a finished check leads the client to solve the mission.

From `missions.js`, which was not loaded on the pages read on 2026-11-28.

| Name | Call | Description |
|------|------|-------------|
| `missionId` | both | Mission ID |
| `_token` | both | CSRF token |
| `requirement` | unlock | Requirement to unlock |
| `goldCost` | unlock | Price shown to the user |

```bash
curl 'https://www.erepublik.com/en/main/mission-check?missionId=1&_token=YOUR_CSRF_TOKEN' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

```bash
curl 'https://www.erepublik.com/en/main/mission-unlock?missionId=1&requirement=REQUIREMENT&goldCost=1&_token=YOUR_CSRF_TOKEN' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

After a check the client reads `isFinished`, and calls `/en/main/mission-solve` when it is true. After an unlock it expects `msg: "success"`, `isFinished` and `gold`.

**Source:** `missions.js` (version 1753347317).

---

## Awards popup

`GET /en/main/awards-popup` · Auth required · 🔍 From code

HTML fragment with new awards, loaded by `missions.js` into `#award_pop_up_ajax`. Not requested: reading it may mark awards as shown.

```bash
curl 'https://www.erepublik.com/en/main/awards-popup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** `missions.js` (version 1753347317), `awardPopup`.

---

## Related code documented elsewhere

| Code | Page |
|------|------|
| `valentinesDay` and `snowFight` battle effects | [Activate battle effect](../military/battlefield/boosters-bombs-pvp.md#activate-battle-effect) |
| Anniversary quest page and help | [An Amazing Journey](amazing-journey.md) |
| Strength switching offered on profiles (`/en/main/switch-strength`) | [Switch strength](../social/profiles.md#switch-strength). Changes state: a `GET` with `action=offer` sends an offer |

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Crypto Assets popup | Shows crypto asset holdings | 404 | Both noted |
| Halloween popup outside the event | Untested | 404 on 2026-11-27 | Live request |
| Empty 200 popups | — | Whether they have content during their event is unknown | — |
| Revival responses | — | No response was captured | Fields from code |
