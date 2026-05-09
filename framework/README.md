# Framework Bundle Reference

#erepublik #api #framework #javascript #frontend

[< Back to API Documentation](../README.md)

Reference documentation for the main eRepublik framework JavaScript bundle (`erepublik.{ts}.js`, ~411 KB). This bundle handles popups, general UI, page routing, session captcha, shop navigation, tournaments, and events.

> **Source:** `https://www.erepublik.net/js/cmp/erepublik.{ts}.js` where `{ts}` is a build timestamp (e.g., `1775541406`)

**Related:** [Frontend Reference](../military/frontend-reference.md) | [CAPTCHA](../captcha/README.md)

---

## Files in This Module

| File | Description |
|------|-------------|
| [endpoints.md](endpoints.md) | All API endpoints (JSON data, HTML popups, navigation) |
| [client-objects.md](client-objects.md) | Client-side JavaScript objects (`erepublik.citizen`, `erepublik.settings`, `SERVER_DATA`) |

---

## Bundle Overview

The `erepublik.{ts}.js` bundle is loaded on **every page** and provides:

1. **`generalPopup`** — Popup/modal system (loads HTML fragments via AJAX GET)
2. **`promoPopup`** — Shop/promo popup system (gold packs, gifts, custom packs)
3. **`sessionCaptcha`** — Anti-bot CAPTCHA flow (documented in [captcha/](../captcha/README.md))
4. **`countriesTournament`** — Countries Tournament ranking queries
5. **`epicSoldier`** — Epic Soldier Event ranking queries
6. **`erepublik.functions`** — Utility functions (money donation, travel launcher, live clock)
7. **`ClassicCaptcha`** — Google reCAPTCHA / Cloudflare Turnstile wrapper
8. **`erepublik.subtleCrypto`** — Browser crypto helpers (SHA hashing for `env` parameter)

---

## Key Patterns

### CSRF Token

All POST requests require `_token`, sourced from:
```javascript
// Hidden form field
_token: $j('#_token').val()

// JavaScript variable
_token: SERVER_DATA.csrfToken
```

### URL Building

All endpoints are culture-prefixed:
```javascript
var url = '/' + erepublik.settings.culture + '/main/endpoint-name';
// e.g., /en/main/endpoint-name
```

For cross-origin requests (tournament rankings):
```javascript
var url = '//' + erepublik.settings.hostname + '/' + erepublik.settings.culture + '/main/endpoint-name';
```

### Popup Loading

`generalPopup.renderPopup(popupName, options)` maps popup names to URL paths and loads them via AJAX GET:
```javascript
generalPopup.renderPopup('travelPopup', {
    params: { battleId: 123, regionId: 456 }
});
```

The `options` object can contain:
- `params` — Query parameters appended to the URL
- `redraw` — Force re-fetch even if cached (`true`/`false`)
- `method` — HTTP method (default: `'GET'`)
- `contentLabel` — Cache key for the loaded content

---

## Endpoint Summary

See [endpoints.md](endpoints.md) for full details.

### JSON Data Endpoints
| Endpoint | Method | Auth | Description |
|----------|--------|------|-------------|
| `/en/main/geoLocation/countryCode` | GET | No | IP geolocation country detection |
| `/en/main/money-donation/{action}/{donationId}` | GET | Yes | Accept/reject money donation |
| `/en/main/countries-tournament-national-rankings/{countryId}` | GET | No | Tournament national rankings |
| `/en/main/countries-tournament-world-rankings` | GET | No | Tournament world rankings |
| `/en/main/countries-tournament-world-rankings/finalists` | GET | No | Tournament finalist rankings |
| `/en/main/epic-soldier-event-citizens-rankings/{countryId}/{divisionId}` | GET | No | Epic Soldier citizen rankings |
| `/en/main/epic-soldier-event-countries-rankings` | GET | No | Epic Soldier country rankings |

