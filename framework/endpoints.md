# Framework Bundle — Endpoints

#erepublik #api #framework #endpoints

[< Back to Framework Reference](README.md)

All endpoints extracted from the main framework bundle `erepublik.{ts}.js`. Organized by type: JSON data endpoints (most useful for scripting), HTML popup endpoints, and shop/navigation endpoints.

---

## JSON Data Endpoints

These return JSON responses and are the most useful for scripting.

---

### GeoLocation Country Code

**Method:** GET
**URL:** `/{culture}/main/geoLocation/countryCode`
**Auth Required:** No
**Called by:** `$.getJSON()` on landing page

#### Description

Detects the user's country based on their IP address. Used on the landing/newspaper page to apply country-specific CSS themes (background images for US, CN, GR, TR, FR, GB).

#### Parameters

None

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/geoLocation/countryCode'
```

</details>

<details>
<summary>Example Response (Expected)</summary>

```json
{
  "countryCode": "PL"
}
```

</details>

#### Notes

- No authentication required — works for anonymous visitors
- The response `countryCode` is an ISO 3166-1 alpha-2 code (uppercase)
- The frontend checks for specific codes: `US`, `CN`, `GR`, `TR`, `FR`, `GB` to apply themed backgrounds
- Called only on landing page (`#newspaper_out`) and homepage

---

### Accept/Reject Money Donation

**Method:** GET
**URL:** `/{culture}/main/money-donation/{action}/{donationId}`
**Auth Required:** Yes
**Called by:** `$.get()` in `erepublik.functions.acceptRejectDonation(action, donationId)`

#### Description

Accepts or rejects a pending money donation (e.g., a treasury donation proposed by Congress). On success, the page reloads. On error, displays a human-readable error message.

#### Parameters

| Name | Location | Type | Required | Description |
|------|----------|------|----------|-------------|
| action | URL path | string | Yes | The action to perform (e.g., `accept` or `reject`) |
| donationId | URL path | integer | Yes | The ID of the donation to act upon |
| _token | query | string | Yes | CSRF token from `$j('#_token').val()` |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/money-donation/accept/123456?_token=YOUR_CSRF_TOKEN' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

</details>

<details>
<summary>Example Response (Expected)</summary>

```json
{
  "message": ""
}
```

</details>

#### Notes

- Response is parsed with `$.parseJSON()` from an HTML response — the actual Content-Type may be `text/html`
- An empty `message` indicates success; non-empty `message` is displayed as an error
- The page reloads after the call regardless of success/failure
- The `_token` is passed as a query parameter (inside the `data` object of `$.get()`)
- Related to Congress treasury donation proposals (see [country/administration.md](../country/administration.md))

---

### Countries Tournament — National Rankings

**Method:** GET
**URL:** `//{hostname}/{culture}/main/countries-tournament-national-rankings/{countryId}`
**Auth Required:** No (cross-origin `$.getJSON`)
**Called by:** `countriesTournament.getNationalData(countryId)`

#### Description

Retrieves national rankings for a specific country in the Countries Tournament event. Returns player rankings and country filter data. If no `countryId` is provided, defaults to the logged-in citizen's citizenship country (`erepublik.citizen.ctCountryId`).

#### Parameters

| Name | Location | Type | Required | Description |
|------|----------|------|----------|-------------|
| countryId | URL path | integer | Yes | Country ID (see [reference/countries.md](../reference/countries.md)) |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/countries-tournament-national-rankings/35'
```

</details>

<details>
<summary>Expected Response Structure</summary>

```json
{
  "top": [
    {
      "id": 12345,
      "name": "PlayerName",
      "damage": 1000000,
      "percent": "25.50",
      "avatar": "..."
    }
  ],
  "myPosition": {
    "id": 67890,
    "countryId": 35,
    "damage": 500000,
    "percent": "12.30"
  },
  "country_filter": [
    { "id": "167", "name": "Albania", "permalink": "Albania" },
    { "id": "27", "name": "Argentina", "permalink": "Argentina" }
  ]
}
```

</details>

#### Notes

- Cross-origin request (uses `//hostname/...` protocol-relative URL)
- The `country_filter` array in the response is overridden client-side with a hardcoded list of all countries — the server may return a different list
- The response is cached client-side (`nationalData`) — subsequent calls return cached data unless forced
- Tournament events are seasonal; this endpoint may return errors outside tournament periods
- The `top` array contains the top-ranked players for that country
- `myPosition` contains the current citizen's position (if logged in and participating)

---

### Countries Tournament — World Rankings

