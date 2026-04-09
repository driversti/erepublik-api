# Work

#erepublik #api #economy #work

[< Back to Economy Overview](README.md)

Employment and daily work actions -- retrieving job data, working as an employee, overtime work, and working as a manager (WAM) in owned companies.

**Related:** [Job Market](job-market.md) | [Companies](companies.md) | [Training](training.md)

---

## Get Job Data

**Method:** GET
**URL:** `/en/main/job-data`
**Auth Required:** Yes

### Description

Retrieves the current employment and work status for the authenticated citizen, including employer information, salary details, and overtime availability.

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
curl 'https://www.erepublik.com/en/main/job-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "isEmployee": true,
  "employer": {
    "id": 2580095,
    "name": "Greenday_1989",
    "img": "https://cdnt.erepublik.net/hTXa-zXGxYdBcMT9YtgJ9BDyNUA=/142x142/smart/avatars/Citizens/2010/01/18/ef89175e7f4e51d0e3036e743cee0cb6.jpg?afd07d8d1a944e1c3867f2ea89094394"
  },
  "salary": 7500,
  "tax": 75,
  "currency": "LTL",
  "alreadyWorked": true,
  "playerLevel": 1690,
  "overTime": {
    "points": 10342,
    "nextOverTime": 1769331700,
    "salary": 15000,
    "usableEnergy": 8308
  }
}
```

</details>

### Notes

- Returns `isEmployee: true` if the citizen is currently employed
- `alreadyWorked` indicates if the citizen has already worked today
- `overTime.nextOverTime` is a Unix timestamp indicating when the next overtime work is available
- `overTime.points` shows accumulated overtime points
- `overTime.salary` is the overtime work salary (typically double the regular salary)
- Currency codes include: LTL (Lithuanian Litas), USD, GOLD, etc.
- Tax amount is deducted from the gross salary

---

## Work as Employee

**Method:** POST
**URL:** `/en/economy/work`
**Auth Required:** Yes

### Description

Work at the current employer as an employee. This endpoint performs the daily work action, earning salary and contributing to work streak achievements. Can only be executed once per day per citizen.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| action_type | string | Yes | Must be set to `work` for employee work |

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
curl -X POST 'https://www.erepublik.com/en/economy/work' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=work'
```

</details>

<details>
<summary>Example Response</summary>

**Already worked today:**
```json
{
  "status": false,
  "message": "already_worked",
  "result": ""
}
```

</details>

### Notes

- **Daily Limit:** Can only be executed once per day (resets at eRepublik day change)
- **Error Messages:**
  - `already_worked`: Citizen has already performed daily work
  - Other errors may include: not employed, employer has no funds, etc.
- **Same Endpoint as WAM:** This uses the same `/en/economy/work` endpoint but with `action_type=work` instead of `action_type=production`
- **Requirements:** Citizen must be employed to use this endpoint
- The CSRF `_token` can be obtained from the page source or previous API responses

---

## Work Overtime

**Method:** POST
**URL:** `/en/economy/workOvertime`
**Auth Required:** Yes

### Description

Performs overtime work at the citizen's current employer. This action grants additional salary, XP, and health in exchange for energy. Overtime work is typically available after regular work has been completed and has a cooldown period.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| _token | string | Yes | CSRF token for request validation |
| action_type | string | Yes | Must be set to `workOvertime` |
| useEnergyBar | string | No | Set to `yes` to auto-consume energy bars before working overtime. When omitted, overtime fails if energy is insufficient. |

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
# Basic overtime (requires sufficient energy)
curl -X POST 'https://www.erepublik.com/en/economy/workOvertime' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=workOvertime'

