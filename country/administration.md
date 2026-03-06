# Country Administration Page

#erepublik #api #country #administration

[< Back to Country Overview](README.md)

---

Retrieves the country administration page as HTML. This is the **5th tab** of the country pages and uses a **different URL pattern** from the other four tabs (`/en/country-administration/` instead of `/en/country/{tab}/`). The page shows military priorities (Campaign of the Day / deploy allied forces) and a paginated history of law proposals.

**Method:** GET
**URL:** `/en/country-administration/{countryName}/{page}`
**Auth Required:** No

## Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Republic-of-Moldova`) |
| page | integer | Yes | Page number for law proposals pagination (starts at `1`) |

## Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (Cloudflare may challenge bot-like requests) |

## Example Request (cURL)

<details>
<summary>cURL example</summary>

```bash
curl -X GET 'https://www.erepublik.com/en/country-administration/USA/1' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

</details>

## Example Response

Returns a full HTML page (`<title>eRepublik Country Administration | USA</title>`) with two main sections.

## Page Sections (parsed from HTML)

### 1. Access Message

A message indicating the viewer's access level:

<details>
<summary>HTML example</summary>

```html
<div class="holder bordersep">
    <p class="infoholder">
        <span class="rightpadded">You are not a president or a congress member in this country.</span>
        <a href="//wiki.erepublik.com/index.php/USA" target="_blank">
            <img src="//www.erepublik.net/images/parts/btn_icon-wiki.gif" title="Open Wiki page" class="flag" alt="wiki">
        </a>
    </p>
</div>
```

</details>

> **Note:** When logged in as the Country President or a Congress member, this area likely shows additional controls (e.g., propose law buttons). The message text changes based on the viewer's role.

### 2. Military Priorities

Under `<h2 class="section">Military Priorities</h2>` inside `div#militaryPriorities`:

<details>
<summary>HTML example</summary>

```html
<div class="holder" id="militaryPriorities">
    <h2 class="section">Military Priorities</h2>
    <div class="adminaction">
        <div class="goleft padded">
            <a class="std_global_btn smallSize orangeColor mppPriorities disabled"
               id="mppPriorityTrigger" title="Deploy allied forces" tipsy>
                <span>Deploy allied forces</span>
            </a>
        </div>
    </div>
</div>
```

</details>

| Data | CSS Selector | Notes |
|------|-------------|-------|
| Deploy allied forces button | `a#mppPriorityTrigger` | Has class `disabled` when viewer is not president |
| Button text | `a#mppPriorityTrigger span` | `Deploy allied forces` |

> **Note:** The "Deploy allied forces" button has class `disabled` for non-president viewers. When the Country President is logged in, this button opens the MPP priority popup (handled by `generalPopup.renderPopup('mppPriority')` in JS). There may also be a "Campaign of the Day" feature (see JS variables below).

### 2b. Manage Organizations

Under `<h2 class="section">Manage Organizations</h2>` inside `div#manageOrganizations`. This section appears **after** Military Priorities:

<details>
<summary>HTML example</summary>

```html
<div class="holder" id="manageOrganizations">
    <h2 class="section">Manage Organizations</h2>
    <div class="adminaction">
        <div class="goleft padded">
            <a class="std_global_btn smallSize orangeColor mppPriorities disabled"
               id="manageOrganizationsTrigger" title="Manage Organizations" tipsy>
                <span>Manage Organizations</span>
            </a>
        </div>
    </div>
</div>
```

</details>

| Data | CSS Selector | Notes |
|------|-------------|-------|
| Manage Organizations button | `a#manageOrganizationsTrigger` | Has class `disabled` when viewer is not president |
| Button text | `a#manageOrganizationsTrigger span` | `Manage Organizations` |

> **Note:** Like the "Deploy allied forces" button, the "Manage Organizations" button has class `disabled` for non-president viewers. When the Country President is logged in, this button likely opens a popup to manage country organizations. Note the CSS class `mppPriorities` is reused from the Military Priorities section (likely a copy-paste from the original code).

### 3. Law Proposals

Under `<h2 class="section">Law proposals</h2>`. Contains a table listing recent law proposals with their status:

<details>
<summary>HTML example</summary>

```html
<div class="holder">
    <h2 class="section">Law proposals</h2>
    <div class="indent">
        <table class="laws lawDescription largepadded">
            <!-- Law entry (repeated for each proposal) -->
            <tr>
                <td width="250" class="first">
                    <a href="//www.erepublik.com/en/main/law/USA/327910"
                       title="" class="dotted taxStyle Donate">
                        Donate
                        <span class="lawIco"></span>
                    </a>
                </td>
                <td width="150" class="first">
                    Day 6,653, 21:35 <br><small>(6 hours ago)</small>
                </td>
                <td width="140" class="first">
                    <img src="//www.erepublik.net/images/parts/icon-law_empty.gif"
                         alt="Pending" class="icon">Pending
                </td>
                <td width="60" class="first">
                    <a href="//www.erepublik.com/en/main/law/USA/327910"
                       class="std_global_btn tinySize blueColor">
                        <span>Details</span>
                    </a>
                </td>
            </tr>
            <!-- More law entries... -->
        </table>
        <ul class="pager">
            <!-- Pagination links when multiple pages exist -->
        </ul>
    </div>
</div>
```

</details>

**Law entry CSS selectors for parsing:**

