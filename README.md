# eRepublik API Documentation

#erepublik #api #documentation

Unofficial API documentation for [eRepublik](https://www.erepublik.com), a browser-based strategy MMO. All endpoints have been discovered through reverse engineering of the game's web client.

> **Disclaimer:** These are unofficial, undocumented endpoints. They may change or break at any time without notice. Use at your own risk.

**Base URL:** `https://www.erepublik.com`

> **Note:** Most endpoints require authentication. See [Authentication](auth/README.md) for details.

---

## Modules

| Module | Description | Files |
|--------|-------------|-------|
| [Authentication](auth/README.md) | Login flow, sessions, cookies | 1 |
| [CAPTCHA & Verification](captcha/README.md) | Anti-bot challenges, session verification | 1 |
| [Homepage](homepage/README.md) | Main dashboard -- citizen state, social feeds, settings | 1 |
| [World & Travel](world/README.md) | Travel, cities, world map, regions | 3 |
| [Country](country/README.md) | Society, economy, politics, military, administration pages | 7 |
| [Military](military/README.md) | Campaigns, battles, deployment, armory, battlefield, websocket | 9 |
| [Social](social/README.md) | Citizen profiles, friends, wall posts, search | 5 |
| [Notifications](notifications/README.md) | Alerts and reports | 1 |
| [Economy](economy/README.md) | Work, marketplace, exchange, companies, inventory | 11 |
| [Rewards](rewards/README.md) | Daily objectives, missions, weekly challenges, VIP | 4 |
| [Power Spin](powerspin/README.md) | Wheel of Fortune -- prizes, spins, jackpots | 1 |
| [Reference Data](reference/README.md) | Country & region ID lookup tables (74 countries, 574 regions) | 2 |

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

### Homepage
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en` | GET | Main dashboard page (HTML) |

### World & Travel
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/travelData` | POST | Get all countries, regions, and travel costs |
| `/en/main/travel` | POST | Execute travel to a region |
| `/en/main/city-data/{cityId}/overview` | GET | Get city overview |
| `/en/main/city-data/{cityId}/residents` | GET | Get city residents list (paginated) |
| `/en/main/city-data/{cityId}/properties` | GET | Get citizen's properties in a city |
| `/en/main/city-search` | GET | Search cities by name, region, or country |
| `/en/main/city-actions` | POST | Perform city actions (establish residence) |
| `/en/main/map-data` | GET | Get world map data (regions, ownership, battles) |

### Country
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/country/society/{countryName}` | GET | Country society page (HTML) |
| `/en/country/economy/{countryName}` | GET | Country economy page (HTML+JS) |
| `/en/country/politics/{countryName}` | GET | Country politics page (HTML) |
| `/en/country/military/{countryName}` | GET | Country military page (HTML) |
| `/en/country-administration/{countryName}/{page}` | GET | Country administration page (HTML) |
| `/en/{countryName}/new-donation` | POST | Propose treasury donation (Congress) |

### Military
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/military/campaignsJson/list` | GET | List all active campaigns (public) |
| `/en/military/campaignsJson/countryId/{id}` | GET | Get campaigns for a specific country |
| `/en/military/campaignsJson/citizen` | GET | Get campaigns for the logged-in citizen |
| `/en/military/battlefield/{battleId}` | GET | Full battlefield page (HTML) |
| `/en/military/battle-console` | POST | Get live battle zone stats |
| `/en/military/battle-stats/{battleId}` | POST | Get battle statistics |
| `/en/military/deploy-bomb` | POST | Deploy a bomb |
| `/en/military/fight-activateBooster` | POST | Activate a combat booster |
| `/en/military/fightDeploy-getInventory` | POST | Get deployment inventory |
| `/en/military/fightDeploy-startDeploy` | POST | Start automated deployment |
| `/en/military/fightDeploy-cancelDeploy` | POST | Cancel active deployment |
| `/en/military/pvp-inventory/` | GET | Get PvP inventory (HTML) |
| `/en/military/battle-map-status/{battleId}` | POST | Get campaign map division results |
| `/en/military/armory-data/overview` | GET | Get armory overview data |
| `/en/military/armory-data/protectors` | GET | Get protectors data |
| `/en/military/armory-actions` | POST | Enroll vehicle for a country |
| `/en/main/battlefieldTravel` | POST | Choose side & travel to battle |
| `/en/main/switch-division` | POST | Switch between divisions |
| `/en/main/pvp-queue` | POST | Join/leave PvP queue |
| `/en/main/fight-activateBattleEffect` | POST | Activate seasonal battle effect |
| `/en/main/fight-listObjectives` | GET | List battle objectives |
| `/en/main/mercenary-campaign/subscribe/` | POST | Subscribe to mercenary campaign |
| `/en/main/mercenary-campaign/unsubscribe/` | POST | Unsubscribe from mercenary campaign |
| `/en/economy/refillEnergy` | POST | Eat food / use energy bars |
| `/en/economy/energyRefill-getInventory` | POST | Get energy refill inventory |
| `/en/military/rw-support` | POST | Fund a resistance war |

### Social
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/citizen-profile-json-global/{citizenId}` | GET | Get citizen profile (public) |
| `/en/main/citizen-profile-json-personal/{citizenId}` | GET | Get citizen profile (authenticated) |
| `/en/main/citizen-addRemoveFriend` | POST | Add or remove friend |
| `/en/main/citizen-hovercard/{citizenId}` | GET | Get citizen hovercard |
| `/en/main/citizen-search?name={query}` | GET | Search citizens by name |
| `/en/main/news/rated/all/all/{page}/rss` | GET | News articles RSS feed |

### Notifications
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/notificationsAjax/report/{page}` | GET | Get paginated notifications |

### Economy
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/economy/marketplace` | GET | Marketplace page (HTML) |
| `/en/main/shopItemsJson` | GET | Get Gold Shop and VIP Shop items |
| `/en/main/job-data` | GET | Get employment and work status |
| `/en/economy/job-market-json/{countryId}/{page}/{sort}` | GET | Browse job market offers |
| `/en/economy/resign` | POST | Resign from current employer |
| `/en/economy/job-market-apply` | POST | Apply for a job |
| `/en/economy/workOvertime` | POST | Work overtime |
| `/en/economy/work` | POST | Work as Manager (WAM) |
| `/en/main/training-grounds-json` | GET | Get training grounds status |
| `/en/economy/exchange-market/` | GET | Monetary market page (HTML) |
| `/en/economy/exchange/retrieve/` | POST | Get exchange offers |
| `/en/economy/exchange/purchase/` | POST | Buy currency from exchange |
| `/en/economy/exchange/create/` | POST | Create exchange offer |
| `/en/economy/exchange/update/` | POST | Update exchange offer |
| `/en/economy/exchange/delete/` | POST | Delete exchange offer |
| `/en/economy/myCompanies` | GET | My Companies page (HTML) |

### Rewards
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/objective-status` | POST | Get daily objective progress |
| `/en/main/objective-claim-reward` | POST | Claim daily objective tier reward |
| `/en/main/daily-missions-data` | POST | Get all daily missions with progress |
| `/en/main/mission-solve` | POST | Claim mission reward |
| `/en/main/weekly-challenge-data` | GET | Get weekly challenge progress |
| `/en/main/weekly-challenge-collect-reward` | POST | Collect weekly challenge reward |
| `/en/main/vip-claim` | POST | Claim daily VIP points |

### Power Spin
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/en/main/wheeloffortune-build` | POST | Build Wheel of Fortune configuration |
| `/en/main/wheeloffortune-spin` | POST | Spin the Wheel of Fortune |

---

## Common Headers

Most authenticated requests require these headers:

| Header | Description |
|--------|-------------|
| `Cookie` | Session cookie (`erpk=...`) |
| `X-Requested-With` | `XMLHttpRequest` for AJAX calls |
| `Content-Type` | `application/x-www-form-urlencoded` or `application/json` |

---

## Example Request

```bash
curl 'https://www.erepublik.com/en/main/citizen-profile-json-global/1234567' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

---

## Contributing

Found a new endpoint? Each endpoint should be documented with:

- HTTP method and URL
- Required parameters and headers
- Example request (cURL)
- Example response (JSON/HTML)
- Any relevant notes or gotchas

---

## Related Links

- [[eRepublik]] - Main eRepublik notes
- [eRepublik Official](https://www.erepublik.com)
