# Military Module

#erepublik #api #military #combat

[< Back to Table of Contents](../API_TOC.md) | [< Back to Military Overview](../API_MILITARY.md)

---

## Overview

Military endpoints cover combat mechanics, battles, wars, weapons, and related functionality.

---

## Sub-files

| File | Description | Endpoints |
|------|-------------|-----------|
| [campaigns.md](campaigns.md) | Active campaigns and citizen campaign data | List Active Campaigns, List Country Campaigns, Get Citizen Campaigns Data |
| [battle-info.md](battle-info.md) | Battle statistics and console data | Get Battle Statistics, Get Battle Statistics (Console), Get Battle Console Data, Get War History (Previous Rounds) |
| [armory.md](armory.md) | Vehicle management and protector system | Get Armory Overview, Enroll Vehicle, Get Armory Protectors |
| [deployment.md](deployment.md) | Battle deployment and fuel | Get Battle Deployment Inventory, Start Battle Deployment, Get Fuel Status, Start Deployment (AJAX), Cancel Deployment, Deployment Report Popup |
| [battlefield.md](battlefield.md) | Battlefield page and embedded data | Get Battlefield Page, BATTLE_SERVER_DATA JSON structure |
| [battlefield-ajax.md](battlefield-ajax.md) | Battlefield AJAX action endpoints | Battle Console, Deploy Bomb, Activate Booster, Battlefield Travel, Switch Division, PvP Queue, PvP Inventory, Activate Battle Effect, List Battle Objectives, Battle Stats, Energy Refill, Refill Energy, Mercenary Campaign, Battle Map Status |
| [websocket.md](websocket.md) | Real-time battle updates | Pomelo WebSocket |
| [wars.md](wars.md) | War support and timing | Support Resistance War (Fund), Determining Battle Finish Time |
| [frontend-reference.md](frontend-reference.md) | Frontend code references | AngularJS Controllers, JavaScript Bundles, Battlefield Automation Summary |

---

## Endpoints Quick Reference

| Endpoint | Method | Auth | File |
|----------|--------|------|------|
| `/en/military/campaignsJson/list` | GET | No | [campaigns.md](campaigns.md) |
| `/en/military/campaignsJson/countryId/{countryId}` | GET | No | [campaigns.md](campaigns.md) |
| `/en/military/campaignsJson/citizen` | GET | Yes | [campaigns.md](campaigns.md) |
| `/en/military/battle-stats/{battleId}/{division}/{battleZoneId}` | GET | Yes | [battle-info.md](battle-info.md) |
| `/en/military/battle-console` | POST | Yes | [battle-info.md](battle-info.md) |
| `/en/military/armory-data/overview` | POST | Yes | [armory.md](armory.md) |
| `/en/military/armory-actions` | POST | Yes | [armory.md](armory.md) |
| `/en/military/armory-data/protectors` | POST | Yes | [armory.md](armory.md) |
| `/en/military/fightDeploy-getInventory` | POST | Yes | [deployment.md](deployment.md) |
| `/en/military/fightDeploy-startDeploy` | POST | Yes | [deployment.md](deployment.md) |
| `/en/military/campaigns` | GET | Yes | [deployment.md](deployment.md) |
| `/en/military/fightDeploy-cancelDeploy` | POST | Yes | [deployment.md](deployment.md) |
| `/en/military/fightDeploy-deployReportPopup` | GET | Yes | [deployment.md](deployment.md) |
| `/en/military/battlefield/{battleId}` | GET | Yes | [battlefield.md](battlefield.md) |
| `/en/military/deploy-bomb` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/military/fight-activateBooster` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/main/battlefieldTravel` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/main/switch-division` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/main/pvp-queue` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/military/pvp-inventory/` | GET | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/main/fight-activateBattleEffect` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/main/fight-listObjectives` | GET | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/military/battle-stats/{battleId}` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/economy/energyRefill-getInventory` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/economy/refillEnergy` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/main/mercenary-campaign/subscribe/` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/main/mercenary-campaign/unsubscribe/` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/military/battle-map-status/{battleId}` | POST | Yes | [battlefield-ajax.md](battlefield-ajax.md) |
| `/en/military/rw-support` | POST | Yes | [wars.md](wars.md) |

---

## Template

Use this template when documenting new endpoints:

```markdown
## Endpoint Name

**Method:** GET/POST
**URL:** `/en/path/to/endpoint`
**Auth Required:** Yes/No

### Description

Brief explanation of what this endpoint does.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| param1 | string | Yes | Description |

### Headers

| Header | Value | Description |
|--------|-------|-------------|
| Content-Type | application/json | ... |

### Example Request (cURL)

\`\`\`bash
curl -X GET "https://www.erepublik.com/en/..." \
  -H "Cookie: erpk=..."
\`\`\`

### Example Response

\`\`\`json
{
  "success": true,
  "data": {}
}
\`\`\`

### Notes

Any additional information, gotchas, or observations.
```

---

## Related

- [API Table of Contents](../API_TOC.md)
- [Authentication](../API_AUTH.md)
- [Social API](../API_SOCIAL.md)
- [Economy API](../API_ECONOMY.md)
