# Employees

[API docs](../README.md) › Economy › Employees

The **Manage employees** screen: attendance and salaries of your employees, your job offer and your salary budget. Visible to the employer only. Assigning employee works to companies happens in [Work as Manager](work.md#work-as-manager).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Employees page](#employees-page) | GET | `/en/economy/manage-employees/{page}` | ✅ Verified 2026-11-27 |
| [Employee data](#employee-data) | GET | `/en/economy/manage-employees-json` | ✅ Verified 2026-11-27 |
| [Job offer and staff actions](#job-offer-and-staff-actions) | POST | `/en/economy/manageJobOffer` | 🔍 From code |
| [Unused staff URL](#unused-staff-url) | ? | `/en/economy/manageEmployees` | ❔ Unconfirmed |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Employees page

`GET /en/economy/manage-employees/{page}` · Auth required · ✅ Verified 2026-11-27

HTML page (`ErpkManageEmployeesController`); the menu links to page `1`. It sets `SERVER_DATA.manageEmployees` with `manageEmployeesURL`, `jobOfferURL`, `canHaveEmployees`, `isAdult` and `isOrganization`.

**Source:** real request, 2026-11-27.

---

## Employee data

`GET /en/economy/manage-employees-json` · Auth required · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/economy/manage-employees-json' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened to one employee and one day (sample numbers):

```json
{
  "settings": { "currency": "LTL", "dayNumbers": [6635, 6636, 6637, 6638, 6639, 6640, 6641], "daysToShow": 7 },
  "employees": [
    {
      "citizenId": 1234567, "name": "EmployeeName", "avatar": "https://cdnt.erepublik.net/…",
      "salary": 1000, "hiredOn": 1000,
      "workHistory": [1, 1, 2, 1, 1, 1, 1],
      "salaryHistory": [1000, 1000, 2000, 1000, 1000, 1000, 1000],
      "totalWorks": 8, "totalSalary": 8000
    }
  ],
  "overview": {
    "jobOffer": {
      "hasJobOffer": true,
      "currentOffer": { "citizenId": 1000001, "countryId": 72, "numberOfJobs": 10, "salary": 1000, "createdAt": 1700000000 },
      "minimumSalary": 1,
      "maxNumberOfJobs": 100
    },
    "salaryLimit": 100000,
    "totalSalaries": { "amount": 1000, "isCritical": false },
    "numEmployees": 1,
    "maxEmployees": 101,
    "employeePresence": 100,
    "employeeStats": {
      "dailyStats": {
        "6635": { "employeePresence": 100, "numEmployees": 1, "presentEmployees": 1, "worksReceived": 1, "payedSalaries": 1000, "averageSalaryPerWork": 1000, "averageWorksPerEmployee": 1 }
      },
      "overall": { "employeePresence": 100, "worksReceived": 8, "payedSalaries": 8000, "averageSalaryPerWork": 1000, "averageWorksPerEmployee": 1.14 }
    }
  }
}
```

| Field | Type | Description |
|-------|------|-------------|
| `settings.currency` | string | Salary currency |
| `settings.dayNumbers` | array | Game days covered, oldest first |
| `settings.daysToShow` | number | Number of days (7) |
| `employees[].citizenId`, `name`, `avatar` | | The employee |
| `employees[].salary` | number | Salary per work |
| `employees[].hiredOn` | number | Game day hired |
| `employees[].workHistory` | array | Works per day, in `dayNumbers` order. The client shows `1` or more as worked, `-1` as not worked and any other value as no information. Earlier notes: works above 1 come from overtime |
| `employees[].salaryHistory` | array | Salary paid per day, in the same order. Earlier notes: gross, including overtime payments |
| `employees[].totalWorks`, `totalSalary` | number | Totals for the period |
| `overview.jobOffer.hasJobOffer` | boolean | An offer is published |
| `overview.jobOffer.currentOffer` | object | `citizenId`, `countryId`, `numberOfJobs`, `salary`, `createdAt` (Unix) |
| `overview.jobOffer.minimumSalary`, `maxNumberOfJobs` | number | Limits for a new offer |
| `overview.salaryLimit` | number | Your salary budget |
| `overview.totalSalaries` | object | `amount` per day, `isCritical` |
| `overview.numEmployees`, `maxEmployees` | number | Staff and capacity |
| `overview.employeePresence` | number | Attendance, percent |
| `overview.employeeStats.dailyStats.{day}` | object | `employeePresence`, `numEmployees`, `presentEmployees`, `worksReceived`, `payedSalaries`, `averageSalaryPerWork`, `averageWorksPerEmployee` |
| `overview.employeeStats.overall` | object | The same totals for the period |

**Source:** real request, 2026-11-27; `getWorkingDayInfo` in `erpk_angular_controllers.js` (version 1795779904); field notes from earlier documentation.

---

## Job offer and staff actions

`POST /en/economy/manageJobOffer` · Auth required · 🔍 From code

> [!WARNING]
> Publishes or removes your job offer, changes salaries or the budget, or fires employees.

One URL; `action` selects the operation. Every call also sends `_token` ([CSRF token](../guides/requests.md#csrf-token)).

| `action` | Other parameters | Effect | Client reads |
|----------|------------------|--------|--------------|
| `addJobOffer` | `numberOfJobs`, `salary` | Publishes or edits the offer | — |
| `removeJobOffer` | — | Removes the offer | — |
| `setSalaryLimit` | `salaryLimit` | Sets the budget | `salaryLimit` |
| `updateSalaries` | `newSalaries[N][employeeId]`, `newSalaries[N][salary]` | Changes salaries | `totalSalaries` |
| `fire` | `employeeIds` (comma-separated) | Fires employees | — |

```bash
curl 'https://www.erepublik.com/en/economy/manageJobOffer' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw 'action=addJobOffer&numberOfJobs=10&salary=1000&_token=YOUR_CSRF_TOKEN'
```

A truthy `error` means failure; the client shows `message`.

**Source:** `ErpkManageEmployeesController` and `ErpkManageEmployeesFactory` in `erpk_angular_controllers.js` (version 1795779904).

---

## Unused staff URL

`/en/economy/manageEmployees` · ❔ Unconfirmed

The page publishes it as `SERVER_DATA.manageEmployees.manageEmployeesURL`, and the factory has a method that would `POST` to it, but no call to that method was found.

**Source:** `ErpkManageEmployeesFactory.manageEmployees` in `erpk_angular_controllers.js` (version 1795779904).
