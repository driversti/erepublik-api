# eRepublik API - CAPTCHA & Verification

#erepublik #api #captcha #verification #security

> 🤖 **Maintained by Claude** - This API documentation is managed by Claude Code. Feel free to ask Claude to add, update, or reorganize endpoints as you discover them.

## Overview

This module covers session verification and CAPTCHA endpoints. eRepublik uses various verification mechanisms to detect automation and ensure fair gameplay. These endpoints handle verification challenges, countdowns, and validation.

---

## Session Verification Challenge

**Method:** GET
**URL:** `/en/main/sessionCaptcha`
**Auth Required:** Yes

### Description

Returns the HTML/JavaScript content for the session verification challenge UI. This endpoint is called when the game detects suspicious activity or automated behavior and requires the user to complete a CAPTCHA challenge to continue playing.

The response includes:
- Countdown timer UI
- CAPTCHA verification widget (clickCaptcha or sliderCaptcha)
- Server data with remaining time and CAPTCHA configuration
- CSS resources for the verification interface

### Parameters

None

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Referer | Current page URL (e.g., battlefield URL) | No |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/sessionCaptcha' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Example Response

```html
<div id="verificationContainer" about="Pride and Prejudice and Zombies and Scripts" class="sessionVerify critical" data-remaining-time="107">
    <div id="verificationBox" class="animated">
        <div id="verificationIcon">
            <i class="fa fa-clock-o"></i>
        </div>
        <div class="verificationCountdown">
            <strong>01:47</strong>
        </div>
        <progress id="verificationProgress" value="107" max="300"></progress>
    </div>

    <div id="verificationPopup" class="">
        <h2>Account verification</h2>
        Please verify your account to ensure a fair gaming environment for all Citizens.
        <div id="verificationTime" class="yellowBox">
            <div class="verificationCountdown">
                Countdown: <strong>01:47</strong>
            </div>
        </div>
        <a id="startSessionVerify" href="javascript:" class="std_global_btn smallSize noFloat blueColor"><span>Verify</span></a>
        <div id="customerServiceLink">
            <a id="verifyLater" href="javascript:">Verify later</a>
            <a href="/en/main/tickets">Customer Service</a>
        </div>
    </div>
</div>
<script about="Hello there, General Kenobi.">
$j.extend(SERVER_DATA, {
  sessionValidation: {
    "remainingTime": 107,
    "engine": "clickCaptcha",
    "captchaId": 27766920,
    "captchaWidgetId": null,
    "firstCall": true,
    "scriptLoaded": false
  }
});
</script>
<link rel="stylesheet preload" as="style" type="text/css" href="//www.erepublik.net/css/sessionCaptcha/sessionCaptcha.1698060170.css"/>
<link rel="stylesheet preload" as="style" type="text/css" href="//www.erepublik.net/css/sessionCaptcha/sliderCaptcha.1698060170.css"/>
```

### Response Data Structure

The embedded JavaScript object `SERVER_DATA.sessionValidation` contains:

| Field | Type | Description |
|-------|------|-------------|
| remainingTime | integer | Seconds remaining before forced verification |
| engine | string | Type of CAPTCHA ("clickCaptcha" or "sliderCaptcha") |
| captchaId | integer | Unique ID for this CAPTCHA challenge |
| captchaWidgetId | integer/null | Widget identifier (null on first call) |
| firstCall | boolean | Whether this is the first time the endpoint is called |
| scriptLoaded | boolean | Whether CAPTCHA scripts have been loaded |

### Notes

- This endpoint returns HTML/JavaScript content, not JSON
- The CAPTCHA is triggered by the anti-bot system when suspicious activity is detected
- The countdown timer gives the user time before they must complete verification
- Two verification engines are supported: `clickCaptcha` and `sliderCaptcha`
- The `data-remaining-time` attribute shows seconds until forced verification
- Maximum countdown appears to be 300 seconds (5 minutes)
- Users can choose to "Verify later" but may face restrictions until verified
- The humorous HTML comments ("Pride and Prejudice and Zombies and Scripts", "Hello there, General Kenobi") are Easter eggs from the developers 🎮

---

## Session Unlock Popup (CAPTCHA Modal)

