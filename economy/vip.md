# VIP and loyalty

[API docs](../README.md) › Economy › VIP and loyalty

Three account reward screens of the shop: VIP points and the VIP Shop, the Loyalty Program (Loyalty Points, LP) and the Gold Bonus for invited citizens. VIP items are listed and bought with the [Shop](shop.md#list-items) endpoints.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [VIP Shop page](#vip-shop-page) | GET | `/en/main/vip-shop` | ✅ Verified 2026-11-27 |
| [Claim daily VIP points](#claim-daily-vip-points) | POST | `/en/main/vip-claim` | 🔍 From code |
| [Loyalty Program page](#loyalty-program-page) | GET | `/en/main/loyalty` | ✅ Verified 2026-11-27 |
| [Gold Bonus page](#gold-bonus-page) | GET | `/en/main/gold-bonus` | ✅ Verified 2026-11-27 |
| [Collect Gold Bonus](#collect-gold-bonus) | POST | `/en/main/gold-bonus` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## VIP Shop page

`GET /en/main/vip-shop` · Auth required · ✅ Verified 2026-11-27

HTML page with your VIP status. The items are loaded from [List items](shop.md#list-items) (`data.vipShopItems`) and bought with [Buy item](shop.md#buy-item).

```bash
curl 'https://www.erepublik.com/en/main/vip-shop' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Selector or script value | Meaning |
|--------------------------|---------|
| `#vip_level` | VIP level |
| `#today_points` | "Today's points" |
| `#vip_points` / `#next_threshold` | Points and the next level's threshold, with thousands separators |
| `#vip_progress` | Progress bar, `width: calc(N% - 4px)` |
| `#claimDailyVIPPoints` / `#vip_claimed` | Claim button and "Claimed" label; the unavailable one has `display:none` |
| script `remainingTime` | Seconds until the stock refresh |
| script `erepublik.citizen.rawCurrencies` | Your raw materials and products, used to pay for VIP items |

<details>
<summary>Status banner (shortened, values replaced with 0)</summary>

```html
<div class="vip_banner">
  <div class="vip_icon"><span>VIP</span> <b id="vip_level">0</b></div>
  <div class="points">Today’s points: <span id="today_points">0</span></div>
  <div class="progress_bar">
    <div id="vip_progress" class="current_progress" style="width: calc(0% - 4px);"></div>
    <span><q id="vip_points">0</q> / <q id="next_threshold">0</q></span>
  </div>
  <a href="javascript:" id="claimDailyVIPPoints" style="display:none">…Claim…</a>
  <span id="vip_claimed" class="std_global_btn mediumSize blueColor disabled faded">Claimed</span>
</div>
```

</details>

An item above your level shows "From VIP N".

**Source:** real request, 2026-11-27.

---

## Claim daily VIP points

`POST /en/main/vip-claim` · Auth required · 🔍 From code

> [!WARNING]
> Changes your account: collects the day's VIP points.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |

```bash
curl 'https://www.erepublik.com/en/main/vip-claim' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Referer: https://www.erepublik.com/en/main/vip-shop' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

Once per game day; resets at 00:00 game time (earlier notes). Earlier notes recommend the `Referer` header.

### Response

On success the client writes **every** key of the response into the page element with the same ID, then reads these:

| Field | Type | Description |
|-------|------|-------------|
| `vip_points` | number | Points; formatted by the client |
| `next_threshold` | number | Next level's threshold |
| `can_claim` | boolean | Whether the claim button stays |
| other keys | — | Matched to element IDs such as `vip_level`, `today_points` |

### Errors

A truthy `error` makes the client show `message`.

<details>
<summary>Examples from earlier notes</summary>

```json
{"error": false, "success": true}
```

```json
{"error": true, "message": "Already claimed"}
```

</details>

**Source:** inline script of the [VIP Shop page](#vip-shop-page), function `claimDailyVip`, 2026-11-27; earlier notes (from the ePlus userscript, `claimVip.ts`).

---

## Loyalty Program page

`GET /en/main/loyalty` · Auth required · ✅ Verified 2026-11-27

HTML page, `#top_customers`: your loyalty status and the benefit table. The page only displays data. Loyalty Points are spent on [shop items](shop.md#buy-item) with `currency=lp`.

```bash
curl 'https://www.erepublik.com/en/main/loyalty' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Selector | Meaning |
|----------|---------|
| `.lp_container h2` | Status name, "… Player" |
| `.benefit_details` | Benefits of your level, as text |
| `.progress_metter` width, `.percentage` | Progress to the next status |
| `.lp_progress .info` | LP left until the next status |
| `.extended_info strong` | Your current number of LP |
| `.lp_container_details table` | All five levels; your column has class `active` |

### Levels

Values of the table on 2026-11-27, columns left to right. The header row has no level names in its text.

| Level column | 1 | 2 | 3 | 4 | 5 |
|--------------|---|---|---|---|---|
| LP earned per Euro | 0.5x | 1x | 2x | 3x | 4x |
| Bonus LP received | 100 | 400 | 1000 | 2000 | 2500 |
| Extra Gold on every purchase | 0% | 5% | 10% | 15% | 20% |
| LP required to reach the next level | 25 | 900 | 8000 | 15000 | locked |

**Source:** real request, 2026-11-27.

---

## Gold Bonus page

`GET /en/main/gold-bonus` · Auth required · ✅ Verified 2026-11-27

HTML page. Citizens you invited bring you 10% of the Gold they get (achievements, level ups, purchases). According to the page, each bonus must be collected within 30 days or it is lost.

| Name | In | Required | Description |
|------|----|----------|-------------|
| `page` | path | No | `/en/main/gold-bonus/{page}`. An out-of-range page (`999`) ends at `/en/main/gold-bonus` |

```bash
curl 'https://www.erepublik.com/en/main/gold-bonus/2' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

| Selector | Meaning |
|----------|---------|
| `#collectMe b` | Gold waiting to be collected; class `inactive` when there is none |
| `#collectShares` | Form used to [collect](#collect-gold-bonus) |
| `.collected_gold_list` rows | `citizen_name` (profile link), `experience_level`, `collected_gold` |
| pager links `first`, `prev`, `next`, `last` | Other pages; the current one has class `on` |

<details>
<summary>One table row (placeholders)</summary>

```html
<tr>
  <td class="citizen_name"><a href="/en/citizen/profile/1234567" title="CitizenName">CitizenName</a></td>
  <td class="experience_level"><strong>0</strong></td>
  <td class="collected_gold"><strong>0</strong></td>
</tr>
```

</details>

**Source:** real requests, 2026-11-27.

---

## Collect Gold Bonus

`POST /en/main/gold-bonus` · Auth required · 🔍 From code

> [!WARNING]
> Changes your account: collects the pending Gold.

| Name | Type | Description |
|------|------|-------------|
| `_token` | string | CSRF token (hidden field of the form) |

A normal form submit, not AJAX: `#collectShares` has `method="post"` and an empty `action`, so it posts to the URL of the page you are on. The click is ignored while the button is `inactive`. The response was not observed.

```bash
curl 'https://www.erepublik.com/en/main/gold-bonus' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

**Source:** HTML and inline script of the [Gold Bonus page](#gold-bonus-page), 2026-11-27.

---

## Open questions

| Topic | Earlier notes | Game code | Page follows |
|-------|---------------|-----------|--------------|
| `vip-claim` success response | `{"error": false, "success": true}` | Reads `vip_points`, `next_threshold`, `can_claim` | Both shown; not verified |
| Loyalty level names | Five levels, Bronze to Diamond | The table has no names; the page shows only your own status name and badge | Columns numbered |
