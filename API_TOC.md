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
| Military | Combat, battles, wars, weapons | [API_MILITARY.md](API_MILITARY.md) |
| Social | Citizens, friends, messages | [API_SOCIAL.md](API_SOCIAL.md) |
| Notifications | Alerts, reports, notifications | [API_NOTIFICATIONS.md](API_NOTIFICATIONS.md) |
| Economy | Marketplace, work, companies, currencies | [API_ECONOMY.md](API_ECONOMY.md) |
| Rewards | Daily objectives, missions, achievements | [API_REWARDS.md](API_REWARDS.md) |

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
| `/en/main/map-data` | GET | Get world map data (regions, ownership, battles) |

### Military
| Endpoint | Method | Description |
|----------|--------|-------------|
| *TBD* | - | Endpoints to be documented |

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
| `/en/main/shopItemsJson` | GET | Get Gold Shop and VIP Shop items |
| `/en/main/job-data` | GET | Get employment and work status |
| `/en/economy/workOvertime` | POST | Work overtime for extra salary |
| `/en/economy/work` | POST | Work as Manager (WAM) in companies |
| `/en/main/training-grounds-json` | GET | Get training grounds status |

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