**Method:** GET
**URL:** `//{hostname}/{culture}/main/countries-tournament-world-rankings`
**Auth Required:** No
**Called by:** `countriesTournament.getWorldData()`

#### Description

Retrieves the worldwide country rankings for the Countries Tournament event.

#### Parameters

None

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/countries-tournament-world-rankings'
```

</details>

<details>
<summary>Expected Response Structure</summary>

```json
{
  "top": [
    {
      "id": 35,
      "name": "Poland",
      "damage": 50000000,
      "percent": "15.20"
    }
  ],
  "myPosition": {
    "id": 35,
    "countryId": 35,
    "damage": 50000000
  }
}
```

</details>

#### Notes

- Same caching behavior as national rankings
- `myPosition` reflects the logged-in citizen's country ranking
- The `top` array contains the top-ranked countries worldwide

---

### Countries Tournament — Finalist Rankings

**Method:** GET
**URL:** `//{hostname}/{culture}/main/countries-tournament-world-rankings/finalists`
**Auth Required:** No
**Called by:** `countriesTournament.getFinalistsData()`

#### Description

Retrieves finalist country rankings for the Countries Tournament — the final round of the tournament with top qualifying countries.

#### Parameters

None

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/countries-tournament-world-rankings/finalists'
```

</details>

#### Notes

- Same response structure as world rankings
- Only available during the finalist phase of the tournament
- Triggered by clicking "Finalists" tab in the tournament UI

---

### Epic Soldier Event — Citizen Rankings

**Method:** GET
**URL:** `//{hostname}/{culture}/main/epic-soldier-event-citizens-rankings/{countryId}/{divisionId}`
**Auth Required:** No
**Called by:** `epicSoldier.getNationalData(countryId, force, divisionId)`

#### Description

Retrieves citizen rankings for a specific country in the Epic Soldier Event. Similar structure to Countries Tournament rankings but includes a division parameter.

#### Parameters