**Method:** GET
**URL:** `/en/main/sessionUnlockPopup`
**Auth Required:** Yes

### Description

Returns the HTML modal UI for the session unlock CAPTCHA challenge. This is the actual interactive popup that displays when a user clicks the "Verify" button from the session verification countdown. The modal contains the CAPTCHA puzzle interface (image-based click challenges or slider puzzles).

The response includes:
- Modal popup HTML structure with blue border styling
- CAPTCHA image container and interaction elements
- Help text explaining how to solve the CAPTCHA
- Verify/Continue button
- Refresh and help icons
- Close button to dismiss the modal

### Parameters

None

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Referer | Current page URL (e.g., battlefield URL) | No |

### Example Request

```bash
curl 'https://www.erepublik.com/en/main/sessionUnlockPopup' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Example Response

```html
<div id="sessionUnlockModal" class="blueBorderPopup captchaEngine_sliderCaptcha captchaEngine_clickCaptcha loading" style="display: block;">
    <a href="javascript:" class="close closeButton" title="Close" id="cancelCaptcha">✖</a>
    <h2>Account verification</h2>
    <p class="text">Please verify your account to ensure a fair gaming environment for all Citizens.</p>
    <div id="sessionUnlockCaptchaDiv">
        <div id="imageWrapper" class="imageWrapper" data-i18n="Click and select in this order (left to right)">
            <img id="captchaImage" class="captchaImage hide" alt="Captcha" />
        </div>
        <i id="refreshIcon" class="refreshIcon fa fa-repeat"></i>
        <i id="helpIcon" class="helpIcon fa fa-question-circle"></i>
        <div id="clickCaptchaHelp" class="hide">
            <p class="up">
                <q class="up">&#11181;</q>
                Find these symbols in the above image.<br>
                Click on the symbols <b>in this order</b> (left to right)
            </p>
            <p class="down">
                <q class="down">&#11183;</q>
                Click <b>Verify</b>
            </p>
        </div>
    </div>
    <div id="sessionInfoMessages"></div>

    <div id="sessionUnlockButtons" class="blueBorderBottom">
        <a id="sessionUnlockSubmit" href="javascript:" title="Verify" class="std_global_btn mediumSize disabled right">
            <span>Continue</span>
        </a>
    </div>
    <br clear="all">
    <script>showSessionValidationCaptcha = sessionCaptcha.clickCaptcha.showValidationCaptcha;</script>
    <div id="sessionUnlockMessages"></div>
</div>
```

### UI Elements

| Element ID | Purpose | Description |
|------------|---------|-------------|
| sessionUnlockModal | Container | Main modal popup with CAPTCHA UI |
| cancelCaptcha | Close button | Dismisses the verification modal |
| sessionUnlockCaptchaDiv | CAPTCHA container | Holds the interactive CAPTCHA challenge |
| captchaImage | Image element | Displays the CAPTCHA image to solve |
| refreshIcon | Refresh button | Requests a new CAPTCHA image |
| helpIcon | Help button | Shows/hides CAPTCHA instructions |
| clickCaptchaHelp | Help text | Instructions for solving the challenge |
| sessionUnlockSubmit | Submit button | Submits the CAPTCHA solution (initially disabled) |
| sessionInfoMessages | Message area | Displays validation messages/errors |

### Notes

- This endpoint returns HTML content, not JSON
- The modal is shown when user initiates verification (clicks "Verify" button)
- Two CAPTCHA types are supported (shown via CSS classes): `captchaEngine_sliderCaptcha` and `captchaEngine_clickCaptcha`
- **Click CAPTCHA**: User must identify and click symbols in the correct order (left to right)
- **Slider CAPTCHA**: User must slide a puzzle piece to complete an image
- The "Continue" button is initially disabled and becomes enabled once the CAPTCHA is solved
- Refresh icon allows requesting a new CAPTCHA if the current one is too difficult
- Help icon toggles display of instructions
- The modal includes both close (✖) and cancel options
- JavaScript callback `showSessionValidationCaptcha` is set to initialize the CAPTCHA widget

---

## Get Session Challenge

**Method:** POST
**URL:** `/en/main/sessionGetChallenge`
**Auth Required:** Yes

### Description

Retrieves the actual CAPTCHA challenge data including the challenge image, IDs, and configuration. This endpoint is called after the user initiates verification (typically after clicking "Verify" in the session verification popup) to load the specific CAPTCHA puzzle that needs to be solved.

The response includes:
- Base64-encoded WebP image of the CAPTCHA puzzle
- Challenge ID and image ID for validation
- JavaScript onLoad callback with cookie setup
- Minimum required click count (for click-based CAPTCHAs)
- srcset ID for responsive image loading

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the session |
| captchaId | integer | Yes | The CAPTCHA ID from the session validation data |
| env | string | Yes | Base64-encoded environment data (browser fingerprint, tracking info) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Content-Type | `application/x-www-form-urlencoded; charset=UTF-8` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Origin | `https://www.erepublik.com` | Yes |
| Referer | Current page URL (e.g., battlefield URL) | No |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/sessionGetChallenge' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Origin: https://www.erepublik.com' \
  --data-urlencode '_token=YOUR_CSRF_TOKEN' \
  --data-urlencode 'captchaId=27766920' \
  --data-urlencode 'env=BASE64_ENCODED_ENV_DATA'
