# Job Market

#erepublik #api #economy #jobs

[< Back to Economy Overview](README.md)

Job market browsing, applying for jobs, resigning, and managing employees in your companies.

**Related:** [Work](work.md) | [Companies](companies.md)

---

## Get Job Market Listings

**Method:** GET
**URL:** `/en/economy/job-market-json/{countryId}/{page}/{sortOrder}`
**Auth Required:** Yes

### Description

Retrieves paginated job market listings for a specific country, showing available job offers sorted by salary. Returns information about the citizen's current employment status, available jobs from employers, salary details (gross/net), and whether the citizen is eligible to apply.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryId | number | Yes | Country ID where jobs are located (e.g., 72 for Lithuania) |
| page | number | Yes | Page number for pagination (starts at 1) |
| sortOrder | string | Yes | Sort order: `asc` (lowest salary first) or `desc` (highest salary first) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Accept | `application/json, text/plain, */*` | Recommended |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/job-market-json/72/1/desc' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "isEmployed": true,
  "isOrganization": false,
  "isFromThisCountry": true,
  "citizenId": 4690052,
  "currencyName": "LTL",
  "employer": {
    "id": 2580095,
    "name": "Greenday_1989",
    "salary": 7500,
    "netSalary": 7425,
    "message": "You already work for <a href='//www.erepublik.com/en/citizen/profile/2580095'>Greenday_1989</a>. To apply for this job you have to quit your current job."
  },
  "jobs": [
    {
      "citizen": {
        "id": 1460339,
        "name": "pogonici",
        "img": "https://cdnt.erepublik.net/.../3f672edbae92cd310dd474294414e0cf.jpg",
        "countryId": 24
      },
      "companyName": "Love is in the air 33",
      "salary": 7111,
      "netSalary": 7039.89,
      "salaryLimit": 0,
      "currency": "LTL"
    },
    {
      "citizen": {
        "id": 2629153,
        "name": "Bob_Marley_BG",
        "img": "https://cdnt.erepublik.net/.../d7d99181731a49635cadd3a94abb9aa1.jpg",
        "countryId": 42
      },
      "companyName": "Riding High",
      "salary": 7040,
      "netSalary": 6969.6,
      "salaryLimit": 0,
      "currency": "LTL"
    }
  ],
  "pages": 20
}
```

</details>

### Notes

- **Employment Status:**
  - `isEmployed`: `true` if citizen is currently employed
  - `isOrganization`: `true` if the citizen account is an organization (not a regular citizen)
  - `isFromThisCountry`: `true` if citizen has citizenship in the job market's country
  - `citizenId`: Authenticated citizen's ID
  - `currencyName`: Country's currency code (LTL, USD, EUR, RON, etc.)

- **Current Employer:**
  - Only present if `isEmployed: true`
  - `employer.id`: Current employer's citizen ID
  - `employer.name`: Current employer's name
  - `employer.salary`: Gross salary at current job
  - `employer.netSalary`: Net salary after tax deduction
  - `employer.message`: HTML message indicating employment status (citizen must quit current job to apply)

- **Job Listings:**
  - Array of available job offers from employers
  - `citizen`: Employer's profile information (ID, name, avatar, country)
  - `companyName`: Name of the company offering the job
  - `salary`: Gross salary offered (before tax)
  - `netSalary`: Net salary after tax deduction (actual amount received)
  - `salaryLimit`: Maximum salary budget restriction (0 = no limit)
  - `currency`: Currency code for salary payment

- **Pagination:**
  - `pages`: Total number of pages available
  - Typically 10 jobs per page
  - Use `page` parameter to navigate through results

- **Sorting:**
  - `desc`: Highest salary first (most common, shows best-paying jobs)
  - `asc`: Lowest salary first (rarely used)

- **Tax Calculations:**
  - Tax rate varies by country (typically 1% in most countries)
  - `netSalary = salary - (salary x taxRate)`
  - Example: 7111 salary with 1% tax = 7039.89 net salary

- **Country IDs (Common):**
  - 24: Argentina
  - 42: Bulgaria
  - 72: Lithuania
  - 79: Romania
  - (See other endpoints for more country IDs)

- **Application Flow:**
  - If employed, citizen must resign first via `POST /en/economy/resign`
  - After resigning, apply via `POST /en/economy/job-market-apply` with `citizenId` and `salary` from the listing
  - Application is instant (no approval process)

- **Related Endpoints:**
  - Use `/en/main/job-data` to get current employment details -- see [work.md](work.md)
  - Use `/en/economy/work` to work at current job -- see [work.md](work.md)
  - Use `/en/economy/resign` to quit current job (below)
  - Use `/en/economy/job-market-apply` to apply for a listed job (below)

- Response time is typically fast (<100ms) since data is cached and indexed
- Job listings update in real-time as employers create or remove offers
- Only shows jobs in companies that have available positions and sufficient funds

---

## Resign from Job

**Method:** POST
**URL:** `/en/economy/resign`
**Auth Required:** Yes

### Description

Resigns from the current employer. After resigning, the citizen becomes unemployed and can apply for a new job on the job market. This action is irreversible -- the work streak (days in a row) is reset to zero, affecting progress toward the Hard Worker medal (awarded every 30 consecutive work days).

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action_type` | string | Yes | Must be `resign` |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/resign' \
  -X POST \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&action_type=resign'
