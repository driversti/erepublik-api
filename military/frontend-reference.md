# Frontend Reference

#erepublik #api #military #frontend #angularjs #javascript

[< Back to Military Module](README.md)

Reference documentation for the battlefield page's frontend code, including AngularJS controllers, JavaScript bundles, and automation flow.

**Related:** [battlefield.md](battlefield.md) | [battlefield-ajax.md](battlefield-ajax.md) | [deployment.md](deployment.md) | [websocket.md](websocket.md)

---

## Battlefield AngularJS Controllers Reference

The battlefield page uses these AngularJS controllers:

| Controller | Element ID | Purpose |
|------------|-----------|---------|
| `BattleStatsController` | `#BattleStatsController` | Live stats, fighter rankings, combat orders, war history |
| `BattlefieldPlayersController` | `#battleConsole` | Player avatars on the battlefield animation |
| `BattleSetupController` | `#battleFooterbattleSetup` | Side selection, travel, division switching |
| `DeploymentController` | `#battleFooterDeploy` | Deploy panel (inventory, energy slider, vehicle selection) |
| `SpecialItemsController` | `#special_weapons_list_area` | Boosters, bombs, special weapons |
| `ErpkBattleFieldChangeSides` | `.switch_region_area` | Division/side switching button |
| `CampaignMapController` | `#CampaignMapController` | Campaign map overlay |
| `ValentinesController` | `#ValentinesController` | Seasonal Valentine's Day events |
| `SideNotificationController` | `#SideNotificationController` | Side notifications (achievements, rewards) |
| `CitizenAchievementsController` | `#CitizenAchievementsController` | Achievement popups |

---

## JavaScript Bundles Reference

Key JS files loaded on the battlefield page:

| File | Size | Purpose |
|------|------|---------|
| `battle_new.{ts}.js` | ~86 KB | Core battle logic: PvP, bomb deployment, division switching, food inventory |
| `erepublik.{ts}.js` | ~411 KB | Main framework: popups, general UI, page routing |
| `erpk_angular_controllers.{ts}.js` | ~391 KB | All AngularJS controllers (battle stats, deploy, boosters, economy) |
| `erpk_angular_services.{ts}.js` | ~24 KB | Angular services and factories |
| `pomelo/lib/build/build.{ts}.js` | -- | Pomelo WebSocket client for real-time updates |
| `pvp/inventory.{ts}.js` | -- | PvP inventory management |

> `{ts}` = build timestamp (e.g., `1770287129`)

---

## Battlefield Automation Summary

For fighting automation, the key endpoints and data flow are:

1. **Load battlefield page** -> `GET /en/military/battlefield/{battleId}` -> extract `SERVER_DATA` (CSRF token, battle zones, boosters, travel)
2. **Choose side & travel** (if needed) -> `POST /en/main/battlefieldTravel`
3. **Switch division** (if needed) -> `POST /en/main/switch-division`
4. **Check energy** -> `energyData` from page or poll `globalNS.userInfo.wellness`
5. **Eat food / use energy bars** -> `POST /en/economy/refillEnergy`
6. **Activate boosters** -> `POST /en/military/fight-activateBooster`
7. **Deploy** -> `POST /en/military/fightDeploy-startDeploy` (automated fighting)
8. **Deploy bombs** -> `POST /en/military/deploy-bomb`
9. **Monitor progress** -> WebSocket (Pomelo) or poll `POST /en/military/battle-console`
10. **Cancel deployment** -> `POST /en/military/fightDeploy-cancelDeploy`

**Important values from `SERVER_DATA` for automation:**

| Value | Source | Use |
|-------|--------|-----|
| `csrfToken` | `SERVER_DATA.csrfToken` | Required `_token` param for all POST requests |
| `battleId` | `SERVER_DATA.battleId` | Battle identifier |
| `battleZoneId` | `SERVER_DATA.battleZoneId` | Current zone/division identifier |
| `mySideCountryId` | `SERVER_DATA.mySideCountryId` | Which side you're on |
| `health` | `SERVER_DATA.health` | Current energy |
| `travelRequired` | `SERVER_DATA.travelRequired` | Whether travel is needed |
| `canSwitchDivisions` | `SERVER_DATA.canSwitchDivisions` | Whether division switching is allowed |
| `boosters.inactive` | `SERVER_DATA.boosters.inactive` | Available boosters to activate |
| `specialWeapons` | `SERVER_DATA.specialWeapons` | Available bombs to deploy |
| `playerStrength` | `erepublik.promos.April1st2017.playerStrength` | Current strength value |

**Detailed endpoint documentation:**

- Battlefield page: [battlefield.md](battlefield.md)
- AJAX endpoints: [battlefield-ajax.md](battlefield-ajax.md)
- Deployment: [deployment.md](deployment.md)
- WebSocket: [websocket.md](websocket.md)
