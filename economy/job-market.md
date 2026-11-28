# Job market and employment

[API docs](../README.md) › Economy › Job market

Browsing job offers of a country, taking a job and leaving it. Hiring people for your own companies is on [Employees](employees.md).

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Job market page](#job-market-page) | GET | `/en/economy/job-market/{countryId}` | ✅ Verified 2026-11-27 |
| [Job offers](#job-offers) | GET | `/en/economy/job-market-json/{countryId}/{page}/{sort}` | ✅ Verified 2026-11-27 |
| [Apply for a job](#apply-for-a-job) | POST | `/en/economy/job-market-apply` | 🔍 From code |
| [Resign](#resign) | POST | `/en/economy/resign` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Current employment: [Job data](work.md#job-data). Country IDs: [reference](../basics/id-tables.md).

Typical order: [offers](#job-offers) → [resign](#resign) → [apply](#apply-for-a-job) → [job data](work.md#job-data) to check → [work](work.md#work-for-your-employer).

---

## Job market page

`GET /en/economy/job-market/{countryId}` · Auth required · ✅ Verified 2026-11-27

HTML page (`JobMarketController`). It sets `jobMarketSettings = { countryId, pageNumber, sortOrder }`. The client rewrites the address to `/en/economy/job-market/{countryId}/{page}/{sort}` as you browse.

**Source:** real request, 2026-11-27.

---

## Job offers

`GET /en/economy/job-market-json/{countryId}/{page}/{sort}` · Auth required · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `countryId` | path | number | Yes | Country of the market |
| `page` | path | number | Yes | Starts at 1 |
| `sort` | path | string | Yes | `desc` (highest salary first) or `asc` |

```bash
curl 'https://www.erepublik.com/en/economy/job-market-json/72/1/desc' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened to one of ten offers:

```json
{
  "isEmployed": true,
  "isOrganization": false,
  "isFromThisCountry": true,
  "citizenId": 1000001,
  "currencyName": "LTL",
  "employer": {
    "id": 1234567, "name": "EmployerName", "salary": 10000, "netSalary": 9900,
    "message": "You already work for <a href='//www.erepublik.com/en/citizen/profile/1234567'>EmployerName</a>. To apply for this job you have to quit your current job."
  },
  "jobs": [
    {
      "citizen": { "id": 7654321, "name": "OtherEmployer", "img": "https://cdnt.erepublik.net/…", "countryId": 24 },
      "companyName": "Company Name",
      "salary": 7111,
      "netSalary": 7039.89,
      "salaryLimit": 0,
      "currency": "LTL"
    }
  ],
  "pages": 20
}
```

| Field | Type | Description |
|-------|------|-------------|
| `isEmployed` | boolean | You have a job |
| `isOrganization` | boolean | The account is an organization |
| `isFromThisCountry` | boolean | You are a citizen of this country |
| `citizenId` | number | Your ID |
| `currencyName` | string | Country currency |
| `employer` | object | Current employer: `id`, `name`, `salary`, `netSalary`, `message` (HTML). Earlier notes: only when employed |
| `jobs[].citizen` | object | Employer: `id`, `name`, `img`, `countryId` |
| `jobs[].companyName` | string | Company or holding name |
| `jobs[].salary`, `netSalary` | number | Gross salary and salary after tax |
| `jobs[].salaryLimit` | number | Employer's salary budget, `0` = none |
| `jobs[].currency` | string | Currency code |
| `pages` | number | Page count, 10 offers per page |

Earlier notes: `netSalary = salary − salary × income tax`; only offers with free positions and enough funds are listed.

**Source:** real request, 2026-11-27; `JobMarketFactory` in `erpk_angular_controllers.js` (version 1795779904).

---

## Apply for a job

`POST /en/economy/job-market-apply` · Auth required · 🔍 From code

> [!WARNING]
> Employs you immediately.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | [CSRF token](../guides/requests.md#csrf-token) |
| `citizenId` | number | Yes | **Employer's** ID, `jobs[].citizen.id` |
| `salary` | number | Yes | `jobs[].salary` of an active offer, exactly |

```bash
curl 'https://www.erepublik.com/en/economy/job-market-apply' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&citizenId=7654321&salary=7111'
```

### Response

The client treats a truthy `message` as shown success and then opens My Places; otherwise it shows `error`. Earlier notes:

```json
{ "message": "Congratulation, you are now working for <a href='/en/citizen/profile/7654321'>OtherEmployer</a>." }
```

```json
{ "message": "You already have a job. You must resign first." }
```

Earlier notes: there is no approval step; the request fails when the offer was taken, removed, or the employer's budget (`salaryLimit`) is used up.

**Source:** `JobMarketController.apply` (version 1795779904); examples from earlier notes.

---

## Resign

`POST /en/economy/resign` · Auth required · 🔍 From code

> [!WARNING]
> Quits your job without asking for confirmation (the game's dialog is only in the web client). Earlier notes: it resets the work streak counted for the Hard Worker medal.

| Name | Type | Required | Description |
|------|------|----------|-------------|
| `_token` | string | Yes | CSRF token |
| `action_type` | string | Yes | `resign` |

```bash
curl 'https://www.erepublik.com/en/economy/resign' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest' \
  --data-raw '_token=YOUR_CSRF_TOKEN&action_type=resign'
```

### Response

Earlier notes:

```json
{ "status": true, "message": true, "error": false }
```

```json
{ "status": false, "message": "You are not employed." }
```

**Source:** `ErpkMyJobController.resign` (version 1795779904); examples from earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed or code | Page follows |
|-------|---------------|------------------|--------------|
| Apply error | In `message` | Client shows `message` as success, reads `error` for errors | Both listed |