```

</details>

<details>
<summary>Example Response</summary>

**Success:**

```json
{
  "status": true,
  "message": true,
  "error": false
}
```

**Already unemployed (expected):**

```json
{
  "status": false,
  "message": "You are not employed."
}
```

</details>

### Notes

- Citizen must be currently employed for this to succeed
- Resets the consecutive work day streak to zero
- After resigning, use `/en/economy/job-market-json/{countryId}/{page}/{sortOrder}` to browse new jobs
- After resigning, use `/en/economy/job-market-apply` to apply for a new position
- The game UI shows a confirmation dialog before resigning -- API calls bypass this

### Related Endpoints

- `GET /en/main/job-data` -- Check current employment status before resigning -- see [work.md](work.md)
- `GET /en/economy/job-market-json/{countryId}/{page}/{sortOrder}` -- Browse available jobs (above)
- `POST /en/economy/job-market-apply` -- Apply for a new job after resigning (below)

---

## Apply for Job

**Method:** POST
**URL:** `/en/economy/job-market-apply`
**Auth Required:** Yes

### Description

Applies for a job from the job market. The citizen must be unemployed (resigned from any previous job). Requires the employer's citizen ID and the exact salary amount from the job listing. Application is instant -- there is no approval process.

### Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `citizenId` | number | Yes | Employer's citizen ID (from job market listing `jobs[].citizen.id`) |
| `salary` | number | Yes | Exact gross salary from the listing (from `jobs[].salary`). Must match an active offer |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | Yes |
| X-Requested-With | `XMLHttpRequest` | Yes |
| Content-Type | `application/x-www-form-urlencoded` | Yes |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/economy/job-market-apply' \
  -X POST \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d '_token=CSRF_TOKEN&citizenId=9699126&salary=7902.03'
```

</details>

<details>
<summary>Example Response</summary>

**Success:**

```json
{
  "message": "Congratulation, you are now working for <a href='/en/citizen/profile/9699126'>Jozsika90</a>."
}
```

**Still employed (must resign first):**

```json
{
  "message": "You already have a job. You must resign first."
}
```

</details>

### Notes

- The `salary` parameter must exactly match an active job offer -- you cannot request an arbitrary salary
- The `citizenId` is the **employer's** citizen ID, not your own
- Application is instant: the response confirms employment immediately
- The success `message` contains HTML with a link to the employer's profile
- If the offer has been taken or removed between fetching the listing and applying, the request will fail
- The `salaryLimit` from the job listing indicates the employer's total salary budget -- if the budget is exhausted, the offer may no longer be available
- A `salaryLimit` of `0` means the employer has no salary cap (unlimited)

### Typical Workflow

```
1. GET  /en/economy/job-market-json/35/1/desc   -> Browse offers (page 1, highest first)
2. POST /en/economy/resign                       -> Quit current job (if employed)
3. POST /en/economy/job-market-apply              -> Apply for chosen offer
4. GET  /en/main/job-data                         -> Verify new employment
```

### Related Endpoints

- `GET /en/economy/job-market-json/{countryId}/{page}/{sortOrder}` -- Browse available jobs to get `citizenId` and `salary` values (above)
- `POST /en/economy/resign` -- Resign from current job (required before applying) (above)
- `GET /en/main/job-data` -- Verify employment status after applying -- see [work.md](work.md)
- `POST /en/economy/work` -- Work at the new employer -- see [work.md](work.md)

---

## Manage Employees Data

**Method:** GET
**URL:** `/en/economy/manage-employees-json`
**Auth Required:** Yes

### Description

