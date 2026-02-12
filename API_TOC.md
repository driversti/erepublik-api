# eRepublik API Documentation

#erepublik #api #documentation

> 🤖 **Maintained by Claude** - This API documentation is managed by Claude Code. Feel free to ask Claude to add, update, or reorganize endpoints as you discover them.

## Overview

This documentation covers the unofficial API endpoints for [eRepublik](https://www.erepublik.com), a browser-based strategy MMO. These endpoints have been discovered through reverse engineering and may change without notice.

**Base URL:** `https://www.erepublik.com`

> **Note:** Most endpoints require authentication. See [Authentication](API_AUTH.md) for details.

---

## Modules

| Module | Description | Link |
|--------|-------------|------|
| Authentication | Login flow, sessions, tokens | [API_AUTH.md](API_AUTH.md) |
| CAPTCHA & Verification | Session verification, anti-bot challenges, CAPTCHA solving | [API_CAPTCHA.md](API_CAPTCHA.md) |
| World & Travel | Countries, regions, travel, geography | [API_WORLD.md](API_WORLD.md) |
| Country | Country-specific pages, politics, society | [API_COUNTRY.md](API_COUNTRY.md) |
| Military | Combat, battles, wars, weapons | [API_MILITARY.md](API_MILITARY.md) |
| Social | Citizens, friends, messages | [API_SOCIAL.md](API_SOCIAL.md) |
| Notifications | Alerts, reports, notifications | [API_NOTIFICATIONS.md](API_NOTIFICATIONS.md) |
| Economy | Marketplace, work, companies, currencies | [API_ECONOMY.md](API_ECONOMY.md) |
| Rewards | Daily objectives, missions, achievements | [API_REWARDS.md](API_REWARDS.md) |
| Reference Data | Country & region ID lookup tables | [API_REFERENCE.md](API_REFERENCE.md) |

---

## Quick Reference

### Authentication
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/login` | POST | User login |

### CAPTCHA & Verification
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/sessionCaptcha` | GET | Get session verification countdown UI |
| `/en/main/sessionUnlockPopup` | GET | Get CAPTCHA modal popup HTML |
| `/en/main/sessionGetChallenge` | POST | Get CAPTCHA challenge image and IDs |
| `/en/main/sessionUnlock` | POST | Submit CAPTCHA solution |

### World & Travel
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/travelData` | POST | Get all countries, regions, and travel costs |
| `/en/main/travel` | POST | Execute travel to a region (costs currency/tickets) |
| `/en/main/city-data/{cityId}/overview` | GET | Get city overview (stats, residence, government, region info) |
| `/en/main/city-data/{cityId}/residents` | GET | Get city residents list (paginated) |
| `/en/main/city-data/{cityId}/properties` | GET | Get citizen's properties in a city (houses, factories) |
| `/en/main/city-search` | GET | Search cities by name, region, or country (autocomplete) |
| `/en/main/city-actions` | POST | Perform city actions (establish residence) |
| `/en/main/map-data` | GET | Get world map data (regions, ownership, battles) |

### Country
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/country/society/{countryName}` | GET | Get country society page - citizen stats, regions, country ID reference (HTML) |
| `/en/country/economy/{countryName}` | GET | Get country economy page - treasury, tax revenue, GDP, resources, taxes (HTML+JS) |
| `/en/country/politics/{countryName}` | GET | Get country politics page - dictator (if dictatorship), president, cabinet, congress parties (HTML) |
| `/en/country/military/{countryName}` | GET | Get country military page - natural enemy (with "Since" date), defence shield, airstrike readiness + donation form, active wars with allies, resistance wars with determination, MPP with expiry (HTML) |
| `/en/country-administration/{countryName}/{page}` | GET | Get country administration page - military priorities, manage organizations, law proposals history with pagination (HTML) |
| `/en/{countryName}/new-donation` | POST | Propose treasury donation (Congress) |

### Military
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/military/campaignsJson/list` | GET | List all active campaigns/battles (public) |
| `/en/military/campaignsJson/countryId/{id}` | GET | Get campaigns for a specific country |
| `/en/military/campaignsJson/citizen` | GET | Get campaigns for the logged-in citizen |
| `/en/military/battlefield/{battleId}` | GET | Full battlefield page with embedded battle state (HTML) |
| `/en/military/battle-console` | POST | Get live battle zone stats, fighters, combat orders |
| `/en/military/battle-stats/{battleId}` | POST | Get battle statistics |
| `/en/military/deploy-bomb` | POST | Deploy a bomb (Big Bomb, Small Bomb, Cruise Missile) |
| `/en/military/fight-activateBooster` | POST | Activate a combat booster |
| `/en/military/fightDeploy-getInventory` | POST | Get deployment inventory (weapons, vehicles, energy) |
| `/en/military/fightDeploy-startDeploy` | POST | Start automated deployment |
| `/en/military/fightDeploy-cancelDeploy` | POST | Cancel active deployment |
| `/en/military/pvp-inventory/` | GET | Get PvP inventory (HTML) |
| `/en/military/battle-map-status/{battleId}` | POST | Get campaign map division results |
| `/en/military/armory-data/overview` | GET | Get armory overview data |
| `/en/military/armory-data/protectors` | GET | Get protectors data |
| `/en/main/battlefieldTravel` | POST | Choose side & travel to battle region |
| `/en/main/switch-division` | POST | Switch between divisions |
| `/en/main/pvp-queue` | POST | Join/leave PvP queue |
| `/en/main/fight-activateBattleEffect` | POST | Activate seasonal battle effect |
| `/en/main/fight-listObjectives` | GET | List battle objectives |
| `/en/main/mercenary-campaign/subscribe/` | POST | Subscribe to mercenary campaign |
| `/en/main/mercenary-campaign/unsubscribe/` | POST | Unsubscribe from mercenary campaign |
| `/en/economy/refillEnergy` | POST | Eat food / use energy bars |
| `/en/economy/energyRefill-getInventory` | POST | Get energy refill inventory |
| `/en/country/military/{countryName}` | GET | Country military page (natural enemy, defence shield, airstrike, wars, MPP) |

### Social
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/citizen-profile-json-global/{citizenId}` | GET | Get citizen profile (public) |
| `/en/main/citizen-profile-json-personal/{citizenId}` | GET | Get citizen profile (authenticated) |
| `/en/main/citizen-addRemoveFriend` | POST | Add or remove friend |

### Notifications
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/notificationsAjax/report/{page}` | GET | Get paginated notifications |

### Economy
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/economy/marketplace` | GET | Marketplace page - product browser with AngularJS, industry/quality filters, industryJSON (HTML) |
| `/en/main/shopItemsJson` | GET | Get Gold Shop and VIP Shop items |
| `/en/main/job-data` | GET | Get employment and work status |
| `/en/economy/workOvertime` | POST | Work overtime for extra salary |
| `/en/economy/work` | POST | Work as Manager (WAM) in companies |
| `/en/main/training-grounds-json` | GET | Get training grounds status |
| `/en/economy/exchange-market/` | GET | Monetary market page - exchange offers, rates, currency data (HTML) |
| `/en/economy/exchange/retrieve/` | POST | Get paginated exchange offers |
| `/en/economy/exchange/purchase/` | POST | Buy currency from an exchange offer |
| `/en/economy/exchange/create/` | POST | Create a new exchange offer |
| `/en/economy/exchange/update/` | POST | Update an existing exchange offer |
| `/en/economy/exchange/delete/` | POST | Delete an exchange offer |

### Rewards
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/objective-status` | POST | Get daily objective progress and rewards |
| `/en/main/daily-missions-data` | POST | Get all daily missions with progress |
| `/en/main/mission-solve` | POST | Claim rewards for a completed mission |
| `/en/main/weekly-challenge-data` | GET | Get weekly challenge progress and rewards |
| `/en/main/weekly-challenge-collect-reward` | POST | Collect a weekly challenge reward |

---

## Common Headers

Most authenticated requests require these headers:

| Header | Description |
|--------|-------------|
| `Cookie` | Session cookie (`erpk=...`) |
| `X-Requested-With` | `XMLHttpRequest` for AJAX calls |
| `Content-Type` | `application/x-www-form-urlencoded` or `application/json` |

---

## Related Links

- [[eRepublik]] - Main eRepublik notes
- [eRepublik Official](https://www.erepublik.com)
