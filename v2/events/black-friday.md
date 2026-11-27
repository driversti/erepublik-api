# Black Friday

[API docs](../README.md) › Events › Black Friday

A one-day sale. This page describes the **2026** edition, observed on 2026-11-27 (game day 6,947).

> [!IMPORTANT]
> Everything here exists only during the event. No event-only endpoint was found: the sale uses the normal [shop endpoints](../economy/shop.md) with special pack types, item IDs and promotion data.

## Schedule

The shop timer and promotions expire at Unix time `1795852800`, which is 2026-11-28 00:00 game time (one day).

## Promotion data

Game pages expose the global `ErpkShop` (`promotions[]`, `popupPromotions[]`) which feeds the sidebar icons and the homepage popup. ✅ Verified 2026-11-27

| Field | Meaning |
|-------|---------|
| `id`, `typeId` | Promotion key |
| `title`, `description`, `buttonText` | Texts |
| `expiresAt`, `expiresIn`, `timeLeft`, `lastDay` | Timing |
| `showPopup` | Included in the homepage popup |
| `popupStyle`, `popupChar`, `buttonImage` | Presentation |
| `shopUrl` | Link target |

Promotions active that day:

| `id` | Title | Ends with the sale |
|------|-------|--------------------|
| `blackFriday` | Black Friday | Yes |
| `lpBonus` | Loyalty Points: extra points on selected packs | Yes |
| `overdrivePack` | Overdrive Pack | Yes |
| `bombPack` (twice) | Land and Air Warfare Packs | Yes |
| `heroPack` (`typeId: energyBars`) | Hero Pack | Yes |
| `icon_gold` | Gold Pack | Yes |
| `blueprintsPack_10` | Daily Bundle | Daily promotion |
| `infantryKit`, `powerPack` | Infantry Kit, Power Pack | No (3 more days) |

## Event packs

Section `section_black_friday`. Contents from the [pack popup](../economy/shop.md#pack-popup) (✅ Verified 2026-11-27). Prices were shown in PLN: **20.99 PLN** each. Every pack except Black Tokens can instead be bought for **49 Game Tokens** (✅ Verified in the [tokens popup](../economy/shop.md#game-tokens-popup); its script sends `packPrice: 4.99`). Each can be bought once.

| Pack type | Name | Contents |
|-----------|------|----------|
| `bf2026ProgGold_XS` | Black Gold XS | Gold 220, VIP 524, bonus Gold 22, LP 100 |
| `bf2026ProgEB_XS` | Black Energy XS | Double Energy Bars 100, Triple Energy Bars 22, VIP 524, LP 100 |
| `bf2026Tokens_XS` | Black Tokens XS | Game Tokens 49, Gold 14, Double Energy Bars 2, bonus Gold 1, LP 100 |
| `bf2026ProgBombGnd_XS` | Land Warfare XS | Gold 41, Triple Energy Bars 35, Damage Boosters 4 (5 min 200%) and 7 (10 min 150%), SLG Peacemaker 4, Annihilator 2, Ground Influence Boosters 4 (10 min 30%), Ground Rank Boosters 2 (10 min 30%), Damage Accelerator 7 (5 min 5×), Fuel 1, Pumpkins 33, VIP 524, bonus Gold 4, LP 100 |
| `bf2026ProgBombAir_XS` | Air Warfare XS | Gold 41, Triple Energy Bars 35, Air Damage Boosters 2 (5 min 100%) and 7 (10 min 50%), AIM-7 Grid-Lock 4, AIM-92 Stinger 82, Air Influence Boosters 4 (10 min 30%), Air Rank Boosters 2 (10 min 30%), Damage Accelerator 7 (5 min 5×), Fuel 1, Pumpkins 33, VIP 524, bonus Gold 4, LP 100 |
| `bf2026Overdrive_XS` | Overdrive XS | Gold 106, Rapid Burn Booster 1, Universal Ground Points 250M, Universal Aircraft Points 50k, Double Energy Bars 14, VIP 524, bonus Gold 10, LP 100 |
| `bf2026Gift` | Black Friday Gift | Free (`freePack`); already received on the observed account, so the popup answers "You cannot buy this pack" |

- LP means Loyalty Points. Bonus Gold and LP are not granted when paying with Game Tokens, and Black Friday LP does not count towards Loyalty Status.
- The `…Prog…` packs and Black Tokens are labelled **Chain Deal** (`progressive_pack`). Only the `XS` tier was listed. Requesting `…Gold_S`, `…Gold_M`, `…EB_S` or `…BombGnd_S` answers "You cannot buy this pack"; later tiers presumably unlock after a purchase (❔ Unconfirmed).
- Regular packs gave their usual LP that day (for example 10 on `goldPack_XS`), against 100 on every event pack.

## Event items

In [`shopItemsJson`](../economy/shop.md#list-items), event items carry `blackFriday` in `packClasses`. ✅ Verified 2026-11-27

| `id` | Gold price | Discounted | LP price | Limit |
|------|-----------:|-----------:|---------:|------:|
| `BlackLandWarfare1`–`5` | 90, 180, 450, 900, 1800 | — | — | 3 each |
| `BlackAirWarfare1`–`5` | 90, 180, 450, 900, 1800 | — | — | 3 each |
| `tripleEnergyBarPackBlackFriday1`–`3` | 28, 140, 280 | — | — | 2 each |
| `doubleEnergyBarPack1` | 5 | 3.5 | 30 | 100 |
| `doubleEnergyBarPack2` | 25 | 15.4 | 132 | 100 |
| `doubleEnergyBarPack3` | 50 | 27.3 | 234 | 100 |

Warfare packs have `category: bombPacks` and `isBombPack: true`.

<details>
<summary>Item contents</summary>

**Land Warfare (`BlackLandWarfare1`–`5`)**

| Item | XS | S | M | L | XL |
|------|--:|--:|--:|--:|--:|
| Annihilator | 2 | 4 | 8 | 16 | 30 |
| +150% Ground Damage | 7 | 13 | 29 | 56 | 107 |
| +30% Ground Influence | 4 | 7 | 15 | 28 | 54 |
| +30% Ground Rank Points | 2 | 4 | 8 | 14 | 27 |
| 5× Damage Accelerator | 7 | 13 | 30 | 57 | 110 |
| Fuel | 1 | 2 | 5 | 10 | 20 |
| Pumpkins | 33 | 62 | 145 | 279 | 538 |

**Air Warfare (`BlackAirWarfare1`–`5`)**

| Item | XS | S | M | L | XL |
|------|--:|--:|--:|--:|--:|
| AIM-92 Stinger | 82 | 154 | 359 | 693 | 1337 |
| +50% Air Damage | 7 | 13 | 29 | 56 | 107 |
| +30% Air Influence | 4 | 7 | 15 | 28 | 54 |
| +30% Air Rank Points | 2 | 4 | 8 | 14 | 27 |
| 5× Damage Accelerator | 7 | 13 | 30 | 57 | 110 |
| Fuel | 1 | 2 | 5 | 10 | 20 |
| Pumpkins | 33 | 62 | 145 | 279 | 538 |

Triple Energy Bar packs 1–3 contain 10, 50 and 100 bars. Double Energy Bar packs 1–3 contain 1, 5 and 10.

</details>

## Not captured yet

- Higher Chain Deal tiers, the gift contents and any purchase response (needs a purchase).
- VIP Shop items showed `discount` badges that day; whether they belong to the sale is unknown.

**Source:** live read-only requests, 2026-11-27; `erpk_angular_controllers.js` (1795779904), `erepublik.js` (1795779896).