# With auto energy bar consumption
curl -X POST 'https://www.erepublik.com/en/economy/workOvertime' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H 'Accept: application/json, text/plain, */*' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=workOvertime&useEnergyBar=yes'
```

</details>

<details>
<summary>Example Response (without energy bar)</summary>

```json
{
  "status": true,
  "message": true,
  "result": {
    "netSalary": 7425,
    "grossSalary": 7500,
    "tax": 75,
    "currency": "LTL",
    "days_in_a_row": 3764,
    "to_achievment": 28,
    "to_achievment_text": "You have <span id=\"production_to_achievment\">28 days</span> left to work to get the Hardworker medal.",
    "xp": 2,
    "health": 100,
    "consumedSummary": [],
    "daily_tasks_done": false
  }
}
```

</details>

<details>
<summary>Example Response (with energy bar consumption)</summary>

```json
{
  "status": true,
  "message": true,
  "result": {
    "netSalary": 9900,
    "grossSalary": 10000,
    "tax": 100,
    "currency": "LTL",
    "days_in_a_row": 3837,
    "to_achievment": 15,
    "to_achievment_text": "You have <span id=\"production_to_achievment\">15 days</span> left to work to get the Hardworker medal.",
    "xp": 2,
    "health": 100,
    "consumedSummary": {
      "13": 2
    },
    "daily_tasks_done": false
  }
}
```

</details>

### Notes

- **Energy Consumption:**
  - 10 energy if the overtime cooldown has passed (>1 hour since last overtime)
  - 100 energy if attempting to work again within the 1-hour cooldown period (anti-spam mechanism)
- **`useEnergyBar` parameter:**
  - When set to `yes`, automatically consumes energy bars from inventory to replenish energy before performing overtime
  - Without this parameter, the request will fail if the citizen doesn't have enough energy
- `netSalary` is the amount received after tax deduction
- `days_in_a_row` tracks consecutive work days for achievement progress
- `to_achievment` shows remaining days needed for the Hardworker medal
- `xp` indicates experience points gained from this overtime work
- `health` shows the health points restored (typically 100)
- **`consumedSummary`:** Map of item type IDs to quantities consumed. Empty array `[]` if nothing consumed; object `{"13": 2}` when energy bars were used (key = item type ID, value = quantity consumed). Item ID `13` corresponds to energy bars.
- Currency codes include: LTL, USD, EUR, RON, etc.
- The CSRF `_token` can be obtained from the page source or previous API responses
- Overtime is typically available after completing regular daily work

---

## Work as Manager (WAM)

**Method:** POST
**URL:** `/en/economy/work`
**Auth Required:** Yes

### Description

Work as Manager (WAM) in own companies. This endpoint allows working in multiple owned companies simultaneously, producing goods based on raw materials and production bonuses. Can only be executed once per day.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| own_work | string | No | URL-encoded JSON array of company IDs to work in as owner |
| employee_works | string | No | URL-encoded JSON object for employee work assignments |
| cntOwnWork | number | No | Count of own companies selected for work |
| cntEmployeeWork | number | No | Count of employee companies selected for work |
| cntSelected | number | Yes | Total count of selected companies |
| action_type | string | Yes | Must be set to `production` |
| _token | string | Yes | CSRF token for request validation |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded; charset=UTF-8` | Yes |
| Accept | `application/json, text/javascript, */*; q=0.01` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
# Work as Manager in 2 companies (IDs: 358462, 3271053)
curl -X POST 'https://www.erepublik.com/en/economy/work' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded; charset=UTF-8' \
  -H 'Accept: application/json, text/javascript, */*; q=0.01' \
  --data-raw 'own_work=%5B358462%2C3271053%5D&employee_works=%7B%7D&cntOwnWork=2&cntEmployeeWork=0&cntSelected=2&action_type=production&_token=YOUR_CSRF_TOKEN'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "status": true,
  "message": true,
  "result": {
    "production": {
      "foodRaw": 0,
      "weaponRaw": 119.85,
      "houseRaw": 0,
      "airplaneRaw": 0,
      "food": [],
      "weapon": {
        "7": 441.5999999999998
      },
      "house": [],
      "airplane": [],
      "totalFood": 0,
      "totalWeapon": 441,
      "totalHouse": 0,
      "totalAirplane": 0
    },
    "resources": {
      "1": {
        "name": "Grain",
        "bonus": 25
      },
      "2": {
        "name": "Fish",
        "bonus": 10
      },
      "3": {
        "name": "Fruits",
        "bonus": 15
      },
      "4": {
        "name": "Cattle",
        "bonus": 20
      },
      "5": {
        "name": "Deer",
        "bonus": 30
      },
      "6": {
        "name": "Iron",
        "bonus": 10
      },
      "7": {
        "name": "Saltpeter",
        "bonus": 0
      },
      "8": {
        "name": "Aluminum",
        "bonus": 15
      },
      "9": {
        "name": "Oil",
        "bonus": 20
      },
      "10": {
        "name": "Rubber",
        "bonus": 0
      },
      "11": {
        "name": "Sand",
        "bonus": 5
      },
      "12": {
        "name": "Clay",
        "bonus": 0
      },
      "13": {
        "name": "Wood",
        "bonus": 7.5
      },
      "14": {
        "name": "Limestone",
        "bonus": 12.5
      },
      "15": {
        "name": "Granite",
        "bonus": 0
      },
      "16": {
        "name": "Neodymium",
        "bonus": 30
      },
      "17": {
        "name": "Magnesium",
        "bonus": 0
      },
      "18": {
        "name": "Cobalt",
        "bonus": 25
      },
      "19": {
        "name": "Titanium",
        "bonus": 0
      },
      "20": {
        "name": "Wolfram",
        "bonus": 0
      }
    },
    "days_in_a_row": 3764,
    "to_achievment": 28,
    "to_achievment_text": "You have <span id=\"production_to_achievment\">28 days</span> left to work to get the Hardworker medal.",
    "xp": 448,
    "health": 2240,
    "works": 0,
    "consumed_summary": [],
    "tax": 14929.8
  }
}
```

</details>

### Notes

- **Work as Manager (WAM):** This endpoint is specifically for working in companies you own as the manager
- **Daily Limit:** Can only be executed **once per day** (resets at eRepublik day change)
- **Company IDs:** Must be URL-encoded JSON array format (e.g., `[358462,3271053]` becomes `%5B358462%2C3271053%5D`)
- **Production Results:**
  - `production.weaponRaw` indicates raw production value before applying bonuses
  - `production.weapon` is a map where keys are quality levels (e.g., `"7"` for Q7 weapons)
  - Final production includes resource bonuses from territories
- **Resource Bonuses:**
  - IDs 1-5: Food raw materials (Grain, Fish, Fruits, Cattle, Deer)
  - IDs 6-10: Weapon raw materials (Iron, Saltpeter, Aluminum, Oil, Rubber)
  - IDs 11-15: House raw materials (Sand, Clay, Wood, Limestone, Granite)
  - IDs 16-20: Airplane raw materials (Neodymium, Magnesium, Cobalt, Titanium, Wolfram)
  - `bonus` represents the percentage production bonus from controlled territories
- **Work Streak:**
  - `days_in_a_row` tracks consecutive days of work for achievements
  - `to_achievment` shows remaining days needed for the Hardworker medal
- **Rewards:**
  - `xp` indicates total experience points gained
  - `health` shows total health points restored
  - `tax` is the total tax paid on production (based on country tax rates)
- The CSRF `_token` can be obtained from the page source or previous API responses
- For WAM operations, `employee_works` should be empty: `employee_works=%7B%7D` (empty JSON object)
- The `own_work` array contains all company IDs where you want to work as manager