| Name | Location | Type | Required | Description |
|------|----------|------|----------|-------------|
| countryId | URL path | integer | Yes | Country ID (defaults to citizen's country if empty) |
| divisionId | URL path | integer | Yes | Division/category ID for the rankings |

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/epic-soldier-event-citizens-rankings/35/1'
```

</details>

<details>
<summary>Expected Response Structure</summary>

```json
{
  "top": [
    {
      "id": 12345,
      "name": "PlayerName",
      "damage": 1000000,
      "percent": "25.50"
    }
  ],
  "myPosition": {
    "id": 67890,
    "countryId": 35,
    "damage": 500000
  },
  "country_filter": [
    { "id": "167", "name": "Albania", "permalink": "Albania" }
  ]
}
```

</details>

#### Notes

- The second path segment (`divisionId`) distinguishes this from the Countries Tournament endpoint which only takes `countryId`
- Cross-origin request using protocol-relative URL
- Cached client-side; subsequent calls use cached data unless `force` parameter is `true`
- Epic Soldier events are seasonal — endpoint may be unavailable outside event periods

---

### Epic Soldier Event — Country Rankings

**Method:** GET
**URL:** `//{hostname}/{culture}/main/epic-soldier-event-countries-rankings`
**Auth Required:** No
**Called by:** `epicSoldier.getWorldData()`

#### Description

Retrieves worldwide country rankings for the Epic Soldier Event.

#### Parameters

None

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/epic-soldier-event-countries-rankings'
```

</details>

#### Notes

- Same response structure and caching behavior as Countries Tournament world rankings
- Seasonal event endpoint

---

## HTML Popup Endpoints

These endpoints return **HTML fragments** loaded into modal dialogs via `generalPopup.renderPopup()`. All use **GET** method and require authentication.

Common headers for all popup endpoints:

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Recommended |

---

### Travel Popup

**URL:** `/en/main/travelPopup`
**Popup Name:** `travelPopup`
**DOM Target:** `#travelPopup`

#### Description

Travel dialog popup. Can be pre-configured with destination parameters.

#### Parameters (via `generalPopup.renderPopup` options)

| Name | Type | Required | Description |
|------|------|----------|-------------|
| holdingId | integer | No | Pre-select holding company destination |
| battleId | integer | No | Pre-select battle destination |
| regionId | integer | No | Pre-select region destination |

<details>
<summary>Example Request (cURL)</summary>

```bash
# Basic travel popup
curl 'https://www.erepublik.com/en/main/travelPopup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'

# With battle destination
curl 'https://www.erepublik.com/en/main/travelPopup?battleId=123456' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

</details>

#### Notes

- Called via `erepublik.functions.launchTravelPopup({ battleId, regionId, holdingId })`
- Parameters are sent as query params via the `params` object in `renderPopup`
- Returns HTML with country/region selectors
- See [world/travel.md](../world/travel.md) for the actual travel POST endpoint

---

### World Map

**URL:** `/en/main/world-map`
**Popup Name:** `map`
**DOM Target:** `#mapsHolder`

#### Description

World map overlay with interactive region display. After loading the HTML, also loads `/js/cmp/map.js` for the MapApp initialization.

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/world-map' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

</details>

#### Notes

- Loaded via `generalPopup.mapPopup()` (separate from `renderPopup`)
- Requires additional JS bundle: `/js/cmp/map.js`
- See [world/map.md](../world/map.md) for the map data API endpoint

---

### MPP Priority Settings

**URL:** `/en/main/country-mpp-priority`
**Popup Name:** `mppPriority`

#### Description

Mutual Protection Pact (MPP) priority configuration popup. Allows setting which allied countries should receive priority in military assistance.

---

### League of Allies Info

**URL:** `/en/main/league-info`
**Popup Name:** `leagueOfAllies`

#### Description

Displays League of Allies information and standings.

---

### Terrain Priorities

**URL:** `/en/main/terrain-priorities-popup`
**Popup Name:** `terrainPriorities`

#### Description

Terrain skill priority configuration. Allows players to set which terrain types they prefer for deployment.

---

### Battle Filter Presets

**URL:** `/en/main/preset-popup`
**Popup Name:** `presetPopup` / `filtersPresetPopup`
**DOM Target:** `#presetPopup`

#### Description

Battle filter preset management popup. Allows saving and loading battlefield filter configurations.

---

### Multiple Aircraft Zones

**URL:** `/en/main/multipleAircraftZonesPopup`
**Popup Name:** `multipleAircraftZonesPopup`

#### Description

Information popup about fighting in multiple aircraft zones simultaneously.

---

### Strength Event

**URL:** `/en/main/strength-popup`
**Popup Name:** `strengthEvent`

#### Description

Strength event information and progress popup.

---

### Energy Refill Popup

**URL:** `/en/main/energyRefill-refillPopup`
**Popup Name:** `recoverEnergy` / `energyRefillPopup`
**DOM Target:** `#recoverEnergyPopup`

#### Description

Energy recovery dialog. Shows available food items and energy bars for consumption.

#### Notes

- See [economy/work.md](../economy/work.md) for the `POST /en/economy/refillEnergy` endpoint

---

### Manage Organizations

**URL:** `/en/main/manage-organizations-popup`
**Popup Name:** `manageOrganizations`

#### Description

Organization account management popup. Allows managing organization accounts associated with the citizen.

---

### Crypto Assets

**URL:** `/en/main/cryptoAssetsPopup`
**Popup Name:** `cryptoAssetsPopup`
**DOM Target:** `#cryptoAssetsPopup`

#### Description

Displays crypto asset holdings and information.

---

### Deploy Report

**URL:** `/en/military/fightDeploy-deployReportPopup`
**Popup Name:** `deployReportPopup`

#### Description

Deployment results report popup. Shows deployment statistics and rewards.

#### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| deploymentId | integer | No | Specific deployment ID to show report for |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/military/fightDeploy-deployReportPopup?deploymentId=98765' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

</details>

#### Notes

- If `deploymentId` is provided, shows that specific deployment's report
- Without `deploymentId`, likely shows the most recent deployment
- See [military/deployment.md](../military/deployment.md) for deployment start/cancel endpoints

---

### Weekly Challenge Rewards

**URL:** `/en/main/weekly-challenge-rewards`
**Popup Name:** `weeklyChallengeRewardsList`

#### Description

Displays weekly challenge reward tiers and collection status.

#### Notes

- See [rewards/weekly-challenges.md](../rewards/weekly-challenges.md) for the data API

---

### Power Spin Rewards

**URL:** `/en/main/powerSpinRewardsPopup`
**Popup Name:** `powerSpinRewardsPopup`

#### Description

Displays Power Spin (Wheel of Fortune) reward history and available prizes.

#### Notes

- See [powerspin/README.md](../powerspin/README.md) for spin endpoints

---

### Deploy Loot

**URL:** `/en/main/deployLootPopup`
**Popup Name:** `deployLootPopup`

#### Description

Displays loot/rewards earned from deployment.

---

### First Message

**URL:** `/en/main/first-message-popup`
**Popup Name:** `firstMessage`

#### Description

Onboarding popup shown for new players' first message interaction. Uses a higher overlay opacity (0.95 vs default 0.3).

---

### Account Revival

**URL:** `/en/main/revival-popup`
**Popup Name:** `revivalPopup`

#### Description

Account revival popup for returning players. Uses higher overlay opacity (0.75).

---

### Revival Event

**URL:** `/en/main/revivalEventPopup`
**Popup Name:** `revivalEventPopup`

#### Description

Seasonal revival event popup with special incentives for returning players.

---

### City Help

**URL:** `/en/main/city-help-popup`
**Popup Name:** `cityHelpPopup`

#### Description

Help popup explaining the city residence system.

---

### Citizenship Banner (Gold Rush)

**URL:** `/en/main/citizenship-banner`
**Popup Name:** `ncGoldRush`

#### Description

New citizenship / gold rush promotional banner.

#### Notes

- Triggered by clicking `#nc_gold_rush_banner`

---

### Payment Success

**URL:** `/en/main/payment-success`
**Popup Name:** `paymentSuccess`

#### Description

Payment confirmation popup shown after successful gold/pack purchase.

---

### Session Unlock Popup

**URL:** `/en/main/sessionUnlockPopup`
**Popup Name:** `sessionUnlockPopup`

#### Description

CAPTCHA verification modal. Fully documented in [captcha/README.md](../captcha/README.md).

---

## Economy Holding Popup Endpoints

These popups manage holding companies and factory assignments. All require authentication and return HTML.

---

### Create Holding

**URL:** `/en/economy/create-holding-popup`
**Popup Names:** `createHolding`, `createHoldingCompanyPopup`

#### Description

Popup form to create a new holding company.

---

### Edit Holding

**URL:** `/en/economy/edit-holding-popup`
**Popup Names:** `editHolding`, `editHoldingCompanyPopup`

#### Description

Popup form to edit an existing holding company.

---

### Assign Factory to Holding

**URL:** `/en/economy/assign-to-holding-popup`
**Popup Names:** `assignToHolding`, `assignFactoryToHoldingPopup`

#### Description

Popup to assign a single factory to a holding company.

---

### Assign Multiple Factories to Holding

**URL:** `/en/economy/assign-multiple-to-holding-popup`
**Popup Name:** `assignMultipleFactoriesToHoldingPopup`

#### Description

Popup to bulk-assign multiple factories to a holding company.

---

### Unassign Factories from Holding

**URL:** `/en/economy/unassign-factories-popup`
**Popup Name:** `unassignMultipleFactoriesPopup`

#### Description

Popup to remove factories from a holding company.

---

## Seasonal/Event Popup Endpoints

These popups appear during seasonal events. All use GET, require auth, and return HTML. They may be unavailable outside their event periods.

| URL | Popup Name | Event |
|-----|------------|-------|
| `/en/main/anniversary-popup` | erepublikAnniversary | eRepublik Anniversary |
| `/en/main/anniversaryQuest` | — | Anniversary quest data |
| `/en/main/anniversaryQuestHelp` | anniversaryQuestHelp | Anniversary quest help |
| `/en/main/halloweenPopup` | halloweenPopup | Halloween (also: halloween2017–2020) |
| `/en/main/halloweenRewards` | halloweenRewards | Halloween rewards |
| `/en/main/easterRewards` | easterRewards | Easter rewards |
| `/en/main/christmas-gifts` | christmasGifts | Christmas gifts |
| `/en/main/aprilsFoolsPopup` | aprilsFools | April Fools |
| `/en/main/valentinesDayPopup` | valentinesDayPopup | Valentine's Day |
| `/en/main/winterOlympicsPopup` | winterOlympicsPopup | Winter Olympics |
| `/en/main/childrensDayPopup` | childrensDayPopup | Children's Day |
| `/en/main/summerChallengeTop5` | summerChallengeTop5 | Summer Challenge |
| `/en/main/cryptoEventPopup` | cryptoEventPopup | Crypto Event |
| `/en/main/day6000Popup` | day6000Popup | Day 6000 Celebration |
| `/en/main/dyeHard-eventShop` | — | Dye Hard event shop |

---

## Shop/Navigation Endpoints

These are **browser navigation redirects** (not AJAX) — the JavaScript sets `document.location.href`. They return full HTML pages, not fragments.

Called via `promoPopup.renderPopup()` or `promoPopup.goToShop()`.

---

### Gold Shop (Special Items)

**URL:** `/en/main/special-items`
**Called by:** `promoPopup.goToShop(showType, showPack)`

#### Parameters

| Name | Location | Type | Required | Description |
|------|----------|------|----------|-------------|
| showType | query | string | No | Filter by item type (e.g., `goldPack`) |
| showPack | query | string | No | Show specific pack |

<details>
<summary>Example URLs</summary>

```
/en/main/special-items
/en/main/special-items?showType=goldPack
/en/main/special-items?showPack=blitzkrieg
```

</details>

---

### Buy Gift for Friend

**URL:** `/en/main/buy-forfriend`
**Called by:** `promoPopup.renderPopup('forFriendPopup', ..., citizenId)`

#### Parameters

| Name | Location | Type | Required | Description |
|------|----------|------|----------|-------------|
| to_citizen_id | query | integer | Yes | Citizen ID of the gift recipient |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/buy-forfriend?to_citizen_id=12345' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

</details>

---

### Custom Pack

**URL:** `/en/main/custom-pack`
**Called by:** `promoPopup.renderPopup(packType, ..., ..., 'custom-pack')`

#### Parameters

| Name | Location | Type | Required | Description |
|------|----------|------|----------|-------------|
| packType | query | string | Yes | Pack type identifier |
| to_citizen_id | query | integer | No | If buying as gift for another citizen |

---

### Starter Pack

**URL:** `/en/main/starter-pack`

#### Description

Starter pack purchase page for new players.

---

### Gifts Pack

**URL:** `/en/main/gifts-pack`

#### Parameters

| Name | Location | Type | Required | Description |
|------|----------|------|----------|-------------|
| giftQuality | query | string | No | Pre-select gift quality tier |

---

### Gold Packs

**URL:** `/en/main/gold-packs`

#### Description

Gold pack purchase page with various gold bundle options.

---

### Gold Pack Payments

**URL:** `/en/main/gold-pack-payments`

#### Description

Gold pack payment processing page. Loads PayPal Digital Goods SDK (`paypalobjects.com/js/external/dg.js`) and the buy script (`/js/buy/buy.js`).

---

### Known Pack Types

From `promoPopup.packWithPayments`:

| Pack Type | Description |
|-----------|-------------|
| `blankPack` | Blank/generic pack |
| `goldAndItemsPack` | Gold + items bundle |
| `starterPack` | Starter pack |
| `stashPack` | Stash pack |
| `assaultPack` | Assault pack |
| `powerPack` | Power pack |
| `economyPack` | Economy pack |
| `monthlyGoldPackSmall` | Monthly gold (small) |
| `monthlyGoldPackBig` | Monthly gold (large) |
| `giftsPack` | Gifts pack |
| `goldPack` | Gold pack |
| `blitzkriegPack` | Blitzkrieg pack |
| `camperPack` | Camper pack |
| `goldPackPayments` | Gold pack payments |
| `goldPackPaymentsSms` | Gold pack via SMS |

---

## Angular Template Endpoints

Some popups load via Angular templates instead of `generalPopup`:

| Popup Name | Angular Controller | Template Path |
|------------|--------------------|---------------|
| `dailyMissionsPopup` | `DailyMissionsController` | `/templates/DailyMissions.tpl.html` |
| `citizenRevivalPopup` | `CitizenRevivalController` | `/templates/CitizenRevival.tpl.html` |
| `citizenRevivalLoginRewardsPopup` | `LoginRewardsRevivalController` | `/templates/LoginRewardsRevival.tpl.html` |

Template path resolution:
```javascript
// ERPK.angular.$templatePath logic:
// 1. Check erepublik.angularTemplates[name] for custom path
// 2. Fallback to '/templates/{name}.tpl.html'
// Controller name -> template name: remove 'Erpk' prefix, remove 'Controller' suffix
```

---

## Session CAPTCHA Endpoints

These are defined in the `sessionCaptcha` object within this bundle. Fully documented in [captcha/README.md](../captcha/README.md).

| Property | URL | Method | Description |
|----------|-----|--------|-------------|
| `sessionCaptchaURL` | `/{culture}/main/sessionCaptcha` | GET | Session verification countdown UI |
| `sessionUnlockURL` | `/{culture}/main/sessionUnlock` | POST | Submit CAPTCHA solution |
| `getChallengeUrl` | `/{culture}/main/sessionGetChallenge` | POST | Get CAPTCHA challenge image |

---

## Popup Overlay Opacity Reference

Different popups use different overlay darkness levels:

| Popup | Opacity | Effect |
|-------|---------|--------|
| `firstMessage` | 0.95 | Nearly opaque — focus on message |
| `halloweenPopup` | 0.80 | Very dark — spooky theme |
| `revivalPopup` | 0.75 | Dark — attention-grabbing |
| `citizenRevivalLoginRewardsPopup` | 0.75 | Dark |
| `strengthEvent` | 0.75 | Dark |
| `leagueOfAllies` | 0.70 | Moderate |
| All others | 0.30 | Light — standard popup |
