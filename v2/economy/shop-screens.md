# Other shop screens and promo popups

[API docs](../README.md) › Economy › Other shop screens and promo popups

The shop screens around the [main shop](shop.md): the Gold Store, the Supply Shop fragment, the gifting popups, the "thank you" popup and the promotional popups. Pack and item endpoints are described on the [Shop](shop.md) page; the VIP Shop is on [VIP and loyalty](vip.md#vip-shop-page). Payment systems are not documented.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Gold Store page](#gold-store-page) | GET | `/en/main/gold-items` | ✅ Verified 2026-11-27 |
| [Supply Shop fragment](#supply-shop-fragment) | GET | `/en/main/shop-gold-items` | ✅ Verified 2026-11-27 |
| [Gift a friend popup](#gift-a-friend-popup) | GET | `/en/main/buy-forfriend` | ✅ Verified 2026-11-27 |
| [Send gift popup](#send-gift-popup) | GET | `/en/main/gifts-pack` | ✅ Verified 2026-11-27 |
| [Send gifts](#send-gifts) | POST | `/en/main/donate-gifts-action` | 🔍 From code |
| [Purchase thank-you popup](#purchase-thank-you-popup) | GET | `/en/main/payment-success` | ✅ Verified 2026-11-27 |
| [Paths that return 404](#paths-that-return-404) | GET | `/en/main/starter-pack`, `/en/main/gold-packs`, `/en/main/gold-pack-payments` | ❔ Unconfirmed |
| [Paths that return 404](#paths-that-return-404) | ? | `/en/main/shopping` | ❔ Unconfirmed |
| [Collect daily Gold](#collect-daily-gold) | POST | `/en/main/collect-daily-gold/` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Gold Store page

`GET /en/main/gold-items` · Auth required · ✅ Verified 2026-11-27

HTML page, tab "Gold Store". The item cards are not in the HTML: the Angular controller `GoldShopController` loads them from [List items](shop.md#list-items) (`data.goldItems`), and buying goes through [Buy item](shop.md#buy-item). The path also answers with a trailing slash (`/en/main/gold-items/`), which the game's energy recovery links use.

```bash
curl 'https://www.erepublik.com/en/main/gold-items' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

The shop tabs link to `/en/main/special-items`, [`/en/main/vip-shop`](vip.md#vip-shop-page), `/en/main/gold-items` and [`/en/main/gold-bonus`](vip.md#gold-bonus-page).

**Source:** real request, 2026-11-27; `GoldShopController`, `GoldShopFactory` in `erpk_angular_controllers.js` (version 1795779904).

---

## Supply Shop fragment

`GET /en/main/shop-gold-items` · Auth required · ✅ Verified 2026-11-27

HTML fragment `section#globalShop`, a popup titled "Supply Shop" with fixed Gold prices. `/en/main/shop-gold-items/1` returned a response of the same size. The fragment has no script, and no caller was found in the current main and Angular scripts.

```bash
curl 'https://www.erepublik.com/en/main/shop-gold-items' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

| Selector | Meaning |
|----------|---------|
| `.tab_content[data-tab_name]` | `energy`, `boosters`, `bazookas`, `fuel` |
| `.shop_item[id]` | Item ID |
| `.shop_item[title]` | Item name |
| `.shop_item .cost` | Price text, e.g. `5 Gold` |

<details>
<summary>Items and prices, 2026-11-27</summary>

| Tab | ID | Item | Gold |
|-----|----|------|------|
| `energy` | `refill10pack` | 10 Energy Bars | 39 |
| `energy` | `refill5pack` | 5 Energy Bars | 22 |
| `energy` | `refill` | 1 Energy Bar | 5 |
| `energy` | `health` | First Aid Kit | 0.5 |
| `boosters` | `speedBooster1` | x2 Damage Accelerator | 1 |
| `bazookas` | `barrel5pack` | 55 Barrels | 2 |
| `bazookas` | `ammo5pack` | 55 Projectiles | 2 |
| `bazookas` | `stock5pack` | 55 Stocks | 2 |
| `bazookas` | `frame5pack` | 55 Trigger kits | 2 |
| `bazookas` | `scope5pack` | 55 Scopes | 2 |
| `fuel` | `fuel` | 1 Fuel | 1 |

</details>

**Source:** real request, 2026-11-27.

---

## Gift a friend popup

`GET /en/main/buy-forfriend?to_citizen_id={citizenId}` · Auth required · ✅ Verified 2026-11-27

HTML fragment `#buyForFriend`: the packs you can buy for another citizen.

| Name | In | Required | Description |
|------|----|----------|-------------|
| `to_citizen_id` | query | Yes | Recipient. Without it the server answers `500` |

```bash
curl 'https://www.erepublik.com/en/main/buy-forfriend?to_citizen_id=1234567' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

| Part of the fragment | Meaning |
|----------------------|---------|
| `.gold_and_items_pack a[data-type]` | Pack type of each "Send" button |
| script `friendId` | The recipient |
| script `canPurchaseItems` | `canPurchaseGold`, `canPurchaseGifts` flags (observed `true`, `false`) |

Pack types listed on 2026-11-27: `powerPack`, `infantryKit`, `blitzkriegPackBase`, `maverickPackBase`, `warStash2021`, `combatStash2021`, `tokensPack999`, `tokensPack599`, `tokensPack249`, `tokensPack99`, `tokensPack49`.

A "Send" button opens the [pack popup](shop.md#pack-popup) with `to_citizen_id` (🔍 From code).

The client opens this popup from any element with class `action_gifts`, taking the citizen ID from its `data-id`.

**Source:** real request, 2026-11-27; `promoPopup.init`, `renderPopup` in `erepublik.js` (version 1795779896).

---

## Send gift popup

`GET /en/main/gifts-pack` · Auth required · ✅ Verified 2026-11-27

HTML fragment `#gifts_pack_offer`: send "Gifts" from your account to a citizen.

| Name | In | Required | Description |
|------|----|----------|-------------|
| `to_citizen_id` | query | No | Recipient; without it the fragment has `to_citizen_id: 0` and no name |
| `giftQuality` | query | No | `0` (observed default) or `1`; copied into the send request |

```bash
curl 'https://www.erepublik.com/en/main/gifts-pack?to_citizen_id=1234567&giftQuality=0' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

| Part of the fragment | Meaning |
|----------------------|---------|
| `.gift_type` list | Contents of one gift |
| script `_giftPackMax` | Largest amount the client lets you send |
| script `_friendPackMax` | Recipient's limit; `null` observed |
| `#gifts_pack_offer[data-max-gifts]` | `0` in the example; meaning unconfirmed |

On 2026-11-27 one gift was listed as 5 Pumpkins, 1 Gold and 1 Prestige Points Booster.

**Source:** real requests, 2026-11-27.

---

## Send gifts

`POST /en/main/donate-gifts-action` · Auth required · 🔍 From code

> [!WARNING]
> Gives away gifts from your account.

| Name | Type | Description |
|------|------|-------------|
| `_token` | string | CSRF token |
| `to_citizen_id` | number | Recipient |
| `amount` | number | Number of gifts, at least `1` |
| `giftQuality` | number | Value the popup was opened with |

The response is JSON. Success is a falsy `error`; otherwise `error` is a code the client switches on:

| `error` | Meaning (from the name) |
|---------|-------------------------|
| `receiver_must_not_be_sender` | You cannot gift yourself |
| `amount_unavailable` | You do not have that many |
| `receiver_over_limit` | The recipient cannot receive that many |

**Source:** inline script of the [send gift popup](#send-gift-popup), 2026-11-27.

---

## Purchase thank-you popup

`GET /en/main/payment-success` · Auth required · ✅ Verified 2026-11-27

A static HTML fragment, `#thanks_4buy_gold`. Closing it reloads the page. It answered without any purchase having been made, so it confirms nothing; use [Payment status](shop.md#payment-status) for that.

```bash
curl 'https://www.erepublik.com/en/main/payment-success' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

```html
<div id="thanks_4buy_gold" class="thanks_4buy_gold" style="display:block;">
  <a id="close_thanks" href="javascript:location.reload(true);" class="close_action" title="Close window">…</a>
  <h2>Thank you for your purchase!</h2>
  <p>The items will be delivered in your account. We hope that you will enjoy your time in the New World!</p>
</div>
```

The client's popup name for it is `paymentSuccess`.

**Source:** real request, 2026-11-27; `erepublik.js` (version 1795779896).

---

## Collect daily Gold

`POST /en/main/collect-daily-gold/` · Auth required · 🔍 From code

> [!WARNING]
> Claims a reward on your account.

Sent by the Gold Pack button (`.gold_pack_btn`) on the My Companies page; `buy.js` also calls it with a `data-type` attribute on the same button.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |

```bash
curl 'https://www.erepublik.com/en/main/collect-daily-gold/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode '_token=YOUR_CSRF_TOKEN'
```

The client reads JSON: with `error` it shows `msg`, otherwise it reloads the page. The extra parameters of the `buy.js` call are not documented.

**Source:** `citizen/myCompanies.js` (version 1760600930).

---

## Paths that return 404

❔ Unconfirmed. All four answered `404` to a `GET` on 2026-11-27. The method of `shopping` is unknown.

| Path | What is known |
|------|---------------|
| `/en/main/starter-pack` | Earlier notes: starter pack purchase page for new players. Still referenced by `promoPopup.renderPopup('starterPack')` |
| `/en/main/gold-packs` | Earlier notes: purchase page with Gold bundle options. Still referenced by `renderPopup('goldPack')`; the [gift a friend](#gift-a-friend-popup) script calls this popup too |
| `/en/main/gold-pack-payments` | Earlier notes: Gold pack payment page that loads the PayPal Digital Goods script (`paypalobjects.com/js/external/dg.js`) and `/js/buy/buy.js`. Still referenced by `renderPopup('goldPackPayments')` |
| `/en/main/shopping` | No reference in the current main and Angular scripts |

**Source:** real requests, 2026-11-27; `renderPopup` in `erepublik.js` (version 1795779896); descriptions from earlier notes.

---

## Promo popups

The promotions are not fetched by a request of their own: logged-in pages declare a global `ErpkShop` in an inline script.

| Key | Type | Use |
|-----|------|-----|
| `promotions` | array | Rotating menu banner (`ErpkMenuPromosController`) |
| `popupPromotions` | array | Popup (`ErpkPopupPromosController`) |
| `battlePromotions` | array | Added in front of the popup list |
| `numberOfPurchasablePacks` | number | — |
| `specialSalesEvent` | boolean | — |

<details>
<summary>One promotion, 2026-11-27</summary>

```json
{
  "id": "blackFriday",
  "typeId": "blackFriday",
  "type": "promotion",
  "title": "Black Friday",
  "description": "Today only!",
  "lifeTime": 5,
  "showPopup": true,
  "shopUrl": "/en/main/special-items",
  "buttonImage": "//www.erepublik.net/images/modules/homepage/promo_blackFriday.png?1698060177",
  "buttonText": "#supplies !",
  "popupStyle": "blackFridayPack",
  "popupChar": 2,
  "newLabel": null,
  "timeLeft": "14 hours left",
  "lastDay": true,
  "expiresAt": 1795852800,
  "expiresIn": 51556
}
```

</details>

Client behaviour (🔍 From code):

- The button target is set by the client: the campaigns page for `type: "battlePromotion"`, the [Gold Store](#gold-store-page) for `trainingContract`, otherwise the [shop](shop.md#shop-page).
- The menu banner shows each entry for `lifeTime` seconds. The popup slides every 10 seconds.
- The popup is not shown while a session captcha or an achievement popup is active.
- `localStorage` key `promoPopup_YYYYMMDD` (server date; the month is zero-based) holds the next time the popup may appear: 1 hour after showing it, 2 hours after you close it.

The client uses `promoPopup.renderPopup(name, …)` to open pack popups: with `custom-pack` it calls the [pack popup](shop.md#pack-popup); the names `starterPack`, `giftsPack`, `goldPack`, `goldPackPayments` and `forFriendPopup` map to the paths on this page; any other name is used as a pack type.

`goldPackPaymentsSms` is a special case: the client loads the address passed as the fourth argument.

### Pack types known to the client

`promoPopup.packWithPayments` in `erepublik.js` (version 1795779896); descriptions from earlier notes. These are names in the script, not a list of packs on sale.

| Pack type | Description |
|-----------|-------------|
| `blankPack` | Blank or generic pack |
| `goldAndItemsPack` | Gold and items bundle |
| `starterPack` | Starter pack |
| `stashPack` | Stash pack |
| `assaultPack` | Assault pack |
| `powerPack` | Power pack |
| `economyPack` | Economy pack |
| `monthlyGoldPackSmall` | Monthly Gold (small) |
| `monthlyGoldPackBig` | Monthly Gold (large) |
| `giftsPack` | Gifts pack |
| `goldPack` | Gold pack |
| `blitzkriegPack` | Blitzkrieg pack |
| `camperPack` | Camper pack |
| `goldPackPayments` | Gold pack payments |
| `goldPackPaymentsSms` | Gold pack by SMS |

**Source:** page of 2026-11-27; `ErpkMenuPromosController`, `ErpkPopupPromosController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Open questions

| Topic | Earlier notes | Game today | Page follows |
|-------|---------------|------------|--------------|
| `starter-pack`, `gold-packs`, `gold-pack-payments` | Working popup pages (descriptions kept [above](#paths-that-return-404)) | `404`, though the script still references them | Game |
| `buy-forfriend`, `gifts-pack`, custom pack | Full page navigations | AJAX-loaded fragments | Game |
| `gifts-pack` `giftQuality` | "Pre-select gift quality tier" | Copied into the send request | Both |
| `shop-gold-items` | Not documented | Fragment answers; no caller found | Whether its items can still be bought is unknown |
