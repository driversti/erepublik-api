# Framework Bundle — Client-Side Objects

#erepublik #api #framework #javascript #client-data

[< Back to Framework Reference](README.md)

Client-side JavaScript objects available globally on eRepublik pages. These are populated by the server and embedded in the page HTML, making them useful for extracting game state without additional API calls.

---

## `erepublik.citizen`

Available on all authenticated pages. Contains the current logged-in citizen's basic state.

| Property | Type | Description |
|----------|------|-------------|
| `citizenId` | integer | Current citizen's unique ID |
| `ctCountryId` | integer | Citizenship country ID |
| `countryLocationName` | string | Name of the country where the citizen is currently located |
| `currency` | string | Local currency name for the citizen's country |
| `currencyAmount` | number | Current local currency balance |
| `gold` | number | Current gold balance |
| `energy` | number | Current energy (health) points |
| `prestige` | number | Prestige points (eRepublik level/status indicator) |
| `loyaltyPoints` | number | Loyalty points balance |
| `currentExperiencePoints` | number | Current XP total |
| `userLevel` | number | Player level |
| `isActive` | boolean | Whether the account is active |
| `isEmpire` | boolean | Whether the citizen's country is an empire |
| `isDictatorship` | boolean | Whether the citizen's country is a dictatorship |
| `terrainSkills` | object | Terrain skill levels per terrain type |
| `residence.hasResidence` | boolean | Whether the citizen has a city residence |
| `residence.regionId` | integer | Region ID of the citizen's residence |

### Usage in Scripts

```javascript
// These are available globally after page load
var myId = erepublik.citizen.citizenId;
var myGold = erepublik.citizen.gold;
var myCountry = erepublik.citizen.ctCountryId;

// Check if citizen needs to travel for a battle
if (erepublik.citizen.residence.hasResidence) {
    var homeRegion = erepublik.citizen.residence.regionId;
}
```

### Notes

- These values are embedded in the page HTML at render time — they don't update live
- For real-time energy, see `SERVER_DATA.health` on the battlefield page
- The `isEmpire` and `isDictatorship` properties reflect the **current** government type
- `terrainSkills` maps terrain IDs to skill level values

---

## `erepublik.settings`

Global configuration object available on all pages.

| Property | Type | Description |
|----------|------|-------------|
| `hostname` | string | Main hostname (e.g., `www.erepublik.com`) |
| `culture` | string | Language/locale code (e.g., `en`, `es`, `pt`) |
| `cookielessHost` | string | CDN hostname for static assets (e.g., `www.erepublik.net`) |
| `domainname` | string | Base domain name |
| `notifications` | integer | Unread notification count (feeds sidebar badge via Tinycon) |
| `achievementPopup` | boolean/object | Whether an achievement popup should be shown on page load |

### Usage in Scripts

```javascript
// Build API URLs dynamically
var apiUrl = '/' + erepublik.settings.culture + '/main/endpoint-name';

// Cross-origin URL (for tournament rankings)
var crossOriginUrl = '//' + erepublik.settings.hostname + '/' + erepublik.settings.culture + '/main/endpoint';

// Static asset URL
var imageUrl = '//' + erepublik.settings.cookielessHost + '/images/flags_png/M/Poland.png';
```

### Notes

- `culture` determines the URL prefix for all API calls (typically `en`)
- `cookielessHost` serves static assets without sending cookies (bandwidth optimization)
- When `achievementPopup` is truthy, `generalPopup.renderPopup()` is blocked to prevent overlap
- `notifications` count is used to set the browser tab badge via the Tinycon library

---

## `SERVER_DATA`

Page-specific server data embedded in `<script>` tags. Contents vary by page — below are the properties used by the framework bundle.

### Common Properties (All Pages)

| Property | Type | Description |
|----------|------|-------------|
| `csrfToken` | string | CSRF token required for all POST requests (`_token` parameter) |

### Session Validation Properties

Present when CAPTCHA verification is active:

| Property | Type | Description |
|----------|------|-------------|
| `sessionValidation.remainingTime` | integer | Seconds until forced verification |
| `sessionValidation.engine` | string | CAPTCHA type: `clickCaptcha` or `sliderCaptcha` |
| `sessionValidation.captchaId` | integer | Unique CAPTCHA challenge ID |
| `sessionValidation.captchaWidgetId` | integer/null | Widget ID (null on first call) |
| `sessionValidation.firstCall` | boolean | Whether this is the first CAPTCHA load |
| `sessionValidation.scriptLoaded` | boolean | Whether CAPTCHA scripts are loaded |

### Battlefield Properties

See [military/frontend-reference.md](../military/frontend-reference.md) for the full battlefield `SERVER_DATA` reference.

### Usage in Scripts

```javascript
// Get CSRF token for POST requests
var token = SERVER_DATA.csrfToken;

// Check if CAPTCHA is active
if (SERVER_DATA.sessionValidation) {
    var remaining = SERVER_DATA.sessionValidation.remainingTime;
    var captchaType = SERVER_DATA.sessionValidation.engine;
}
```

---

## `erepublik.subtleCrypto`

Wrapper around the Web Crypto API for hashing. Used internally to build the `env` parameter for CAPTCHA verification.

| Method | Parameters | Returns | Description |
|--------|-----------|---------|-------------|
| `textEncoder(string)` | string | Uint8Array | Encode string to bytes (TextEncoder polyfill) |
| `hexEncoder(buffer)` | ArrayBuffer | string | Convert buffer to hex string |
| `digest(algorithm, data)` | string, string | Promise\<string\> | Hash data with given algorithm |

### Supported Algorithms

