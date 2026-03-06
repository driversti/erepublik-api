# Shop

#erepublik #api #economy #shop

[< Back to Economy Overview](README.md)

Gold Shop and VIP Shop items -- retrieving available items, prices, discounts, and purchase limits.

**Related:** [Inventory](inventory.md) | [Boosters](boosters.md)

---

## Get Shop Items

**Method:** GET
**URL:** `/en/main/shopItemsJson`
**Auth Required:** Yes

### Description

Retrieves all available items in the Gold Shop and VIP Shop, including prices, discounts, purchase limits, and item details. This endpoint returns two main sections:
- **Gold Items:** Standard shop items purchasable with Gold or Loyalty Points (energy bars, fuel, boosters, bazooka parts, storage)
- **VIP Shop Items:** Special discounted items available at different VIP levels, purchasable with Gold, Raw Materials, or Currency

### Parameters

No parameters required.

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/shopItemsJson' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "error": false,
  "data": {
    "goldItems": {
      "sectionId": "goldItems",
      "title": "Gold Items",
      "items": [
        {
          "id": "fuel",
          "category": "fuel",
          "name": "Fuel",
          "details": {
            "description": "Increases your Deployment limit for the current week. Unused Fuel does not carry over to the next week. Fuel price starts at 1 Gold and increases with every purchase. Bought this week: 5"
          },
          "description": "+1 Fuel",
          "itemPurchaseLimit": 1,
          "initialPurchaseLimit": 1,
          "goldPrice": 1,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "isKey": false,
          "dotNotification": false
        },
        {
          "id": "refill",
          "category": "energyBars",
          "name": "1 Energy Bar",
          "details": {
            "description": "+200 Energy (Energy is vital in the eWorld as it is used in most of the Citizen's actions, such as fighting, training, working etc.)"
          },
          "description": "+200 Energy",
          "goldPrice": 5,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "lpPrice": 30,
          "lpCurrencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/loyalty_points.png",
          "lpCurrencyName": "Loyalty Points",
          "isKey": false,
          "dotNotification": false
        },
        {
          "id": "storage",
          "category": "storage",
          "name": "Storage",
          "details": {
            "description": "Extends the capacity of your Storage with 20000 Units (Storage is a building that stores products such as Energy Bars, Food, Weapons, Raw Materials etc.)"
          },
          "description": "+20000 Storage",
          "goldPrice": 100,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "lpPrice": 175,
          "lpCurrencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/loyalty_points.png",
          "lpCurrencyName": "Loyalty Points",
          "isKey": false,
          "dotNotification": false
        }
      ]
    },
    "vipShopItems": {
      "sectionId": "goldItems",
      "title": "VIP Shop",
      "items": [
        {
          "id": 3054,
          "vipLevel": 0,
          "type": "speed_booster_1_180",
          "quantity": 10,
          "currency": "gold",
          "cost": 0.7,
          "discount": 30,
          "data": {
            "name": "x2 Damage Accelerator",
            "type": "speed_booster",
            "image": "/images/icons/boosters/128px/speed.png",
            "quality": 1,
            "tooltip": "Hit x2 faster for 3 minutes",
            "discount": 30,
            "duration": 180
          },
          "maxQuantity": 10,
          "currencyIcon": "https://www.erepublik.net/images/icons/rewards/64px/gold.png",
          "currencyName": "Gold",
          "price": 0.7,
          "category": "vipShop",
          "vipShopItem": true,
          "name": "x2 Damage Accelerator",
          "description": "Hit x2 faster for 3 minutes",
          "discountClass": "blue",
          "itemPurchaseLimit": 10,
          "soldOut": false
        },
        {
          "id": 3055,
          "vipLevel": 1,
          "type": "damage_booster_50_7200",
          "quantity": 10,
          "currency": "food_raw",
          "cost": 6,
          "discount": 85,
          "currencyIcon": "https://www.erepublik.net/images/icons/industry/7/default_55x55.png",
          "currencyName": "Food Raw Materials",
          "price": 6,
          "name": "+50% Ground Damage",
          "description": "Hit 50% stronger in Ground Battles for 2 hours",
          "discountClass": "red",
          "itemPurchaseLimit": 10,
          "soldOut": false
        },
        {
          "id": 3074,
          "vipLevel": 11,
          "type": "catchup_booster_40_300_vip11",
          "quantity": 0,
          "currency": "unavailable",
          "cost": 0,
          "discount": 10,
          "maxQuantity": 0,
          "locked": true,
          "currencyIcon": null,
          "currencyName": "Unavailable",
          "name": "+40% Ghost Booster",
          "description": "Add 40% of the Best Damage per Hit in a Ground Battle to your Damage for 5 minutes",
          "discountClass": "green",
          "itemPurchaseLimit": 0,
          "soldOut": false
        }
      ]
    }
  }
}
```

</details>

### Notes

- **Gold Items Section:**
  - Standard shop items available to all players
  - Purchasable with Gold or Loyalty Points (some items offer both options)
  - Includes: Fuel, Energy Bars, Speed Boosters, Bazooka Parts, Storage upgrades
  - `itemPurchaseLimit`: Remaining quantity available for purchase
  - `initialPurchaseLimit`: Original purchase limit before any purchases
  - Some items have dynamic pricing (e.g., Fuel price increases with each purchase)

- **VIP Shop Section:**
  - Special discounted items unlocked progressively by VIP level (0-13)
  - `vipLevel`: Minimum VIP level required to purchase the item
  - `locked`: `true` for items above the citizen's current VIP level
  - `soldOut`: `true` when purchase limit is exhausted
  - `discount`: Percentage discount applied (affects `discountClass` color)

- **Currencies:**
  - `gold`: Standard Gold currency
  - `food_raw`, `weapon_raw`, `aircraft_raw`, `house_raw`: Raw materials
  - `currency`: Country currency (CC)
  - `loyalty_points`: Loyalty Points (alternative to Gold for some items)
  - `unavailable`: Item is locked (insufficient VIP level)

- **Discount Classes:**
  - `green`: 5-20% discount
  - `blue`: 25-40% discount
  - `purple`: 45-60% discount
  - `orange`: 65-80% discount
  - `red`: 85%+ discount

- **Item Types:**
  - `speed_booster_X`: Damage accelerators (x2, x5, x9 faster hitting)
  - `damage_booster_X`: Damage increase boosters (+50%, +100%, +150%)
  - `energy_bars`: Energy restoration items (+100, +200 energy)
  - `storage`: Storage capacity expansion
  - `fuel`: Weekly deployment limit increase
  - `catchup_booster`: Ghost boosters (% of best damage)
  - `deploy_size_booster`: Deployment size increase
  - `prestige_points`: Prestige increase
  - Various other specialized items (reset tokens, discharge documents, etc.)

- VIP levels range from 0 (non-VIP) to 13 (maximum VIP tier)
- Items often have limited daily/weekly purchase quantities
- Raw material prices make high-tier items accessible without spending Gold

- **Pack Promotions:**
  - Special pack offers (Tycoon Pack, Hero Packs, Combat Packs, Seasonal Packs) appear in `ErpkShop` variable on the homepage
  - `ErpkShop = {"promotions":[],"popupPromotions":[],"battlePromotions":[],"numberOfPurchasablePacks":0,"specialSalesEvent":false}`
  - Packs are real-money purchases (EUR/USD/Game Tokens), not available via this JSON endpoint
  - See [[Packs]] for pack mechanics (Tycoon Pack: 20g + 150 tickets + 20% productivity for 7 days)
  - Promotions shown as banners on homepage, purchased via external payment providers