```

### Example Response

```json
{
  "src": "data:image/webp;base64,UklGRgAnAABXRUJQVlA4IPQmAACQgACdASqQAeYAPm0ylUekIqIpJbE7qSANiWNuvPkbYsQB...",
  "imageId": "dccef7146bc403bad1157d0ef98ee1a9ec7fffbd553237bbad044fbd1e98fb00ec9a60d41705f148caf6ac7e10ea38ca93557a607a6c35c47bcd91bb9a6ff837",
  "challengeId": "2f5cc944c96c7a694848b5e467ef0daf623404e9239524646142396878c79dfe5a87bdb8fec8fd864c9d9b1838e09ab6be067a1ebdef30808b3c64bc33607d7e",
  "onLoad": "$j.cookie(\"f6b735ea\",\"51d6f70fa5182ec7303466ab8c369caee7a11ba204e54f1448bf1d294ed040fd\",{expires:1,path:\"/en/main/sessionUnlock\"})",
  "minCnt": 3,
  "srcsetId": "4942cbd1727fae29068cd4135f8901d2f139e875ae2cefd4e96c10dcef4676b8ccd86ba0f46614d2b79657a7b3e8962644938387c9bee3a727a365c5627b7420"
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| src | string | Base64-encoded data URI of the CAPTCHA image (WebP format) |
| imageId | string | SHA-256 hash identifier for this specific CAPTCHA image |
| challengeId | string | SHA-256 hash identifier for this challenge session |
| onLoad | string | JavaScript code to execute on load (sets tracking cookie) |
| minCnt | integer | Minimum number of clicks required (for click CAPTCHAs) |
| srcsetId | string | SHA-256 hash for responsive image srcset |

### Notes

- The CAPTCHA image is returned as a WebP format data URI (base64-encoded)
- The image shows symbols/objects that the user must identify and click in order (left to right)
- The `minCnt` field indicates how many items must be clicked (typically 3)
- The `onLoad` JavaScript sets a temporary cookie used for tracking/validation
- The cookie path `/en/main/sessionUnlock` suggests it's used during the unlock process
- All hash IDs are SHA-256 hashes used for validation and tracking
- The `env` parameter contains base64-encoded browser fingerprinting data to detect automation
- Request returns HTTP 200 even if successful

### Related Flow

1. User receives verification countdown → `sessionCaptcha` endpoint
2. User clicks "Verify" → `sessionUnlockPopup` endpoint displays modal
3. Modal loads challenge data → **`sessionGetChallenge`** endpoint (this one)
4. User solves CAPTCHA → Submit solution endpoint (to be documented)
5. Verification complete → Session unlocked

---

## Submit Session Unlock (CAPTCHA Solution)

**Method:** POST
**URL:** `/en/main/sessionUnlock`
**Auth Required:** Yes

### Description

Submits the user's CAPTCHA solution to unlock the session. After completing the click-based CAPTCHA challenge (clicking symbols in the correct order), this endpoint validates the solution and either unlocks the session or returns an error.

This is the final step in the session verification flow. The user's click coordinates are sent as a JSON array, along with various challenge identifiers and environment data for anti-bot validation.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token from the session |
| captchaId | integer | Yes | The CAPTCHA ID from the session validation data |
| imageId | string | Yes | SHA-256 hash identifier for the CAPTCHA image (from `sessionGetChallenge`) |
| challengeId | string | Yes | SHA-256 hash identifier for the challenge session (from `sessionGetChallenge`) |
| clickMatrix | string | Yes | URL-encoded JSON array of click coordinates, e.g., `[{"x":377,"y":50},{"x":237,"y":104}]` |
| isMobile | boolean | Yes | Whether the user is on a mobile device (`true`/`false`) |
| env | string | Yes | Base64-encoded environment data (browser fingerprint, tracking info) |
| src | string | Yes | The CAPTCHA image data URI (same as received from `sessionGetChallenge`) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| Content-Type | `application/x-www-form-urlencoded; charset=UTF-8` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Origin | `https://www.erepublik.com` | Yes |
| Referer | Current page URL (e.g., battlefield URL) | No |

### Example Request

```bash
curl -X POST 'https://www.erepublik.com/en/main/sessionUnlock' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Origin: https://www.erepublik.com' \
  --data-urlencode '_token=YOUR_CSRF_TOKEN' \
  --data-urlencode 'captchaId=27766920' \
  --data-urlencode 'imageId=298c8d002cea8c42...' \
  --data-urlencode 'challengeId=d931cbe1d9e67248...' \
  --data-urlencode 'clickMatrix=[{"x":377,"y":50},{"x":237,"y":104},{"x":372,"y":91}]' \
  --data-urlencode 'isMobile=false' \
  --data-urlencode 'env=BASE64_ENCODED_ENV_DATA' \
  --data-urlencode 'src=data:image/png;base64,...'
```

### Example Response (Incorrect Solution)

```json
{
  "error": true,
  "message": "The challenge solution was incorrect.",
  "verified": false,
  "mustReload": true
}
```

### Example Response (Successful - Expected)

```json
{
  "error": false,
  "message": "Session verified successfully",
  "verified": true,
  "mustReload": false
}
```

### Response Fields

| Field | Type | Description |
|-------|------|-------------|
| error | boolean | `true` if verification failed, `false` if successful |
| message | string | Human-readable result message |
| verified | boolean | `true` if session is now verified/unlocked |
| mustReload | boolean | `true` if page must be reloaded (typically on failure) |

### Click Matrix Format

The `clickMatrix` parameter is a JSON array of coordinate objects representing where the user clicked on the CAPTCHA image:

```json
[
  {"x": 377, "y": 50},
  {"x": 237, "y": 104},
  {"x": 372, "y": 91}
]
```

- Coordinates are relative to the CAPTCHA image (not the page)
- Order matters - clicks must match the expected symbol order (left to right)
- The number of clicks should match the `minCnt` value from `sessionGetChallenge`

### Notes

- This is the final step in the session verification flow
- The `env` parameter contains browser fingerprint data used to detect automation
- The `src` parameter includes the full image data URI, which may be quite large
- On failure, the response indicates `mustReload: true`, suggesting a new challenge is needed
- Multiple incorrect attempts may result in additional restrictions or longer lockouts
- All hash IDs (`imageId`, `challengeId`) must match those received from `sessionGetChallenge`

### Complete Verification Flow

1. **Detection** → Anti-bot system triggers verification
2. **Countdown** → `GET /en/main/sessionCaptcha` - Shows countdown UI
3. **Popup** → `GET /en/main/sessionUnlockPopup` - Displays CAPTCHA modal
4. **Challenge** → `POST /en/main/sessionGetChallenge` - Loads CAPTCHA image and IDs
5. **Solution** → **`POST /en/main/sessionUnlock`** (this endpoint) - Submits click coordinates
6. **Result** → Session unlocked or retry required

---

## Related Endpoints

Additional CAPTCHA-related endpoints to be documented:
- Endpoint to verify CAPTCHA completion status

---

## Related Links

- [[API_AUTH]] - Authentication and session management
- [API Table of Contents](API_TOC.md)