| Data | CSS Selector | Example |
|------|-------------|---------|
| Law type + link | `table.laws a.dotted` | text: `Donate`, href: `/en/main/law/USA/327910` |
| Law type CSS class | `table.laws a.dotted` class attr | `taxStyle Donate`, `taxStyle NaturalEnemy` |
| Law icon | `table.laws span.lawIco` | Empty span, styled via CSS based on parent class |
| Date/time | `table.laws td:nth-child(2)` | `Day 6,653, 21:35` with `<small>(6 hours ago)</small>` |
| Status icon | `table.laws td:nth-child(3) img.icon` | `alt` attr: `Pending` or `Accepted` |
| Status text | `table.laws td:nth-child(3)` | `Pending` or `Accepted` |
| Details link | `table.laws a.std_global_btn` | href: `/en/main/law/{countryName}/{lawId}` |
| Law ID | Extract from details link | Integer from URL (e.g., `327910`) |
| Pagination | `ul.pager` | Contains page links when more than one page of laws |

**Law types discovered (from CSS classes):**

| CSS Class | Law Type | Description |
|-----------|----------|-------------|
| `taxStyle Donate` | Donate | Treasury donation proposal |
| `taxStyle NaturalEnemy` | Natural Enemy | Declare/remove natural enemy |
| `taxStyle Airstrike` | Airstrike | Launch an airstrike against a non-bordering country |
| `taxStyle DeclareWar` | Declare War | Declare war on a bordering country |

> **Note:** Additional law types likely exist (e.g., MPP, Embargo, Tax Change, Impeachment) but have not been observed yet. The CSS class on the `<a>` tag follows the pattern `taxStyle {LawType}`.

**Status icons:**

| Status | Icon File | Alt Text |
|--------|----------|----------|
| Pending | `icon-law_empty.gif` | `Pending` |
| Accepted | `icon-law_valid.gif` | `Accepted` |
| Rejected | `icon-law_invalid.gif` | `Rejected` |

> **Note:** Additional statuses may exist (e.g., Expired) with corresponding icon files.

Example law proposals data (USA, eDay 6654, page 1):

| Law Type | Law ID | Date | Status |
|----------|--------|------|--------|
| Donate | 327910 | Day 6,653, 21:35 | Pending |
| Natural Enemy | 327868 | Day 6,653, 06:28 | Pending |
| Donate | 327847 | Day 6,652, 21:10 | Accepted |
| Donate | 327788 | Day 6,651, 21:08 | Accepted |
| Donate | 327725 | Day 6,650, 21:02 | Accepted |
| Donate | 327676 | Day 6,649, 20:59 | Accepted |
| Donate | 327619 | Day 6,648, 20:09 | Accepted |
| Donate | 327561 | Day 6,647, 19:13 | Accepted |
| Donate | 327504 | Day 6,646, 18:36 | Accepted |
| Donate | 327437 | Day 6,645, 18:30 | Accepted |

> **Observation:** The first row has `class="first"` on all `<td>` elements -- subsequent rows do not have this class. This is purely for CSS styling (top border).

## JavaScript on this Page

| Variable | Value | Description |
|----------|-------|-------------|
| `SERVER_culture` | `"en"` | Language/culture code |
| `SERVER_token` | `"{csrfToken}"` | CSRF token (same as `csrfToken` global) |
| `txt_no_bod` | `"No Campaign of the Day is currently set."` | Campaign of the Day empty state text |
| `txt_set_success` | `"Successfully set as Campaign of the Day"` | Campaign of the Day set confirmation |
| `txt_unset_success` | `"This campaign is no longer Campaign of the Day"` | Campaign of the Day unset confirmation |
| `COUNTRY_ADMINISTRATION_TEXTS` | `{set_text: 'Set', unset_text: 'Unset'}` | Button labels for Campaign of the Day |

The `countrySelector` variable uses `urlTab: "country-administration"` (note: hyphenated, not `/country/administration`).

| Script | Purpose |
|--------|---------|
| `battle_of_td.js` | Campaign of the Day (Battle of the Day) functionality |
| `countryPage.js` | General country page interactions |
| `countrySelector.js` | Country dropdown selector |

> **Campaign of the Day:** The JS variables and `battle_of_td.js` script reveal a feature where the Country President can set a military campaign as the "Campaign of the Day" to prioritize it for citizens. This feature is likely only visible/functional when logged in as president.

## Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/country-administration/{countryName}/{page}` | Administration page (paginated) |
| `/en/main/law/{countryName}/{lawId}` | Law proposal details page |

## Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible **without authentication** -- but shows reduced functionality with the message "You are not a president or a congress member in this country"
- **Different URL pattern** from other country tabs: uses `/en/country-administration/{countryName}/{page}` instead of `/en/country/{tab}/{countryName}`
- The `{page}` parameter is **required** (not optional) -- must be at least `1`
- Law proposals are **paginated** -- `ul.pager` contains pagination links when there are more laws than fit on one page
- Law IDs are **sequential integers** across all countries (e.g., `327910`, `327868`) -- they can be used with `/en/main/law/{countryName}/{lawId}`
- The `class="first"` on `<td>` elements only appears on the first row -- it's for CSS top-border styling
- The **Campaign of the Day** and **Deploy allied forces** features are only functional for the Country President (button is `disabled` for other viewers)
- Law types observed so far: `Donate`, `NaturalEnemy`, `Airstrike`, `DeclareWar`. Additional types likely exist (MPP, Embargo, Tax changes, Impeachment)
- The administration tab link in the country tabs uses the full URL with page number: `/en/country-administration/USA/1`
- Additional CSS: `country.{buildId}.css` (instead of `war_list.css` used by military tab)

---

## See Also

- [Country Society Page](society.md)
- [Country Economy Page](economy.md)
- [Country Politics Page](politics.md)
- [Country Military Page](military.md)
- [Congressional Donations](congress.md) -- propose donation endpoint
