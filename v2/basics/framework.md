# Client framework

[API docs](../README.md) › Basics › Client framework

`erepublik.{timestamp}.js` (about 411 KB) and `global.{timestamp}.js` load on game pages. They provide the popups, the JavaScript globals that tools read, and a few site-wide requests.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Country by IP](#country-by-ip) | GET | `/en/main/geoLocation/countryCode` | ✅ Verified 2026-11-27 |
| [Menu badges](#menu-badges) | GET | `/en/main/citizenDailyAssistant` | ✅ Verified 2026-11-27 |
| [Current user](#current-user) | GET | `/en/main/user-info` | ✅ Verified 2026-11-27 |
| [Look up a citizen](#look-up-a-citizen) | POST | `/en/main/user-info` | 🔍 From code |
| [Close a global alert](#close-a-global-alert) | POST | `/en/main/global-alerts/close` | 🔍 From code |
| [Support page](#support-page) | GET | `/en/main/tickets` | ✅ Verified 2026-11-27 |
| [Report content](#report-content) | GET | `/en/main/tickets-report/{id}/{type}` | 🔍 From code |
| [Outbound links](#outbound-links) | GET | `/en/main/ga_track/{page}` | 🔍 From code |
| [Facebook sync](#facebook-sync) | POST | `/en/main/facebook-sync` | 🔍 From code |
| [Facebook account link](#facebook-account-link) | GET | `/en/facebook-register` | 🔍 From code |
| [Facebook story](#facebook-story) | POST | `/en/main/facebook-stories/` | 🔍 From code |
| [New-citizenship banner](#new-citizenship-banner) | GET | `/en/main/citizenship-banner` | ✅ Verified 2026-11-27 |
| [New countries](#new-countries) | POST | `/en/main/citizenship-countries` | 🔍 From code |
| [Applicant list](#applicant-list) | GET | `/en/main/citizenship-statuslist` | ✅ Verified 2026-11-27 |
| [Apply for citizenship](#apply-for-citizenship) | POST | `/en/main/citizenship-participate` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Scripts

Game scripts are served from `https://www.erepublik.net/js/…`; the number in a file name is its build timestamp.

| Script | Content |
|--------|---------|
| `cmp/erepublik.{ts}.js` | Popups, utilities, [session CAPTCHA](captcha.md), shop navigation, tournaments, events |
| `cmp/erpk_angular_init.{ts}.js`, `…_controllers`, `…_services`, `…_directives`, `…_filters` | The AngularJS application, including the [WebSocket](websocket.md) service |
| `global.{ts}.js` | Header, side bar, menu badges (`globalNS`) |
| `facebook/facebook_canvas.{ts}.js` | Facebook integration |
| `pomelo/lib/build/build.js` | [WebSocket](websocket.md) client library |

The main bundle holds `generalPopup`, `promoPopup`, `sessionCaptcha`, `countriesTournament`, `epicSoldier`, `erepublik.functions`, `ClassicCaptcha` and `erepublik.subtleCrypto`.

### Building URLs

```javascript
var url = '/' + erepublik.settings.culture + '/main/endpoint-name';          // /en/main/endpoint-name
// used for tournament rankings:
var absolute = '//' + erepublik.settings.hostname + '/' + erepublik.settings.culture + '/main/endpoint-name';
```

The CSRF token for `_token` comes from `SERVER_DATA.csrfToken`; some older code reads the hidden field `#_token`.

---

## JavaScript globals

Embedded in the page HTML at render time, so they do not update on their own. Full examples of most of them are on the [Dashboard](../home/dashboard.md#embedded-data-objects) page.

### `erepublik`

Keys seen on `/en` (2026-11-27): `settings`, `citizen`, `info`, `i18n`, `promos`, `shortcuts`, `angularTemplates`, `functions`, `subtleCrypto`, `directives`, `mission`, `debug`.

### `erepublik.settings`

Fields: see [Dashboard](../home/dashboard.md#erepubliksettings). In addition:

| Property | Description |
|----------|-------------|
| `culture` | Language segment of every URL |
| `cookielessHost` | Static asset host, served without cookies |
| `notifications` | Unread count, also drawn on the browser tab icon (Tinycon) |
| `achievementPopup` | When truthy, `generalPopup.renderPopup()` does nothing, so popups do not overlap |
| `pomelo` | [WebSocket](websocket.md) settings, including a secret token |

### `erepublik.citizen`

The logged-in citizen, on authenticated pages. Types below were checked on `/en` on 2026-11-27.

| Property | Type | Description |
|----------|------|-------------|
| `citizenId`, `name`, `avatar` | number, string | Identity |
| `citizenshipCountryId` | number | Citizenship country |
| `country` | number | Not documented yet |
| `citizenshipCurrencyName`, `currency` | string | Local currency |
| `currencyAmount`, `gold` | number | Balances |
| `countryLocationId`, `countryLocationName`, `countryLocationPermalink` | number, string | Country where you are now (differs from citizenship abroad) |
| `regionLocationId` | number | Region where you are now |
| `residence` | object | `hasResidence`, `cityId`, `regionId`, `countryId`, `bonuses` |
| `energy`, `energyPoolLimit` | number | Energy and its maximum |
| `energyToRecover`, `recoverableEnergy` | number | Missing up to the limit; recoverable right now |
| `energyPerInterval` | number | Recovery per interval (6 minutes) |
| `energyFromFoodRemaining`, `hasFoodInInventory` | number, boolean | Food |
| `energyStatus` | string | For example `recovering` |
| `canWorkTrainAgainIn` | number | Seconds until work and training are available |
| `userLevel`, `currentExperiencePoints` | number | Level and XP |
| `loyaltyPoints` | number | Loyalty points |
| `division`, `canSwitchDivisions` | number, boolean | Military division (1–4, 11) |
| `muId`, `muCountryId`, `regimentId` | number | Military unit |
| `dailyOrders` | array | Daily order battles |
| `dailiesToCollect` | number | Daily tasks ready to collect |
| `party` | object | `id`, `party_id`, `is_party_president`, `name`, `economical_orientation`, `stripped_title`, `has_avatar`, `created_at`, `joined_at` |
| `terrainSkills` | object | Skill per terrain ID |
| `canChangeMPPPriorities`, `canChangeTerrainPriorities` | boolean | Government permissions |
| `isEmpire` | boolean | Government of the current location |
| `isDictatorship`, `isOrganization` | number | Government of the current location; account type |
| `isNewCitizen`, `isAdult`, `isTopCustomer`, `hasCoreLoopAccess` | boolean | Account flags |
| `isActive` | function | Listed as a boolean in earlier notes |
| `boxKeys` | array | Not documented yet |

`gold`, `currencyAmount`, `energy`, `energyPoolLimit`, `recoverableEnergy` and `userLevel` are visible only to their owner, so real values prove an authenticated session. There is no `prestige` field.

### `erepublik.info`

Static country data: `countries` (`id`, `name`, `permalink`, `isEmpire`, `isDictatorship`) with the helpers `getCountryById`, `getCountryPermalink`, `getCountryName`, `getCountryFlag`, `updateCountryInfo`, `isEmpire` and `isDictatorship`. The values are listed in [ID tables](id-tables.md).

### `SERVER_DATA`

Page-specific data. Keys on `/en`: `csrfToken`, `isLoggedIn`, `serverTime`, `extras`, `wallData`, `rwSupport`, `webDeployEnabled`, `fbAppId`, `fbApiVersion`, `fbPixelId`, `gaPropertyId`.

- `csrfToken` is needed for `POST` requests.
- `sessionValidation` appears during a [verification](captcha.md#when-it-appears).
- Battle pages: see [Battlefield page](../military/battlefield/page.md).

### `erepublik.angularTemplates`

Overrides for template paths. `ERPK.angular.$templatePath(controller)` removes `Erpk` and `Controller` from the name, looks it up here, and falls back to `/templates/{name}.tpl.html`.

### `erepublik.subtleCrypto`

Wrapper around the browser's Web Crypto API; `null` when `crypto.subtle` is missing.

| Method | Returns |
|--------|---------|
| `textEncoder(string)` | Bytes |
| `hexEncoder(buffer)` | Hex string |
| `digest(algorithm, text)` | Promise of a hex string; `SHA-1`, `SHA-256` (default), `SHA-384`, `SHA-512` |

### `erepublik.functions`

| Method | Description |
|--------|-------------|
| `acceptRejectDonation(action, id)` | Accept or reject a money donation ([description](../social/organizations.md#accept-or-reject-a-donation)) |
| `launchTravelPopup(options)` | Travel popup; `holdingId`, `battleId`, `regionId` |
| `openMapPopup()` | World map |
| `liveClock(elementId, hours, minutes)` | Header clock |
| `formatNumber(number)` | Number formatting |
| `displayHumanMsgError(message)` | Error notification |
| `shortcuts()` | Keyboard shortcuts |
| `fbTrackEvent(...)` | Facebook tracking |
| `confirmationAlert(...)` | Confirmation dialog |

---

## Popups

`generalPopup.renderPopup(name, options)` maps a name to a path, loads the HTML with `GET` and shows it in `#promoPopupHolder`. An unknown name throws.

| Option | Default | Description |
|--------|---------|-------------|
| `params` | `{}` | Query parameters |
| `redraw` | `false` | Load again even if cached; forced for non-`GET` |
| `method` | `GET` | HTTP method |
| `contentLabel` | popup name | Cache key |

Other methods: `init()` (binds the new-citizenship banner), `getScripts(urls)` (returns a promise), `compileAngularElement(selector)`, `onLoad()`, `onClose()`. The world map also has its own `erepublik.functions.openMapPopup()`.

Popup pages need authentication. Send `X-Requested-With: XMLHttpRequest`.

<details>
<summary>Popup names and paths</summary>

| Popup name | Path | Documented in |
|------------|------|---------------|
| `ncGoldRush` | `/main/citizenship-banner` | [below](#new-citizenship-banner) |
| `travelPopup` | `/main/travelPopup` | [travel](../world/travel.md) |
| `map` | `/main/world-map` | [map](../world/map.md) |
| `createHolding`, `createHoldingCompanyPopup` | `/economy/create-holding-popup` | [companies](../economy/companies.md#create-holding-popup) |
| `editHolding`, `editHoldingCompanyPopup` | `/economy/edit-holding-popup` | [companies](../economy/companies.md#edit-holding-popup) |
| `assignToHolding`, `assignFactoryToHoldingPopup` | `/economy/assign-to-holding-popup` | [companies](../economy/companies.md#move-one-company-popup) |
| `assignMultipleFactoriesToHoldingPopup` | `/economy/assign-multiple-to-holding-popup` | [companies](../economy/companies.md#move-several-popup) |
| `unassignMultipleFactoriesPopup` | `/economy/unassign-factories-popup` | [companies](../economy/companies.md#unassign-popup) |
| `recoverEnergy`, `energyRefillPopup` | `/main/energyRefill-refillPopup` | [energy](../home/energy.md) |
| `weeklyChallengeRewardsList` | `/main/weekly-challenge-rewards` | [weekly challenge](../home/weekly-challenge.md) |
| `powerSpinRewardsPopup` | `/main/powerSpinRewardsPopup` | [power spin](../home/power-spin.md) |
| `deployReportPopup` | `/military/fightDeploy-deployReportPopup` | [deployment](../military/battlefield/deployment.md) |
| `presetPopup`, `filtersPresetPopup` | `/main/preset-popup` | [campaigns](../military/campaigns.md) |
| `terrainPriorities` | `/main/terrain-priorities-popup` | [campaigns](../military/campaigns.md) |
| `strengthEvent` | `/main/strength-popup` | [live data](../military/battlefield/live-data.md) |
| `anniversaryQuestHelp` | `/main/anniversaryQuestHelp` | [An Amazing Journey](../events/amazing-journey.md) |
| `mppPriority` | `/main/country-mpp-priority` | [military](../country/military.md#allied-forces-popup) |
| `leagueOfAllies` | `/main/league-info` | [alliances](../country/alliances.md#league-popup) |
| `manageOrganizations` | `/main/manage-organizations-popup` | [organizations](../social/organizations.md#manage-organizations-popup) |
| `cityHelpPopup` | `/main/city-help-popup` | [cities](../world/cities.md#help-popup) |
| `firstMessage` | `/main/first-message-popup` | [messages](../social/messages.md#first-message-popup) |
| `paymentSuccess` | `/main/payment-success` | [shop screens](../economy/shop-screens.md#purchase-thank-you-popup) |
| `multipleAircraftZonesPopup` | `/main/multipleAircraftZonesPopup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `cryptoAssetsPopup`, `cryptoEventPopup` | `/main/cryptoAssetsPopup`, `/main/cryptoEventPopup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `deployLootPopup` | `/main/deployLootPopup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `revivalPopup`, `revivalEventPopup` | `/main/revival-popup`, `/main/revivalEventPopup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `erepublikAnniversary` | `/main/anniversary-popup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `halloweenPopup`, `halloween2017`–`halloween2020` | `/main/halloweenPopup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `halloweenRewards`, `easterRewards` | `/main/halloweenRewards`, `/main/easterRewards` | [dormant event code](../events/dormant-code.md#event-popups) |
| `christmasGifts`, `aprilsFools` | `/main/christmas-gifts`, `/main/aprilsFoolsPopup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `valentinesDayPopup`, `winterOlympicsPopup` | `/main/valentinesDayPopup`, `/main/winterOlympicsPopup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `childrensDayPopup`, `summerChallengeTop5` | `/main/childrensDayPopup`, `/main/summerChallengeTop5` | [dormant event code](../events/dormant-code.md#event-popups) |
| `day6000Popup` | `/main/day6000Popup` | [dormant event code](../events/dormant-code.md#event-popups) |
| `dailyMissionsPopup` | template `DailyMissions` | [daily missions](../home/daily-missions.md) |
| `citizenRevivalPopup` | template `CitizenRevival` | — |
| `citizenRevivalLoginRewardsPopup` | template `LoginRewardsRevival` | — |

The three template popups load `/templates/{name}.tpl.html` through their Angular controller. Earlier notes also list `sessionUnlockPopup`; the code opens it [directly](captcha.md#dialog).

</details>

Overlay darkness: `firstMessage` 0.95, `halloweenPopup` 0.80, `strengthEvent`, `revivalPopup` and `citizenRevivalLoginRewardsPopup` 0.75, `leagueOfAllies` 0.70, others 0.30. `firstMessage` does not close on an outside click.

### `promoPopup`

Shop popups. `renderPopup(pack, returnTo, citizenId, type, lightboxOpts)`; also `init()`, `onClose()`, `beforeSend()` (loads payment scripts) and `goToShop(showType, showPack)`, which navigates to `/main/special-items`.

| Call | Loads |
|------|-------|
| `renderPopup('starterPack')` | `/main/starter-pack` |
| `renderPopup('giftsPack')` | `/main/gifts-pack` |
| `renderPopup('goldPack')` | `/main/gold-packs` |
| `renderPopup('goldPackPayments')` | `/main/gold-pack-payments` |
| `renderPopup(packType, false, 0, 'custom-pack')` | `/main/custom-pack?packType={packType}` |
| `renderPopup('forFriendPopup', false, citizenId)` | `/main/buy-forfriend?to_citizen_id={citizenId}` |

See [Shop](../economy/shop.md).

### `ClassicCaptcha`

A wrapper around Google reCAPTCHA or Cloudflare Turnstile, separate from the [session CAPTCHA](captcha.md). Pages load `challenges.cloudflare.com/turnstile/v0/api.js`.

---

## Static asset paths

| Path | Content |
|------|---------|
| `/images/flags_png/{S,M,L,XL}/{permalink}.png` | Flags |
| `/images/flags/{size}/{permalink}.gif` | Flags (older) |
| `/images/modules/misc/session_captcha_script_detected.jpg` | Verification warning |
| `/js/cmp/map.js` | World map script |
| `/js/buy/buy.js` | Shop script |
| `/css/sessionCaptcha/sessionCaptcha.{ts}.css`, `sliderCaptcha.{ts}.css` | Verification styles |
| `/templates/{name}.tpl.html` | Angular templates |

---

## Country by IP

`GET /en/main/geoLocation/countryCode` · Auth not required · ✅ Verified 2026-11-27

The country of your IP address. The logged-out home page uses it to pick a background for `US`, `CN`, `GR`, `TR`, `FR` and `GB`; the script skips the request on the logged-out newspaper page.

```bash
curl 'https://www.erepublik.com/en/main/geoLocation/countryCode'
```

```json
{ "countryCode": "PL" }
```

| Field | Type | Description |
|-------|------|-------------|
| `countryCode` | string | ISO 3166-1 alpha-2 code |

**Source:** real request with a session, 2026-11-27; anonymous access from earlier notes; `erepublik.js` (version 1795779896) calls it when the page has `#homepage_out` and no `#newspaper_out`.

---

## Menu badges

`GET /en/main/citizenDailyAssistant` · Auth required · ✅ Verified 2026-11-27

Dots and short texts on the main menu. The client puts each entry on the element whose `data-notify` equals the key.

```bash
curl 'https://www.erepublik.com/en/main/citizenDailyAssistant' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

The request returned an empty array (nothing to show). Fields the client reads from each entry:

| Field | Description |
|-------|-------------|
| `type` | CSS class of the badge |
| `text` | Badge text; empty or `"1"` draws a plain dot |
| `tooltip` | Optional hover text |

A truthy `error` means failure.

**Source:** real request, 2026-11-27; `global.js` (version 1793955735), `updateDotNotifications` and `initDotNotifications`.

---

## Current user

`GET /en/main/user-info` · Auth required · ✅ Verified 2026-11-27

Name and avatar of the logged-in citizen.

```bash
curl 'https://www.erepublik.com/en/main/user-info' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

```json
{ "error": false, "message": "", "user": "CITIZEN_NAME", "avatar": "https://cdnt.erepublik.net/..." }
```

The response is JSON although its content type is `text/html`.

**Source:** real request, 2026-11-27. No caller of the `GET` was found in the scripts that were read.

---

## Look up a citizen

`POST /en/main/user-info` · Auth required · 🔍 From code

Name and avatar of a given citizen. The [alliance page](../country/alliances.md#propose-an-alliance-law) calls it before proposing a new leader.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `citizenId` | number | Yes | Citizen to look up |

```bash
curl 'https://www.erepublik.com/en/main/user-info' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&citizenId=1234567'
```

The client reads `error`, `message`, `avatar` and `user`.

**Source:** `alliances/main.js` (version 1698060179), as recorded on the [alliances](../country/alliances.md#propose-an-alliance-law) page.

---

## Close a global alert

`POST /en/main/global-alerts/close` · Auth required · 🔍 From code

> [!WARNING]
> Dismisses an alert on your account.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `alert_id` | — | Yes | ID of the notification |
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |

```bash
curl 'https://www.erepublik.com/en/main/global-alerts/close' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'alert_id=ALERT_ID&_token=YOUR_CSRF_TOKEN'
```

The client uses it for the notification types `jackpot`, `empire`, `ongoingRw`, `partyAlerts`, `citizenAchievement` and `boxKeyReceived`. When the player follows the alert's link, the page is opened after a successful answer.

**Source:** `erpk_angular_controllers.js` (version 1795779904), `hideGlobalNotify`.

---

## Support page

`GET /en/main/tickets` · Auth required · ✅ Verified 2026-11-27

The eRepublik Support page (HTML). The verification box links to it as "Customer Service".

```bash
curl 'https://www.erepublik.com/en/main/tickets' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** real request, 2026-11-27; only the page load was checked.

---

## Report content

`GET /en/main/tickets-report/{id}/{type}` · Auth required · 🔍 From code

> [!WARNING]
> Not requested: it belongs to reporting content to the moderators.

| Name | In | Description |
|------|----|-------------|
| `id` | path | Reported item |
| `type` | path | Kind of item; `wall-post` was seen |

```bash
curl 'https://www.erepublik.com/en/main/tickets-report/ITEM_ID/wall-post' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

Wall posts carry the full link in `reportURL`; the **Report** button opens it.

**Source:** links on the home feed, 2026-11-27 (not followed).

---

## Outbound links

`GET /en/main/ga_track/{page}` · Auth unknown · 🔍 From code

Footer links that pass through the game for tracking. Values of `page` seen: `wiki`, `forum`, `latest-updates`, `rules`, `terms`, `privacy`, `contact`, `jobs`.

```bash
curl 'https://www.erepublik.com/en/main/ga_track/wiki'
```

**Source:** links on `/en`, 2026-11-27 (not followed).

---

## Facebook sync

`POST /en/main/facebook-sync` · Auth unknown · 🔍 From code

> [!WARNING]
> Links, logs in or registers an account through Facebook. It sends Facebook credentials.

| Name | Description |
|------|-------------|
| `_token` | CSRF token |
| `_fbResponse` | Result of Facebook's `/me` call with `email` |
| `api` | Facebook API used |
| `authResponse` | Facebook authentication object |
| `signed_request`, `accessToken` | Taken from `authResponse` |

The `response` field of the answer selects the next step: `found_existing_account` (link), `facebook_login` (go to `/en/facebook/login`) or `facebook_register`.

No cURL example: the values are objects and credentials produced by Facebook's script, and how the client encodes them was not recorded.

**Source:** `facebook_canvas.js` (version 1793955735), `facebook.sync`.

---

## Facebook account link

`GET /en/facebook-register` · Auth unknown · 🔍 From code

> [!WARNING]
> Part of linking a Facebook account; its server-side effect is unknown.

`facebook.linkAccount` requests it. On the answer `0` the client goes to `/en/facebook/login`; otherwise it follows the link in `#final_click`.

```bash
curl 'https://www.erepublik.com/en/facebook-register' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

**Source:** `facebook_canvas.js` (version 1793955735).

---

## Facebook story

`POST /en/main/facebook-stories/` · Auth required · 🔍 From code

Asks for the text of a story before opening Facebook's share dialog (`.post_reward` buttons).

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `storyId` | string | Yes | Button attribute `data-story` |
| `storyParams` | string | Yes | Button attribute `data-params` |

```bash
curl 'https://www.erepublik.com/en/main/facebook-stories/' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode '_token=YOUR_CSRF_TOKEN' \
  --data-urlencode 'storyId=STORY_ID' \
  --data-urlencode 'storyParams=STORY_PARAMS'
```

The client reads `err`, `name`, `caption`, `description` and a link.

**Source:** `global.js` (version 1793955735).

---

## New-citizenship banner

`GET /en/main/citizenship-banner` · Auth required · ✅ Verified 2026-11-27

HTML popup "Become a Founding Father of these new countries", opened by `#nc_gold_rush_banner`. Its inline script calls the three requests below, with the culture from `SERVER_DATA.culture` or `en`.

```bash
curl 'https://www.erepublik.com/en/main/citizenship-banner' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

**Source:** real request, 2026-11-27.

---

## New countries

`POST /en/main/citizenship-countries` · Auth required · 🔍 From code

The countries shown in the banner.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |

```bash
curl 'https://www.erepublik.com/en/main/citizenship-countries' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN'
```

The answer is an array:

| Field | Description |
|-------|-------------|
| `country_id`, `country_name` | Country |
| `status` | `in_progress`, `finished`, or not started yet |
| `time_left` | Seconds; the banner draws progress over 86400 |
| `can_apply`, `has_applied` | Your state |

**Source:** inline script of the banner, 2026-11-27.

---

## Applicant list

`GET /en/main/citizenship-statuslist` · Auth required · ✅ Verified 2026-11-27

Citizens who applied for one of the new countries.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `id` | query | number | Yes | Country ID |
| `_token` | query | string | Sent by the client | CSRF token |

```bash
curl 'https://www.erepublik.com/en/main/citizenship-statuslist?id=1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

For a country with no list, the real request (sent without `_token`) returned `false`. Otherwise the client expects an array:

| Field | Description |
|-------|-------------|
| `id`, `name` | Citizen |
| `status` | `new` (registered, hidden by the banner), `accepted` or `denied` |
| `citizenship_type` | `native` marks native citizens |

**Source:** real request, 2026-11-27; fields from the banner's inline script.

---

## Apply for citizenship

`POST /en/main/citizenship-participate` · Auth required · 🔍 From code

> [!WARNING]
> Applies for citizenship of a new country.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `id` | number | Yes | Country ID |
| `_token` | string | Yes | CSRF token |

```bash
curl 'https://www.erepublik.com/en/main/citizenship-participate' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'id=COUNTRY_ID&_token=YOUR_CSRF_TOKEN'
```

**Source:** inline script of the banner, 2026-11-27.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| `citizen.dailiesToCollect` | Array | Number | Live page |
| `citizen.isDictatorship` | Boolean | Number | Live page |
| `citizen.isActive` | Boolean | Function | Live page |
| `citizen.ctCountryId` | Citizenship country | Not present on `/en` | Live page |
| `subtleCrypto` use | Builds the `env` value of the [session CAPTCHA](captcha.md) | The CAPTCHA functions that build and send `env` do not call it | Not settled |
