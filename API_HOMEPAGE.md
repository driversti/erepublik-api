# eRepublik API - Homepage (Dashboard)

#erepublik #api #homepage #dashboard

[< Back to Table of Contents](API_TOC.md)

---

## Overview

The homepage (`/en`) is the main authenticated dashboard that loads when a logged-in citizen visits eRepublik. It returns a full HTML page (~131 KB) with multiple embedded JavaScript data objects containing citizen state, social feeds, battle listings, settings, and more.

Unlike JSON API endpoints, the homepage requires HTML parsing to extract the embedded data. It serves as a rich "bootstrap" page — the client-side AngularJS app reads these objects to render the dashboard without additional API calls.

---

## Endpoints

### Homepage (Dashboard)

**Method:** GET
**URL:** `/en`
**Auth Required:** Yes (redirects to login if unauthenticated)

#### Description

Returns the main citizen dashboard page. Contains social feeds (country, friends, military unit, party, community), daily order progress, battle listings, citizen state, energy data, event popups, and navigation shortcuts. All data is embedded in `<script>` blocks as JavaScript objects.

#### Headers

| Header | Value | Required |
|--------|-------|----------|
| `Cookie` | `erpk=YOUR_SESSION_TOKEN` | Yes |
| `Accept` | `text/html` | Recommended |

#### Example Request

```bash
curl 'https://www.erepublik.com/en' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'Accept: text/html,application/xhtml+xml' \
  -H 'User-Agent: Mozilla/5.0 ...'
```

#### Response

Returns an HTML page (~131 KB, ~2200 lines). The key data is embedded in `<script>` blocks:

| Script Block | Variable | Description |
|--------------|----------|-------------|
| Script 0 (~11.7 KB) | `erepublik` | Main global object: citizen, settings, promos, i18n, shortcuts, angularTemplates |
| Script 0 | `csrfToken` | CSRF token for POST requests |
| Script 0 | `Environment` | Session environment flags |
| Script 20 (~1 KB) | `ErpkPvp` | PvP chat/XMPP settings + `dictatorships` and `empires` maps |
| Script 21 (~740 B) | `energyData` | Energy recovery status |
| Script 30 | `SERVER_DATA` | CSRF token refresh |
| Script 34 (~1.5 KB) | `ARTICLES` | News article tabs and categories |
| Script 35 (~5.6 KB) | `SERVER_DATA.wallData` | Social feed configuration and citizen info |

---

### Embedded Data Objects

#### `erepublik` (main global object)

The primary JavaScript global with 6 top-level keys:

```javascript
var erepublik = {
  settings: { /* 11 keys */ },
  citizen: { /* 42 keys */ },
  i18n: { /* 10 keys */ },
  promos: { /* 7 keys */ },
  shortcuts: { /* 2 keys */ },
  angularTemplates: { /* 6 keys */ }
};
```

---

#### `erepublik.settings`

Platform configuration and real-time connection settings:

