# Boosters

#erepublik #api #economy #boosters

[< Back to Economy Overview](README.md)

Activating boosters and houses from the inventory for temporary combat and economy enhancements.

**Related:** [Inventory](inventory.md) | [Shop](shop.md)

---

## Activate Booster

**Method:** POST
**URL:** `/en/main/activateBooster`
**Auth Required:** Yes

### Description

Activates a booster from the inventory. Boosters provide temporary combat enhancements such as increased damage, faster hitting speed, or other bonuses. If another booster of the same type is already active, the endpoint returns a confirmation prompt asking whether to replace the existing booster.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| type | string | Yes | Booster type: `speed` (Damage Accelerator), `damage` (Damage Booster), etc. |
| quality | number | Yes | Booster quality level (determines strength: 1=x2, 2=x5, etc. for speed; 10=+100%, 15=+150%, etc. for damage) |
| duration | number | Yes | Duration in seconds (e.g., 180 = 3 minutes, 300 = 5 minutes, 7200 = 2 hours) |
| amount | number | Yes | Quantity to activate (typically 1) |
| unit | string | No | Time unit for display: `m` (minutes), `h` (hours) |
| value | number | No | Display value for duration (e.g., 5 for "5 minutes") |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/main/activateBooster' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'type=speed&quality=2&duration=300&amount=1&unit=m&value=5&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response (Confirmation Required)</summary>

```json
{
  "error": true,
  "status": "error",
  "message": "Please confirm activating this booster",
  "confirmationRequired": true,
  "confirmationText": "You already have another active item of this type.<br><br>\n Are you sure you want to activate this item?"
}
```

</details>

<details>
<summary>Example Response (Successful Activation)</summary>

```json
{
  "status": "success",
  "message": "Booster activated"
}
```

</details>

### Notes

- **Booster Types:**
  - `speed`: Damage Accelerator (x2, x5, x9 faster hitting)
  - `damage`: Damage Booster (+50%, +100%, +150% damage)
  - `catchup`: Ghost Booster (% of best damage in battle)
  - `deploy_size`: Deployment Size Booster
  - `prestige`: Prestige Points Booster
  - Additional types may exist for other booster categories

- **Quality Levels:**
  - Speed Boosters: 1=x2, 2=x5, 3=x9
  - Damage Boosters: 10=+100%, 15=+150%, 5=+50%
  - Quality determines the strength of the boost

- **Duration:**
  - Short boosters: 60s (1 min), 180s (3 min), 300s (5 min)
  - Medium boosters: 900s (15 min), 1800s (30 min), 3600s (1 hour)
  - Long boosters: 7200s (2 hours), 14400s (4 hours), 86400s (24 hours)

- **Confirmation Logic:**
  - If a booster of the same type is already active, `confirmationRequired: true` is returned
  - Client must prompt user to confirm replacement
  - To proceed after confirmation, resubmit the request (implementation may require additional parameter)
  - Activating replaces the existing booster (remaining time is lost)

- **Booster Stacking:**
  - Boosters of different types can be active simultaneously (e.g., speed + damage)
  - Boosters of the same type cannot stack (activating replaces the active one)
  - Multiple boosters in inventory can be activated sequentially after previous ones expire

- **Inventory Management:**
  - Activated boosters are consumed from inventory (`amount` is deducted)
  - Remaining quantity is updated in the inventory
  - Boosters can be obtained from the Shop, VIP Shop, events, or rewards

- **Expiration:**
  - `expiresAt`: Unix timestamp when the booster effect ends
  - After expiration, the booster effect is removed automatically
  - Citizens can activate a new booster immediately after expiration

- **Related Endpoints:**
  - Use `/en/economy/inventory-json` to retrieve available boosters -- see [inventory.md](inventory.md)
  - Use `/en/main/shopItemsJson` to purchase new boosters from the Shop -- see [shop.md](shop.md)
  - Booster activation is typically done before entering combat or during battles

- The CSRF `_token` can be obtained from the inventory or main page source
- Response time is typically very fast (<50ms)
- This endpoint is used by the Inventory and Active Enhancements UI sections

---

## Activate House

**Method:** POST
**URL:** `/en/economy/activateHouse`
**Auth Required:** Yes

### Description

Activates a house from the inventory to gain its benefits. Houses provide energy pool bonuses and overtime points accumulation. When activated, a house consumes one unit from inventory and charges an activation tax. Only one house can be active at a time, and activating a new house replaces the currently active one (remaining time is lost).

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| type | string | Yes | Must be set to `house` |
| quality | number | Yes | House quality level (1-5): Q1 (+100 energy pool), Q2 (+200), Q3 (+300), Q4 (+400), Q5 (+500) |
| amount | number | Yes | Quantity to activate (typically 1) |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl -X POST 'https://www.erepublik.com/en/economy/activateHouse' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw 'type=house&quality=1&amount=1&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "status": true,
  "message": "Successfully activated house",
  "activationTax": 628.9,
  "currency": 6273068,
  "error": false
}
```

</details>

### Notes

- **House Benefits:**
  - **Energy Pool Bonus:** Increases maximum energy capacity
    - Q1: +100 energy pool
    - Q2: +200 energy pool
    - Q3: +300 energy pool
    - Q4: +400 energy pool
    - Q5: +500 energy pool
  - **Overtime Points:** +1 overtime point per hour while active
  - Houses remain active for a duration based on quality (typically days or weeks)

- **Activation Tax:**
  - `activationTax`: Tax amount charged for activating the house (in country currency)
  - Tax is deducted from the citizen's currency balance
  - Tax varies by country and house quality level
  - Example: 628.9 LTL for Q1 house activation

- **Currency Balance:**
  - `currency`: Updated currency balance after activation tax is deducted (in hundredths)
  - Example: `6273068` = 62,730.68 LTL
  - Insufficient currency will result in an error

- **Activation Rules:**
  - Only one house can be active at a time
  - Activating a new house replaces the currently active one
  - Remaining time on the previous house is lost (no refund)
  - The house is consumed from inventory upon activation
  - Duration varies by quality: Q1 (~7 days), Q5 (~30 days)

- **Inventory Management:**
  - Houses must be in inventory before activation
  - Can be purchased from the marketplace or Shop
  - Each activation consumes one house unit
  - Recommended to activate houses before they expire in inventory

- **Active House Status:**
  - Use `/en/economy/inventory-json` to check currently active house -- see [inventory.md](inventory.md)
  - Active houses appear in the "Active Enhancements" section
  - Shows remaining time until expiration
  - After expiration, energy pool bonus and overtime points are removed

- **Strategic Use:**
  - Activate houses before extended play sessions
  - Higher quality houses provide better energy pool and longer duration
  - Energy pool increases effective combat capacity
  - Overtime points accumulate hourly for additional work opportunities

- **Error Cases:**
  - Insufficient currency for activation tax
  - No house of specified quality in inventory
  - Invalid CSRF token

- The CSRF `_token` can be obtained from the inventory page source
- Response time is typically very fast (<50ms)
- This endpoint is used by the Inventory UI when clicking "Activate" on a house