### HTML Popup Endpoints (GET, Auth Required)
| Endpoint | Popup Name | Category |
|----------|------------|----------|
| `/en/main/travelPopup` | travelPopup | World |
| `/en/main/world-map` | map | World |
| `/en/main/country-mpp-priority` | mppPriority | Country |
| `/en/main/league-info` | leagueOfAllies | Military |
| `/en/main/terrain-priorities-popup` | terrainPriorities | Military |
| `/en/main/preset-popup` | presetPopup | Military |
| `/en/main/multipleAircraftZonesPopup` | multipleAircraftZones | Military |
| `/en/main/strength-popup` | strengthEvent | Military |
| `/en/main/energyRefill-refillPopup` | recoverEnergy | Economy |
| `/en/main/manage-organizations-popup` | manageOrganizations | Economy |
| `/en/economy/create-holding-popup` | createHolding | Economy |
| `/en/economy/edit-holding-popup` | editHolding | Economy |
| `/en/economy/assign-to-holding-popup` | assignToHolding | Economy |
| `/en/economy/assign-multiple-to-holding-popup` | assignMultiple | Economy |
| `/en/economy/unassign-factories-popup` | unassignMultiple | Economy |
| `/en/military/fightDeploy-deployReportPopup` | deployReportPopup | Military |
| `/en/main/weekly-challenge-rewards` | weeklyChallengeRewardsList | Rewards |
| `/en/main/powerSpinRewardsPopup` | powerSpinRewardsPopup | Rewards |
| `/en/main/deployLootPopup` | deployLootPopup | Rewards |
| `/en/main/cryptoAssetsPopup` | cryptoAssetsPopup | Economy |
| `/en/main/first-message-popup` | firstMessage | Onboarding |
| `/en/main/revival-popup` | revivalPopup | Onboarding |
| `/en/main/city-help-popup` | cityHelpPopup | Help |
| `/en/main/citizenship-banner` | ncGoldRush | Promo |
| `/en/main/payment-success` | paymentSuccess | Shop |
| `/en/main/sessionUnlockPopup` | sessionUnlockPopup | CAPTCHA |

### Seasonal/Event Popup Endpoints (GET, Auth Required)
| Endpoint | Popup Name |
|----------|------------|
| `/en/main/anniversary-popup` | erepublikAnniversary |
| `/en/main/anniversaryQuest` | — |
| `/en/main/anniversaryQuestHelp` | anniversaryQuestHelp |
| `/en/main/halloweenPopup` | halloweenPopup |
| `/en/main/halloweenRewards` | halloweenRewards |
| `/en/main/easterRewards` | easterRewards |
| `/en/main/christmas-gifts` | christmasGifts |
| `/en/main/aprilsFoolsPopup` | aprilsFools |
| `/en/main/valentinesDayPopup` | valentinesDayPopup |
| `/en/main/winterOlympicsPopup` | winterOlympicsPopup |
| `/en/main/childrensDayPopup` | childrensDayPopup |
| `/en/main/summerChallengeTop5` | summerChallengeTop5 |
| `/en/main/revivalEventPopup` | revivalEventPopup |
| `/en/main/cryptoEventPopup` | cryptoEventPopup |
| `/en/main/day6000Popup` | day6000Popup |
| `/en/main/dyeHard-eventShop` | — |

### Shop/Navigation Endpoints (Browser Redirect, Not AJAX)
| Endpoint | Description |
|----------|-------------|
| `/en/main/special-items?showType={type}` | Gold shop with optional filter |
| `/en/main/buy-forfriend?to_citizen_id={citizenId}` | Buy gift for friend |
| `/en/main/custom-pack?packType={packType}` | Custom pack page |
| `/en/main/starter-pack` | Starter pack page |
| `/en/main/gifts-pack?giftQuality={quality}` | Gifts pack page |
| `/en/main/gold-packs` | Gold packs page |
| `/en/main/gold-pack-payments` | Gold pack payments page |