```json
{
  "domainname": "erepublik.com",
  "hostname": "www.erepublik.com",
  "economyhostname": "economy.erepublik.com",
  "cookielessHost": "www.erepublik.net",
  "isLive": true,
  "culture": "en",
  "eDay": 6659,
  "isOnHomepage": true,
  "requestUri": "L2Vu",
  "notifications": 1,
  "pomelo": {
    "gate": "gate.erepublik.com",
    "port": 3050,
    "authToken": "9c1471f8235385cc96cc4d525b91e70d"
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `domainname` | string | Base domain (`erepublik.com`) |
| `hostname` | string | Main host (`www.erepublik.com`) |
| `economyhostname` | string | Economy subdomain (`economy.erepublik.com`) |
| `cookielessHost` | string | Static assets host (`www.erepublik.net`) |
| `isLive` | boolean | Whether the server is in live mode |
| `culture` | string | Language code (e.g., `"en"`) |
| `eDay` | number | Current eRepublik day number |
| `isOnHomepage` | boolean | `true` on the homepage, `false` on other pages |
| `requestUri` | string | Base64-encoded request URI (`"L2Vu"` = `/en`) |
| `notifications` | number | Unread notifications count |
| `pomelo` | object | WebSocket connection config for real-time updates |
| `pomelo.gate` | string | WebSocket gateway host |
| `pomelo.port` | number | WebSocket port (`3050`) |
| `pomelo.authToken` | string | Auth token for WebSocket (= `erpk` session token) |

---

#### `erepublik.citizen`

Full citizen state (42 keys). This is the same object available on the battlefield page — see [API_MILITARY.md](API_MILITARY.md) (`erepublik.citizen` section) for the full 43-key reference with field descriptions.

The homepage version has the same structure. Example:

```json
{
  "citizenId": 4690052,
  "isOrganization": 0,
  "isAdult": true,
  "isNewCitizen": false,
  "country": 72,
  "citizenshipCountryId": 72,
  "name": "driver sti",
  "avatar": "https://cdnt.erepublik.net/.../avatar.png?...",
  "currency": "LTL",
  "citizenshipCurrencyName": "LTL",
  "isEmpire": false,
  "isDictatorship": 0,
  "countryLocationId": 72,
  "regionLocationId": 663,
  "countryLocationName": "Lithuania",
  "countryLocationPermalink": "Lithuania",
  "residence": { "hasResidence": 1, "cityId": 710, "regionId": 663, "countryId": 72, "bonuses": { "..." } },
  "division": 4,
  "muId": 893,
  "muCountryId": 72,
  "regimentId": 1390,
  "party": { "id": 3773, "party_id": 3773, "is_party_president": 0, "name": "...", "..." },
  "energy": 64,
  "energyToRecover": 10220,
  "energyFromFoodRemaining": 0,
  "energyPerInterval": 64,
  "hasFoodInInventory": true,
  "gold": 75689,
  "currencyAmount": 7120147,
  "userLevel": 1725,
  "hasCoreLoopAccess": false,
  "canWorkTrainAgainIn": 69744,
  "currentExperiencePoints": 8503502,
  "dailyOrders": [ { "battleId": 869119, "sideCountryId": 72, "..." } ],
  "dailiesToCollect": 0,
  "canChangeMPPPriorities": false,
  "canChangeTerrainPriorities": false,
  "canSwitchDivisions": true,
  "terrainSkills": { "1": { "terrain_id": 1, "skill_points": 40000 }, "...14 entries..." },
  "isTopCustomer": true,
  "loyaltyPoints": 2620,
  "boxKeys": { "cupid_key": 223 }
}
```

> **Note:** The `division` field may differ from the battlefield page — on the homepage it shows the citizen's natural division (`4`), while on the battlefield it may show the currently viewed division (e.g., `11` for aircraft).

---

#### `erepublik.i18n`

Localized UI strings (10 sections):

```json
{
  "shortcut": "Shortcut:",
  "waiting": "waiting",
  "missions": {
    "status1Title": "New mission",
    "status2Title": "Ongoing mission",
    "status3Title": "Click to complete",
    "status4Title": "Rewarded",
    "triviaInputText": "Type in the answer...",
    "triviaSubmitText": "Submit answer"
  },
  "countdownLang": {
    "daysLabel": "Days",
    "dayLabel": "Day",
    "hoursLabel": "Hrs",
    "minutesLabel": "Min",
    "secondsLabel": "Sec"
  },
  "boosterActivation": {
    "baConfirm": "Are you sure you want to use this Booster?",
    "baActivate": "Activate Booster",
    "baYour": "Your ",
    "baAvailability": "Booster availability",
    "baDisabled": "A better Booster is already active",
    "disabledCatchupBooster": "Conditions for activating this booster not met!"
  },
  "citizen": {
    "regionSelect": "Please select a region",
    "friends": "Friends",
    "birthday": "eRepublik birthday"
  },
  "residence": {
    "nomad": "You don\u2019t have a residence city",
    "home": "Residence bonuses available",
    "neutral": "Residence bonuses partially available. Energy Pool bonus expires in:",
    "away": "Residence bonuses expired. Travel back to your residence to reactivate them.",
    "notificationTitle": "Residence bonuses expired.",
    "notificationBody": "Travel back to your residence to reactivate them."
  },
  "sliderCaptcha": {
    "loading": "Loading \u2026",
    "tryAgain": "Try again",
    "slideToUnlock": "Slide to complete the puzzle",
    "clickToUnlock": "Click and select in this order (left to right)",
    "verifyAccount": "Verify",
    "successText": "Success",
    "helpText": "Select the symbols in the image in the specified order"
  },
  "citizenAchievements": {
    "newAchievement": "New Achievement",
    "postAchievement": "Post achievement on My Feeds.",
    "reward": "Reward",
    "gold": "Gold",
    "goldAmount": "%%1%% Gold",
    "currency": "Currency",
    "currencyAmount": "%%1%% LTL",
    "great": "Great"
  },
  "dailyMissions": {
    "timer": "Daily missions reset after this timer ends",
    "claim": "Claim",
    "quantity": "Quantity",
    "close": "Close",
    "dailyChallenge": "Daily Challenge",
    "activityPoints": "Activity Points",
    "refreshesIn": "Refreshes in",
    "reachActivityPoints": "Reach %%1%% Activity Points to collect these rewards",
    "rewardsCollected": "You have collected these rewards",
    "allMissionsCompleted": "You have completed all the missions",
    "goToMissionText": "Go \u00bb",
    "goToURL": "Go to %%1%%",
    "myCompaniesText": "My Companies",
    "campaignsText": "Campaigns",
    "trainingGroundsText": "Training Grounds",
    "inventoryText": "Inventory",
    "marketplaceText": "Marketplace",
    "exchangeText": "Monetary Market",
    "travelText": "Travel"
  }
}
```

- Text placeholders use `%%1%%`, `%%2%%` for variable substitution
- The `currencyAmount` template is localized with the citizen's currency name

---

#### `erepublik.promos`

Active promotional events (all boolean flags):

```json
{
  "consumption_boost": false,
  "winter_events": false,
  "week_of_heroes": false,
  "raffle_top_5": false,
  "winner_takes_it_all": false,
  "soldiers_of_fortune": false,
  "regiment_rumble": false
}
```

When an event is active, the corresponding flag is `true` and additional event-specific UI is rendered on the homepage.

---

#### `erepublik.shortcuts`

Navigation shortcuts displayed in the sidebar:

```json
{
  "links": {
    "inventory": "/en/main/inventory",
    "myland": "//www.erepublik.com/en/economy/myCompanies",
    "campaigns": "//www.erepublik.com/en/military/campaigns",
    "top_news": "//www.erepublik.com/en/news/international",
    "new_message": "//www.erepublik.com/en/main/messages-compose/0",
    "world_map": "//www.erepublik.com/en/map",
    "military_unit": "//www.erepublik.com/en/main/group-home/military",
    "alerts": "//www.erepublik.com/en/main/notifications",
    "residence": "//www.erepublik.com/en/main/myResidence",
    "armory": "//www.erepublik.com/en/military/armory"
  },
  "names": {
    "inventory": "Storage",
    "myland": "My places",
    "campaigns": "Campaigns",
    "top_news": "Top news",
    "new_message": "New Message",
    "world_map": "World map",
    "military_unit": "Military unit",
    "alerts": "Notifications",
    "residence": "Residence",
    "armory": "Protectors of Nations"
  }
}
```

- `links` — URL path for each shortcut (some protocol-relative, some path-only)
- `names` — Display label for each shortcut

---

#### `erepublik.angularTemplates`

Template URLs loaded by AngularJS controllers:

| Template Name | Description |
|---------------|-------------|
| `CitizenAchievements` | Achievement popup overlay |
| `DailyMissions` | Daily missions panel |
| `CitizenRevival` | Revival mechanism UI |
| `LoginRewardsRevival` | Login rewards revival popup |
| `SideNotification` | Side notification banners |
| `Hovercard` | Citizen hovercard tooltip |

These are short URL references (e.g., `/angularTemplates/CitizenAchievements`) loaded on demand.

---

#### `Environment`

Session environment flags:

```json
{
  "facebookSession": 0,
  "isInIframe": false,
  "isOnFacebook": false,
  "isWebView": false,
  "isOnHomepage": true,
  "isSymfony": false,
  "logEnabled": false,
  "hasSessionCaptcha": false,
  "sessionId": "9c1471f8235385cc96cc4d525b91e70d"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `facebookSession` | number | `0` = not a Facebook session |
| `isInIframe` | boolean | Whether the page is inside an iframe |
| `isOnFacebook` | boolean | Whether accessed via Facebook app |
| `isWebView` | boolean | Whether accessed via mobile webview |
| `isOnHomepage` | boolean | `true` on the homepage |
| `isSymfony` | boolean | Whether using Symfony routing |
| `logEnabled` | boolean | Whether client-side logging is enabled |
| `hasSessionCaptcha` | boolean | Whether session CAPTCHA verification is pending |
| `sessionId` | string | Session ID (= `erpk` cookie value) |

---

#### `energyData`

Energy recovery status (also merged into `erepublik.citizen` via `$j.extend`):

```json
{
  "energy": 64,
  "energyPoolLimit": 10220,
  "recoverableEnergy": 0,
  "energyPerInterval": 64,
  "energyStatus": "recovering"
}
```

| Field | Type | Description |
|-------|------|-------------|
| `energy` | number | Current energy |
| `energyPoolLimit` | number | Maximum energy pool (affected by residence) |
| `recoverableEnergy` | number | Energy that can be recovered by eating |
| `energyPerInterval` | number | Energy recovered per 6-minute interval |
| `energyStatus` | string | Recovery status: `"recovering"`, `"full"` |

---

#### `SERVER_DATA.wallData`

Social feed configuration for the homepage wall. Set via `$j.extend(SERVER_DATA, { wallData: {...} })`:

```json
{
  "citizenFeeds": {
    "country": {
      "name": "Lithuania",
      "showName": false,
      "avatar": "//www.erepublik.net/images/flags_png/S/Lithuania.png",
      "showAvatar": true,
      "feedRoute": "country-post",
      "commentRoute": "country-comment",
      "viewPostParameter": "countryPost",
      "unreadCount": 15,
      "isDefault": false,
      "firstPostId": null,
      "canPost": true,
      "postAs": null,
      "placeholder": "Say something to your countrymen"
    },
    "friends": { "..." },
    "militaryUnit": { "..." },
    "party": { "..." },
    "community": { "..." }
  },
  "citizenInfo": {
    "id": 4690052,
    "name": "driver sti",
    "sex": "",
    "created_at": "2011-04-12 13:22:14",
    "is_organization": 0,
    "is_alive": 1,
    "is_validated": 1,
    "has_avatar": 1,
    "parent_id": 0,
    "is_payer": 1,
    "has_jid": 1,
    "avatar_version": "fa70b874e2f337186b979af9404828eb",
    "avatar_file": "png",
    "level": 1725,
    "avatar": "https://cdnt.erepublik.net/.../avatar.png?...",
    "profile_url": "//www.erepublik.com/en/citizen/profile/4690052"
  },
  "defaultFeed": "friends",
  "firstPostId": false,
  "isContentBlocked": false,
  "postsPerPage": 20,
  "postCharactersLimit": 280,
  "commentsPerPage": 5,
  "commentCharactersLimit": 480,
  "dailyOrder": {
    "showOnAllFeeds": true,
    "progress": {
      "isCompleted": false,
      "current": 0,
      "required": 25
    },
    "battle": {
      "id": 869119,
      "sideId": 72,
      "url": "//www.erepublik.com/en/military/battlefield-choose-side/869119/72",
      "text": "Fight for <img ...> <b>Lithuania</b> in <br><em>Jiangsu</em>"
    },
    "hasMore": false
  }
}
```

**`citizenFeeds`** — 5 feed tabs (country, friends, militaryUnit, party, community), each with:

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Feed display name |
| `showName` | boolean | Whether to show the name in the tab |
| `avatar` | string | Feed avatar/icon URL |
| `showAvatar` | boolean | Whether to show the avatar |
| `feedRoute` | string | API route suffix for loading posts (e.g., `"country-post"`) |
| `commentRoute` | string | API route suffix for loading comments |
| `viewPostParameter` | string | URL parameter name for viewing a post |
| `unreadCount` | number | Number of unread posts in this feed |
| `isDefault` | boolean | Whether this is the default feed tab |
| `firstPostId` | number/null | ID of the first post (for pagination) |
| `canPost` | boolean | Whether the citizen can post to this feed |
| `postAs` | object/null | Posting identity (null = post as self) |
| `placeholder` | string | Input placeholder text |

**`citizenInfo`** — Extended citizen info for the wall (different from `erepublik.citizen`):

| Field | Type | Description |
|-------|------|-------------|
| `id` | number | Citizen ID |
| `name` | string | Display name |
| `sex` | string | Gender (empty string if unset) |
| `created_at` | string | Account creation datetime (`"YYYY-MM-DD HH:MM:SS"`) |
| `is_organization` | number | `0` = regular citizen, `1` = organization |
| `is_alive` | number | `1` if the citizen is alive |
| `is_validated` | number | `1` if email is validated |
| `has_avatar` | number | `1` if citizen has a custom avatar |
| `parent_id` | number | Parent citizen ID (`0` = no parent) |
| `is_payer` | number | `1` if citizen has made purchases (VIP/top customer) |
| `has_jid` | number | `1` if citizen has a Jabber/XMPP ID |
| `avatar_version` | string | Avatar cache-busting hash |
| `avatar_file` | string | Avatar file extension (`"png"`, `"jpg"`) |
| `level` | number | Experience level |
| `avatar` | string | Full avatar URL (CDN) |
| `profile_url` | string | Protocol-relative URL to citizen profile |

**`dailyOrder`** — Daily order widget shown on all feeds:

| Field | Type | Description |
|-------|------|-------------|
| `showOnAllFeeds` | boolean | Whether to show the daily order across all feed tabs |
| `progress.isCompleted` | boolean | Whether the daily order is completed |
| `progress.current` | number | Current kill count |
| `progress.required` | number | Required kill count |
| `battle.id` | number | Battle ID |
| `battle.sideId` | number | Country ID to fight for |
| `battle.url` | string | Battle choose-side URL |
| `battle.text` | string | Formatted HTML text (includes flag image and region name) |
| `hasMore` | boolean | Whether there are additional daily orders |

---

#### `ErpkPvp` + `dictatorships` + `empires`

PvP/chat configuration and political state maps:

```javascript
var ErpkPvp = (ErpkPvp || {});
ErpkPvp.bosh_service = 'unassigned2';
ErpkPvp.muc_host = 'unassigned1';
ErpkPvp.citizenId = 4690052;

const dictatorships = {"47":1,"67":1,"28":1,"26":1,"33":1,"69":1,"170":1,"168":1,"30":1};
const empires = {"35":1};
```

| Variable | Type | Description |
|----------|------|-------------|
| `ErpkPvp.bosh_service` | string | BOSH/XMPP service endpoint (often `"unassigned"`) |
| `ErpkPvp.muc_host` | string | Multi-user chat host (often `"unassigned"`) |
| `ErpkPvp.citizenId` | number | Current citizen ID |
| `dictatorships` | object | Map of country ID → `1` for countries currently under dictatorship |
| `empires` | object | Map of country ID → `1` for countries currently holding empire status |

> **Useful:** The `dictatorships` and `empires` maps provide a real-time snapshot of the political state of all countries. As of this capture: 9 dictatorships (country IDs: 47, 67, 28, 26, 33, 69, 170, 168, 30) and 1 empire (country ID: 35).

---

#### Other Embedded Variables

```javascript
var csrfToken = '99e749d8bab079654efe0b67c298b9a2';
var culture = 'en';
var domainname = 'erepublik.com';

// Energy & food
var energyData = { /* see energyData section */ };
var food_remaining = 0;  // or parseInt("0", 10)
var linkForBuy = '/en/economy/marketplace#72/1';
var textForBuy = "Buy food";
var smallestFood = {"q":1,"use":2};

// Event popups (seasonal)
var eventPopupAutoShowName = 'winterOlympicsPopup';  // or 'valentinesDayPopup'
```

| Variable | Type | Description |
|----------|------|-------------|
| `csrfToken` | string | CSRF token for POST requests |
| `culture` | string | Language culture code |
| `food_remaining` | number | Remaining food items that can be eaten |
| `linkForBuy` | string | Marketplace URL for buying food (countryId/quality) |
| `smallestFood` | object | Smallest food available: `q` = quality, `use` = energy per unit |
| `eventPopupAutoShowName` | string | Name of the auto-show event popup (seasonal) |

---

### AngularJS Controllers

The homepage uses AngularJS (`ng-app="ErpkApp"`) with these controllers:

| Controller | Section | Description |
|------------|---------|-------------|
| `ErpkMenuPromosController` | Top menu | Promotional banners and event links |
| `ErpkPopupPromosController` | Popups | Event popup modals (seasonal events) |
| `WeeklyChallengeController` | Sidebar | Weekly challenge progress widget |
| `ErpkHpTopNewsController` | News section | Top news/articles display |
| `ObjectivesController` | Sidebar | Daily objectives progress |
| `ListCampaignsControllerHomepage` | Battle listing | Active campaigns list with contributions, COTD, country battles |
| `ErpkFeedController` | Main content | Social wall/feed (posts, comments) |
| `SideNotificationController` | Overlay | Side notification banners |
| `CitizenAchievementsController` | Overlay | Achievement popups |

The battle listing section uses Angular templates with `ng-repeat`:
```html
<div ng-repeat="(sectionName, section) in campaigns.battles">
  <!-- Sections: contributions, cotd, myCountry, allies, others -->
</div>
```

Campaign data is loaded asynchronously via the `ListCampaignsControllerHomepage` controller (uses `/en/military/campaignsJson/citizen`).

---

### Data Extraction

To extract embedded data from the homepage HTML:

**Python example:**

```python
import re
import json

# 1. Load the HTML
with open('homepage.html', 'r') as f:
    html = f.read()

# 2. Extract the main erepublik object using brace counting
start = html.find('var erepublik = {')
brace_start = html.index('{', start)
depth, i = 0, brace_start
while i < len(html):
    if html[i] == '{': depth += 1
    elif html[i] == '}':
        depth -= 1
        if depth == 0: break
    elif html[i] == '"':
        i += 1
        while i < len(html) and html[i] != '"':
            if html[i] == '\\': i += 1
            i += 1
    i += 1

raw_obj = html[brace_start:i+1]

# 3. Convert new Date('...') to JSON strings
json_str = re.sub(r"new Date\('([^']+)'\)", r'"\1"', raw_obj)

# 4. Parse
data = json.loads(json_str)
citizen = data['citizen']
settings = data['settings']

# 5. Extract energyData
match = re.search(r'var energyData = ({[^}]+})', html)
energy = json.loads(match.group(1))

# 6. Extract wallData (has unquoted JS keys)
match = re.search(r'\$j\.extend\(SERVER_DATA, \{\s*wallData:', html)
# ... use brace counting + key quoting (see Notes)
```

> **Note:** The `wallData` block uses unquoted JavaScript keys (e.g., `wallData: {citizenFeeds: ...}`). Convert with: `re.sub(r'(\{|,)\s*(\w+)\s*:', r'\1"\2":', raw)` before parsing as JSON.

---

### Notes

- **HTML page, not JSON API**: Returns a full HTML document (~131 KB, ~2200 lines). All data is embedded in `<script>` blocks.
- **Multiple `<script>` blocks**: The page contains ~37 script blocks. Key data is in scripts 0, 20, 21, 30, 34, and 35.
- **`erepublik.citizen` is shared**: The same citizen object structure appears on the homepage, battlefield page, and other pages. It is extended by `energyData` via `$j.extend(erepublik.citizen, energyData)`.
- **Campaign data is async**: Unlike other embedded objects, the battle listing data is loaded by the `ListCampaignsControllerHomepage` AngularJS controller via AJAX after page load (see `/en/military/campaignsJson/citizen`).
- **`SERVER_DATA` is extended multiple times**: At least 2 `$j.extend(SERVER_DATA, ...)` calls — one for `csrfToken` refresh, one for `wallData`.
- **Event popups are seasonal**: `eventPopupAutoShowName` changes based on active events (e.g., `winterOlympicsPopup`, `valentinesDayPopup`). Multiple event popups can coexist.
- **`dictatorships` and `empires` maps**: These `const` declarations provide a real-time snapshot of which countries are dictatorships or empires. Useful for bot logic that needs to check political status without additional API calls.
- **Pomelo WebSocket**: `erepublik.settings.pomelo` contains the same WebSocket configuration as the battlefield page — `gate.erepublik.com:3050` with the session token as auth. Can be used for real-time notifications on the homepage.
- **XMPP/PvP chat**: `ErpkPvp.bosh_service` and `muc_host` are typically `"unassigned"` — the PvP chat system appears inactive or deprecated.
- **`citizenInfo` vs `citizen`**: `wallData.citizenInfo` contains account metadata (`created_at`, `is_alive`, `is_validated`, `is_payer`) not available in `erepublik.citizen`, while `erepublik.citizen` has gameplay data (energy, gold, division, daily orders) not in `citizenInfo`.

---
