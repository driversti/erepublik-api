# Work

[API docs](../README.md) › Economy › Work

The daily job at an employer, overtime, and Work as Manager (WAM) in your own [companies](companies.md). The buttons are on **My Places** (`/en/economy/myCompanies`) and in the dashboard job widget. Each of the daily actions resets with the game day.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Job data](#job-data) | GET | `/en/main/job-data` | ✅ Verified 2026-11-27 |
| [Work for your employer](#work-for-your-employer) | POST | `/en/economy/work` | 🔍 From code |
| [Work as Manager](#work-as-manager) | POST | `/en/economy/work` | 🔍 From code |
| [Work overtime](#work-overtime) | POST | `/en/economy/workOvertime` | 🔍 From code |
| [Submit a captcha](#submit-a-captcha) | POST | `/en/economy/recaptchaAjax` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Leaving a job: [Resign](job-market.md#resign).

---

## Job data

`GET /en/main/job-data` · Auth required · ✅ Verified 2026-11-27

Employer, salary, whether you worked today, and overtime state.

```bash
curl 'https://www.erepublik.com/en/main/job-data' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

```json
{
  "isEmployee": true,
  "employer": { "id": 1234567, "name": "EmployerName", "img": "https://cdnt.erepublik.net/…" },
  "salary": 10000,
  "tax": 100,
  "currency": "LTL",
  "alreadyWorked": true,
  "playerLevel": 100,
  "overTime": { "points": 48, "nextOverTime": 1700000000, "salary": 20000, "usableEnergy": 800 }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `isEmployee` | boolean | Whether you have a job |
| `employer` | object | `id` (citizen ID), `name`, `img` |
| `salary` | number | Gross salary |
| `tax` | number | Income tax deducted from the salary |
| `currency` | string | Currency code |
| `alreadyWorked` | boolean | Worked today |
| `playerLevel` | number | Your level |
| `overTime.points` | number | Overtime points collected |
| `overTime.nextOverTime` | number | Unix time when the overtime cooldown ends |
| `overTime.salary` | number | Overtime salary (earlier notes: typically double the salary) |
| `overTime.usableEnergy` | number | Energy available for overtime |

The client draws the overtime button red when `points < 24`, when the cooldown is running and `usableEnergy < 100`, or when `usableEnergy < 10`. With the cooldown running and at least 100 usable energy (and enough points) it is orange, otherwise green. This suggests one overtime shift needs 24 points.

**Source:** real request, 2026-11-27; `ErpkMyJobController` in `erpk_angular_controllers.js` (version 1795779904).

---

## Work for your employer

`POST /en/economy/work` · Auth required · 🔍 From code

> [!WARNING]
> Performs today's work on your account and spends energy.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `action_type` | string | Yes | `work` |
| `useEnergyBar` | string | No | `yes` to eat energy bars when energy is short |

```bash
curl 'https://www.erepublik.com/en/economy/work' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=work'
```

### Response

On success `status` and `message` are both `true`, and `result` has the fields of the [overtime result](#overtime-result). Earlier notes, already worked:

```json
{ "status": false, "message": "already_worked", "result": "" }
```

The client spends 10 energy as the cost estimate for this action. Messages it handles are in [Result messages](#result-messages).

**Source:** `erpk_angular_controllers.js` (version 1795779904), `ErpkMyJobController.startWork`; example from earlier notes.

---

## Work as Manager

`POST /en/economy/work` · Auth required · 🔍 From code

> [!WARNING]
> Produces in the selected companies: spends energy, raw materials and the work tax. Once per company per day.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action_type` | string | Yes | `production` |
| `own_work` | string | Yes | JSON array of [company IDs](companies.md#my-companies-page) to work in yourself, e.g. `[111111,222222]` |
| `employee_works` | string | Yes | JSON object `{companyId: works}` for employee works; `{}` for none |
| `cntOwnWork` | number | Yes | Length of `own_work` |
| `cntEmployeeWork` | number | Yes | Number of keys in `employee_works` |
| `cntSelected` | number | Yes | Sum of the two counts; the client does not send the request when it is `0` |
| `useEnergyBar` | string | No | `yes` to eat energy bars |

The client always sends all counts. `employee_works` values are the **additional** works: the selected number minus `todays_works`.

```bash
curl 'https://www.erepublik.com/en/economy/work' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-urlencode 'own_work=[111111,222222]' \
  --data-urlencode 'employee_works={}' \
  --data-raw 'cntOwnWork=2&cntEmployeeWork=0&cntSelected=2&action_type=production&_token=YOUR_CSRF_TOKEN'
```

### Response

Shortened (earlier notes):

```json
{
  "status": true,
  "message": true,
  "result": {
    "production": { "weaponRaw": 119.85, "weapon": { "7": 441.6 }, "totalWeapon": 441 },
    "resources": { "1": { "name": "Grain", "bonus": 25 } },
    "xp": 4,
    "health": 20,
    "tax": 100
  }
}
```

<details>
<summary>Full example response (earlier notes, sample numbers)</summary>

```json
{
  "status": true,
  "message": true,
  "result": {
    "production": {
      "foodRaw": 0, "weaponRaw": 119.85, "houseRaw": 0, "airplaneRaw": 0,
      "food": [], "weapon": { "7": 441.5999999999998 }, "house": [], "airplane": [],
      "totalFood": 0, "totalWeapon": 441, "totalHouse": 0, "totalAirplane": 0
    },
    "resources": {
      "1": { "name": "Grain", "bonus": 25 }, "2": { "name": "Fish", "bonus": 10 },
      "3": { "name": "Fruits", "bonus": 15 }, "4": { "name": "Cattle", "bonus": 20 },
      "5": { "name": "Deer", "bonus": 30 }, "6": { "name": "Iron", "bonus": 10 },
      "7": { "name": "Saltpeter", "bonus": 0 }, "8": { "name": "Aluminum", "bonus": 15 },
      "9": { "name": "Oil", "bonus": 20 }, "10": { "name": "Rubber", "bonus": 0 },
      "11": { "name": "Sand", "bonus": 5 }, "12": { "name": "Clay", "bonus": 0 },
      "13": { "name": "Wood", "bonus": 7.5 }, "14": { "name": "Limestone", "bonus": 12.5 },
      "15": { "name": "Granite", "bonus": 0 }, "16": { "name": "Neodymium", "bonus": 30 },
      "17": { "name": "Magnesium", "bonus": 0 }, "18": { "name": "Cobalt", "bonus": 25 },
      "19": { "name": "Titanium", "bonus": 0 }, "20": { "name": "Wolfram", "bonus": 0 }
    },
    "days_in_a_row": 100,
    "to_achievment": 20,
    "to_achievment_text": "You have <span id=\"production_to_achievment\">20 days</span> left to work to get the Hardworker medal.",
    "xp": 4,
    "health": 20,
    "works": 0,
    "consumed_summary": [],
    "tax": 100
  }
}
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `result.production.*Raw` | number | Raw materials: `foodRaw`, `weaponRaw`, `houseRaw`, `airplaneRaw`. Earlier notes call `weaponRaw` the raw value before bonuses |
| `result.production.food`, `weapon`, `house`, `airplane` | object \| array | Quality → units; `[]` when empty |
| `result.production.total*` | number | Whole units per industry |
| `result.resources` | object | Region resource bonuses in percent. IDs 1–5 food raws, 6–10 weapon, 11–15 house, 16–20 aircraft |
| `result.days_in_a_row` | number | Work streak |
| `result.to_achievment` | number | Days to the next Hard Worker medal (spelled so by the game) |
| `result.xp`, `result.health` | number | Experience gained; energy spent |
| `result.tax` | number | Work tax in local currency |

Earlier notes: the final production includes the region resource bonuses, and overtime is typically available after the regular daily work.

**Source:** `citizen/myCompanies.js` (version 1760600930), `generateParams` and `produce`; example from earlier notes.

---

## Work overtime

`POST /en/economy/workOvertime` · Auth required · 🔍 From code

> [!WARNING]
> Spends overtime points and energy: 100 energy while the cooldown is running, otherwise 10.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action_type` | string | Yes | `workOvertime` |
| `useEnergyBar` | string | No | `yes` to eat energy bars first; without it the request fails when energy is short |

```bash
curl 'https://www.erepublik.com/en/economy/workOvertime' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=workOvertime'
```

The cooldown is one hour: the client charges 100 energy when `overTime.nextOverTime` is still in the future.

### Overtime result

Earlier notes, with energy bars used:

```json
{
  "status": true,
  "message": true,
  "result": {
    "netSalary": 9900,
    "grossSalary": 10000,
    "tax": 100,
    "currency": "LTL",
    "days_in_a_row": 100,
    "to_achievment": 20,
    "to_achievment_text": "You have <span id=\"production_to_achievment\">20 days</span> left to work to get the Hardworker medal.",
    "xp": 2,
    "health": 100,
    "consumedSummary": { "13": 2 },
    "daily_tasks_done": false
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `netSalary`, `grossSalary`, `tax` | number | Salary after tax, before tax, and the tax |
| `xp` | number | Experience gained |
| `health` | number | Energy; the client shows it as spent. Earlier notes call it restored |
| `consumedSummary` | object \| array | Item → quantity eaten; `[]` when nothing. Earlier notes: key `13` means energy bars |
| `daily_tasks_done` | boolean | Daily tasks finished |

**Source:** `erpk_angular_controllers.js` (version 1795779904), `startWorkOvertime`; examples from earlier notes.

---

## Result messages

When `message` is not `true`, the client handles these values:

| `message` | Meaning |
|-----------|---------|
| `redirect`, `processing` | The client reloads the page |
| `lock` | A request is already running; ignored |
| `captcha` | A captcha is needed, see [below](#submit-a-captcha) |
| `money` | Employer cannot pay, or not enough currency |
| `tax_money` | Not enough currency for the work tax |
| `not_enough_health`, `not_enough_health_food` | Not enough energy or food |
| `not_enough_food_raw`, `not_enough_weapon_raw`, `not_enough_house_raw`, `not_enough_airplane_raw`, `not_enough_raw` | Not enough raw materials |
| `not_enough_storage` | Storage is full |
| `not_enough_works`, `work_limit` | Work limits |
| `nothing_selected` | Raised by the client itself when no company is selected |

---

## Submit a captcha

`POST /en/economy/recaptchaAjax` · Auth required · 🔍 From code

> [!WARNING]
> Performs the work or [training](training.md#train) action named in `action_type`.

Used instead of the normal endpoint when the page variable `has_captcha` is set or the server answers `captcha`. See also [session verification](../basics/captcha.md).

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action_type` | string | Yes | `work`, `workOvertime`, `production`, `workInGoldMines` or `train` |
| `g-recaptcha-response` | string | Yes | Captcha solution |
| other | — | — | The parameters of the original action; `useEnergyBar` if chosen |

`status: false` with `message: false` means the captcha was rejected. A successful answer has the format of the original action.

`workInGoldMines` has its own result popup in the script; no button or URL for it was seen on the page.

**Source:** `citizen/myCompanies.js` (version 1760600930), `submitCaptcha`; `ErpkTrainingGroundController` (version 1795779904).

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| `useEnergyBar` | Overtime only | Sent by work, production and training too | Code |
| `health` in results | Energy restored | Shown as `-N` | Code |
| Work response | Only the `already_worked` error | `already_worked` is not handled by name | Both listed |
| Production counts | Optional except `cntSelected` | Client always sends them | Code |