- `SHA-1`
- `SHA-256`
- `SHA-384`
- `SHA-512`

### Usage

```javascript
// Hash a string with SHA-256
erepublik.subtleCrypto.digest('SHA-256', 'some data').then(function(hash) {
    console.log(hash); // hex string
});
```

### Notes

- Only available when browser supports `crypto.subtle` (all modern browsers)
- Used to generate the `env` parameter containing browser fingerprint data for CAPTCHA
- Falls back gracefully — `erepublik.subtleCrypto` is `null` if crypto is unavailable

---

## `erepublik.angularTemplates`

Maps Angular controller names to custom template paths. Used by `ERPK.angular.$templatePath()`.

```javascript
// Template path resolution logic:
// 1. Strip 'Erpk' prefix and 'Controller' suffix from controller name
// 2. Check erepublik.angularTemplates[name]
// 3. If not found, use '/templates/{name}.tpl.html'

ERPK.angular.$templatePath('DailyMissionsController');
// -> erepublik.angularTemplates['DailyMissions'] || '/templates/DailyMissions.tpl.html'
```

---

## `generalPopup` Object

Popup management system.

| Method | Parameters | Description |
|--------|-----------|-------------|
| `init()` | — | Binds click handler on `#nc_gold_rush_banner` |
| `getScripts(urls)` | string[] | Loads JS files dynamically, returns Promise |
| `compileAngularElement(selector)` | string | Compile Angular template at selector |
| `renderPopup(name, options)` | string, object | Load and display a popup by name |
| `mapPopup()` | — | Open world map overlay (separate from `renderPopup`) |
| `onLoad()` | — | Callback after popup content loads |
| `onClose()` | — | Callback when popup closes |

### `renderPopup` Options Object

```javascript
generalPopup.renderPopup('popupName', {
    params: {},           // Query parameters to append to URL
    redraw: false,        // Force re-fetch even if cached
    method: 'GET',        // HTTP method (default: GET)
    contentLabel: 'name'  // Cache key (default: popup name)
});
```

---

## `promoPopup` Object

Shop/promotion popup system.

| Method | Parameters | Description |
|--------|-----------|-------------|
| `init()` | — | Binds click handlers for gift/pack buttons |
| `renderPopup(pack, returnTo, citizenId, type, lightboxOpts)` | various | Load and display shop popup |
| `onClose()` | — | Cleanup callback |
| `beforeSend()` | — | Pre-request hook (loads payment scripts) |
| `goToShop(showType, showPack)` | string, string | Navigate to `/main/special-items` |

### `renderPopup` Pack Resolution

```javascript
// Direct pack types:
promoPopup.renderPopup('starterPack');    // -> /main/starter-pack
promoPopup.renderPopup('giftsPack');      // -> /main/gifts-pack
promoPopup.renderPopup('goldPack');       // -> /main/gold-packs
promoPopup.renderPopup('goldPackPayments'); // -> /main/gold-pack-payments

// Custom pack (with type parameter):
promoPopup.renderPopup('anyType', false, 0, 'custom-pack');
// -> /main/custom-pack?packType=anyType

// Buy for friend:
promoPopup.renderPopup('forFriendPopup', false, 12345);
// -> /main/buy-forfriend?to_citizen_id=12345
```

---

## `sessionCaptcha` Object

CAPTCHA management. See [captcha/README.md](../captcha/README.md) for full endpoint documentation.

| Property/Method | Type | Description |
|----------------|------|-------------|
| `sessionUnlockURL` | string | `/{culture}/main/sessionUnlock` |
| `sessionCaptchaURL` | string | `/{culture}/main/sessionCaptcha` |
| `getChallengeUrl` | string | `/{culture}/main/sessionGetChallenge` |
| `captchaDisabled(message)` | function | Show error when 3rd-party scripts interfere |
| `clickCaptcha.showValidationCaptcha()` | function | Initialize click-based CAPTCHA widget |
| `showMessage(message, ...)` | function | Display CAPTCHA status messages |
| `handleUnlockSuccess(response)` | function | Handle successful verification |

---

## `erepublik.functions` (Selected Methods)

Utility functions used across the site.

| Method | Parameters | Description |
|--------|-----------|-------------|
| `acceptRejectDonation(action, id)` | string, integer | Accept/reject money donation |
| `launchTravelPopup(options)` | object | Open travel popup with optional `{holdingId, battleId, regionId}` |
| `openMapPopup()` | — | Open world map |
| `liveClock(elementId, hours, minutes)` | string, int, int | Update live clock display |
| `formatNumber(number)` | number | Format number with locale separators |
| `displayHumanMsgError(message)` | string | Show user-facing error notification |
| `shortcuts()` | — | Initialize keyboard shortcuts |
| `fbTrackEvent(...)` | various | Facebook tracking pixel event |
| `confirmationAlert(...)` | various | Show confirmation dialog |

---

## Static Asset URL Patterns

| Pattern | Description |
|---------|-------------|
| `/images/flags_png/{size}/{countryPermalink}.png` | Country flag PNG (`size`: S, M, L) |
| `/images/flags/{country}/{permalink}.gif` | Country flag GIF (legacy) |
| `/images/modules/misc/session_captcha_script_detected.jpg` | CAPTCHA script-detection image |
| `/js/cmp/map.js` | World map JavaScript bundle |
| `/js/buy/buy.js` | Shop/purchase JavaScript |
| `/css/sessionCaptcha/sessionCaptcha.{ts}.css` | CAPTCHA styles |
| `/css/sessionCaptcha/sliderCaptcha.{ts}.css` | Slider CAPTCHA styles |
| `/templates/{ControllerName}.tpl.html` | Angular template files |
