# Weekly challenge

[API docs](../README.md) › Home and daily routine › Weekly challenge

A reward ladder bought with Prestige Points from [daily missions](daily-missions.md). It resets every Tuesday at 00:00 game time. Event weeks change the title and rewards (2026-11-27: "Anniversary Challenge (II)").

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Challenge data](#challenge-data) | GET | `/en/main/weekly-challenge-data` | ✅ Verified 2026-11-27 |
| [Reward list popup](#reward-list-popup) | GET | `/en/main/weekly-challenge-rewards` | ✅ Verified 2026-11-27 |
| [Collect one reward](#collect-one-reward) | POST | `/en/main/weekly-challenge-collect-reward` | 🔍 From code |
| [Collect all rewards](#collect-all-rewards) | POST | `/en/main/weekly-challenge-collect-all` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Challenge data

`GET /en/main/weekly-challenge-data` · Auth required · ✅ Verified 2026-11-27

The reward tiers around your current position, your points and the time left.

```bash
curl 'https://www.erepublik.com/en/main/weekly-challenge-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened to one tier:

```json
{
  "error": false,
  "enabled": true,
  "type": { "anniversary": true },
  "title": "Anniversary Challenge (II)",
  "timeLeft": 310718,
  "weeklyId": 685,
  "maxRewardId": 0,
  "skinId": null,
  "progress": 0.9435714285714286,
  "player": { "name": "PlayerName", "avatar": "https://cdnt.erepublik.net/…", "prestigePoints": 39630 },
  "nextReward": { "maxReward": false, "type": "icon_amazing_coin", "text": "+10 Amazing Coins" },
  "rewards": {
    "normal": [
      { "id": 156, "collectedBefore": 155, "percent": 0.8214285714285714, "status": "rewarded",
        "icon": "amazing_coin", "tooltip": "+10 Amazing Coins", "label": "You have already collected this reward" }
    ],
    "extra": []
  }
}
```

(`type` is shortened; the real object also lists the other challenge flags.)

| Field | Type | Description |
|-------|------|-------------|
| `error`, `enabled` | boolean | Request state; whether the challenge is on |
| `type` | object | Theme flags: `anniversary`, `flavorPacks`, `springChallenge`, `summerChallenge`, `tropicalChallenge`, `blueElectric`, `autumnChallenge`, `shadowChallenge`, `halloweenChallenge`, `winterChallenge`, `skinChallenge`, `valentinesDay`, `lunarNewYear`, `childrensDay`, `cryptoChallenge`, `icarusChallenge` |
| `title` | string | Challenge name |
| `timeLeft` | number | Seconds until reset |
| `weeklyId` | number | Week number |
| `maxRewardId` | number | Sent to [collect all](#collect-all-rewards) |
| `skinId` | number \| null | Themed skin |
| `progress` | number | Progress bar position, `0`–`1` |
| `player.prestigePoints` | number | Your points |
| `nextReward` | object | `maxReward`, `type`, `text` |
| `rewards.normal[]` | array | A window of tiers (16 returned) |
| `rewards.normal[].id` | number | Tier ID, sent as `rewardId` |
| `rewards.normal[].status` | string | `rewarded` collected, `completed` ready to collect, `""` locked |
| `rewards.normal[].icon`, `tooltip`, `label` | string | Reward type and text. Observed icons: `energy_bars`, `amazing_coin`; earlier notes: `factory_reset_token` |
| `rewards.normal[].percent` | number | Position on the bar |
| `rewards.normal[].collectedBefore` | number | See [Open questions](#open-questions) |
| `rewards.extra` | array | Extra rewards; empty when observed |

**Source:** real request, 2026-11-27; `ErpkWeeklyChallengeFactory` in `erpk_angular_controllers.js` (version 1795779904); flag list — earlier notes.

---

## Reward list popup

`GET /en/main/weekly-challenge-rewards` · Auth required · ✅ Verified 2026-11-27

An **HTML fragment** with absolute point thresholds, which the JSON data does not give.

```bash
curl 'https://www.erepublik.com/en/main/weekly-challenge-rewards' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

<details>
<summary>Example response (earlier notes, trimmed)</summary>

```html
<div class="fadeInRight" id="WeeklyRewardsList">
  <div class="rewardsWrapper">
    <div class="progressBar"><span class="currentProgress" style="height: calc(70px * 0);"></span></div>
    <div class="rewardRow points_0 pending">
      <div class="rewardProgress">
        <span class="points pending">5</span>
        <img class="status" src="//www.erepublik.net/images/modules/popups/weekly_challenge_rewards/white_rhombus.png" alt="">
      </div>
      <div class="rewardWrapper pending">
        <div class="rewardIcon"><img src="https://www.erepublik.net/images/icons/rewards/energy_booster.png?1698060173" alt=""></div>
        <b class="pending">+1 Energy recovery until the end of Day 6,943</b>
      </div>
    </div>
  </div>
</div>
<script>
  setTimeout(function () { $j('.rewardsWrapper').mCustomScrollbar('scrollTo', '.rewardRow.points_' + -1); }, 500);
</script>
```

</details>

| Selector | Meaning |
|----------|---------|
| `.rewardRow.points_{i}` | One milestone; `i` is a row index, not a point value |
| `.rewardRow` state class | `pending` or `rewarded` |
| `.rewardProgress .points` | Prestige Point threshold |
| `.rewardIcon img[src]` | Reward type, by file name |
| `.rewardWrapper b` | Reward text |
| `.currentProgress` style | `calc(70px * N)`, progress bar fill; rows are 70 px tall |
| `scrollTo … points_' + X` | Last reached row; `-1` for none |

### Ladders observed

The ladder is server-defined and differs between weeks; do not hardcode it.

| Date | Rows | Range | Rewards |
|------|------|-------|---------|
| 2026-11-27, with 39630 points | 102 (11 `rewarded`) | 34500–90000 | `+10 Amazing Coins`, `20 Energy Bars`, `5 Energy Bars`, `+5 Fuel` |
| Earlier notes, week ending Day 6,943, 0 points | 257 | 5–90000 | See below |

<details>
<summary>Full-week ladder (earlier notes)</summary>

| Points | Spacing | Rewards |
|--------|---------|---------|
| 5–5000 | irregular | Mostly energy recovery boosts and +20 Amazing Coins; +5 Fuel, damage boosters, 1–5 Energy Bars, 1 Small Bomb |
| 5000–20000 | ~250 | Energy recovery boosts, +10 Amazing Coins, 10 Energy Bars (to 15000), then 15 |
| 20000–40000 | 500 | 20 Energy Bars and +10 Amazing Coins alternating, some +5 Fuel |
| 40000–80000 | 500 | +10 Amazing Coins and 5 Energy Bars alternating |
| 80000–90000 | 1000 | +10 Amazing Coins |

Totals: 1200 Amazing Coins, 862 Energy Bars, 50 energy recovery boosts, 35 Fuel, 4 damage and 3 air damage boosters, 1 Small Bomb.

| Icon file | Reward text |
|-----------|-------------|
| `energy_booster.png` | `+1 Energy recovery until the end of Day {N}` |
| `128px/amazing_coin.png` | `+10` / `+20 Amazing Coins` |
| `q10_128x128.png` | `{1,5,10,15,20} Energy Bars` |
| `fuel.png` | `+5 Fuel` |
| `damage.png` | `+150% Damage Booster for 10 minutes` |
| `air_damage.png` | `+50% Air Damage Booster for 5 minutes` |
| `q21_128x128.png` | `1 Small Bombs` |

Energy recovery rewards expire at the end of the challenge week.

</details>

**Source:** real request, 2026-11-27; popup `weeklyChallengeRewardsList` in `erepublik.js` (version 1795779896); earlier notes.

---

## Collect one reward

`POST /en/main/weekly-challenge-collect-reward` · Auth required · 🔍 From code

> [!WARNING]
> Collects the reward on your account.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `rewardId` | number | Yes | `rewards.normal[].id` of a `completed` tier |

```bash
curl 'https://www.erepublik.com/en/main/weekly-challenge-collect-reward' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'rewardId=53&_token=YOUR_CSRF_TOKEN'
```

### Response

The client shows the response as the reward popup and reads `amount` and `multiplier` when the tier icon is `energy_booster`; `energy_house` reloads the page. It then refreshes the [data](#challenge-data).

Earlier notes give:

```json
{ "status": "success", "message": "Reward collected" }
```

```json
{ "status": "error", "message": "Already collected" }
```

**Source:** `erpk_angular_controllers.js` (version 1795779904), `getReward`; examples — earlier notes.

---

## Collect all rewards

`POST /en/main/weekly-challenge-collect-all` · Auth required · 🔍 From code

> [!WARNING]
> Collects every unlocked reward on your account.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `maxRewardId` | number | Yes | `maxRewardId` from the [data](#challenge-data); tiers up to it are collected |

```bash
curl 'https://www.erepublik.com/en/main/weekly-challenge-collect-all' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'maxRewardId=13&_token=YOUR_CSRF_TOKEN'
```

### Response

Earlier notes report two shapes, both HTTP `200`:

```json
{ "error": false, "rewards": [] }
```

```json
{ "status": "error", "message": "no_reward" }
```

The client reads `rewards[]` (`icon`, `amount`, `multiplier`). In earlier tests `rewards` was empty even when tiers 12 and 13 were collected (tier 13 was "5 Q5 Moving Tickets"), so compare tier statuses before and after to know what you got. `maxRewardId=10` returned `no_reward`; 11–13 were accepted.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `getAllReward`; examples — earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| `collectedBefore` | Times collected in earlier weeks | Values like `155` next to `id: 156`, `0` on later tiers | Not settled |
| `progress` | Share of the way to the next tier | Rule not derived | — |
| Single collect response | `status`, `message` | Client reads `amount`, `multiplier` | Both listed |
| `rewards` of collect all | Always empty | Client reads items from it | Both listed |
| `maxRewardId` when nothing is ready | Not described | `0` with no `completed` tier | Live response |