Retrieves comprehensive employee management data for the authenticated citizen's companies, including individual employee work histories, salary details, job offer information, and daily statistics. This endpoint provides a complete overview of employee performance, attendance, and company salary budget management.

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
curl 'https://www.erepublik.com/en/economy/manage-employees-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  -H 'Accept: application/json, text/plain, */*'
```

</details>

<details>
<summary>Example Response</summary>

```json
{
  "settings": {
    "currency": "LTL",
    "dayNumbers": [6635, 6636, 6637, 6638, 6639, 6640, 6641],
    "daysToShow": 7
  },
  "employees": [
    {
      "citizenId": 5385502,
      "name": "SP rider",
      "avatar": "https://cdnt.erepublik.net/.../de8c10b2e136a296fbe88a5f4c8671ff.jpg",
      "salary": 2100,
      "hiredOn": 5372,
      "workHistory": [1, 1, 2, 1, 1, 1, 1],
      "salaryHistory": [2100, 2100, 4200, 2100, 2100, 2100, 2100],
      "totalWorks": 8,
      "totalSalary": 16800
    }
  ],
  "overview": {
    "jobOffer": {
      "hasJobOffer": true,
      "currentOffer": {
        "citizenId": 4690052,
        "countryId": 72,
        "numberOfJobs": 100,
        "salary": 2100,
        "createdAt": 1745836141
      },
      "minimumSalary": 1,
      "maxNumberOfJobs": 1335
    },
    "salaryLimit": 210000,
    "totalSalaries": {
      "amount": 2100,
      "isCritical": false
    },
    "numEmployees": 1,
    "maxEmployees": 1336,
    "employeePresence": 100,
    "employeeStats": {
      "dailyStats": {
        "6635": {
          "employeePresence": 100,
          "numEmployees": 1,
          "presentEmployees": 1,
          "worksReceived": 1,
          "payedSalaries": 2100,
          "averageSalaryPerWork": 2100,
          "averageWorksPerEmployee": 1
        },
        "6636": {
          "employeePresence": 100,
          "numEmployees": 1,
          "presentEmployees": 1,
          "worksReceived": 1,
          "payedSalaries": 2100,
          "averageSalaryPerWork": 2100,
          "averageWorksPerEmployee": 1
        }
      },
      "overall": {
        "employeePresence": 100,
        "worksReceived": 8,
        "payedSalaries": 16800,
        "averageSalaryPerWork": 2100,
        "averageWorksPerEmployee": 1.14
      }
    }
  }
}
```

</details>

### Notes

- **Settings:**
  - `currency`: Currency code used for salaries (LTL, USD, EUR, etc.)
  - `dayNumbers`: Array of eRepublik day numbers (eDay) for the displayed period
  - `daysToShow`: Number of days shown in history (typically 7 days)

- **Employee Data:**
  - `citizenId`: Unique identifier of the employee
  - `name`: Employee's display name
  - `avatar`: Profile picture URL
  - `salary`: Current daily salary offered
  - `hiredOn`: eDay number when employee was hired
  - `workHistory`: Array showing number of works per day (corresponds to `dayNumbers` array)
  - `salaryHistory`: Array showing total salary paid per day (corresponds to `dayNumbers` array)
  - `totalWorks`: Total works received from this employee during the displayed period
  - `totalSalary`: Total salary paid to this employee during the displayed period

- **Job Offer Information:**
  - `hasJobOffer`: `true` if an active job offer exists
  - `currentOffer.citizenId`: Company owner's citizen ID
  - `currentOffer.countryId`: Country where the job offer is posted
  - `currentOffer.numberOfJobs`: Number of available positions
  - `currentOffer.salary`: Offered salary per work
  - `currentOffer.createdAt`: Unix timestamp when offer was created
  - `minimumSalary`: Minimum allowed salary (typically 1)
  - `maxNumberOfJobs`: Maximum employees allowed (based on company level/holding capacity)

- **Salary Budget:**
  - `salaryLimit`: Maximum daily salary budget before warnings appear
  - `totalSalaries.amount`: Total daily salary expenses
  - `totalSalaries.isCritical`: `true` if salary expenses exceed safe thresholds

- **Employee Capacity:**
  - `numEmployees`: Current number of employees
  - `maxEmployees`: Maximum employee capacity (based on company buildings)
  - `employeePresence`: Overall attendance percentage (0-100)

- **Daily Statistics:**
  - `dailyStats`: Object keyed by eDay number with per-day metrics
  - `employeePresence`: Percentage of employees who worked that day
  - `presentEmployees`: Count of employees who worked
  - `worksReceived`: Total works completed that day (can exceed employee count due to overtime)
  - `payedSalaries`: Total salaries paid that day
  - `averageSalaryPerWork`: Average salary paid per work completed
  - `averageWorksPerEmployee`: Average works per employee (includes overtime)

- **Overall Statistics:**
  - `overall`: Aggregated stats across the entire displayed period
  - Includes total works, total salaries, and averages

- **Work History Arrays:**
  - Arrays are ordered chronologically matching the `dayNumbers` array
  - Example: `dayNumbers[0]=6635` corresponds to `workHistory[0]=1` (1 work on day 6635)
  - Works can exceed 1 per day if employee worked overtime
  - Salary history shows gross salary paid (includes overtime payments)

- **Use Cases:**
  - Monitor employee attendance and productivity
  - Track salary expenses across time
  - Identify inactive or low-performing employees
  - Manage job offer settings and capacity
  - Calculate average costs and efficiency metrics

- This endpoint is used by the "Manage Employees" page in the Economy section
- Response time is typically fast (<100ms)
- Data covers the last 7 days by default
- Employee data is only visible to the company owner
