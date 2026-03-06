# Country Politics Page

#erepublik #api #country #politics

[< Back to Country Overview](README.md)

---

Retrieves the country politics page as HTML. This is a **web page endpoint**, not a JSON API. The page displays three sections: the current Country President, appointed political titles (cabinet), and congressional party distribution. Unlike the economy page, this page contains **no JavaScript data variables** -- all data must be parsed from the HTML DOM.

**Method:** GET
**URL:** `/en/country/politics/{countryName}`
**Auth Required:** No

## Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Bosnia-Herzegovina`). Case-sensitive, multi-word names use hyphens |

## Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (avoids 403 from Cloudflare) |

## Example Request (cURL)

<details>
<summary>cURL example</summary>

```bash
curl -X GET 'https://www.erepublik.com/en/country/politics/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

</details>

## Example Response

Returns a full HTML page (`<body id="country">`) with three sections:

<details>
<summary>HTML response</summary>

```html
<!DOCTYPE html>
<html ng-app="ErpkApp" lang="en">
<head>
  <title>eRepublik Country Politics | USA</title>
  <meta name="description" content="USA is a country in Erepublik. Find information about the political activities of USA" />
  ...
</head>
<body id="country" class="logged_out lowered">
  ...
</body>
</html>
```

</details>

## Page Sections (parsed from HTML)

### 0. Dictator (Dictatorship Countries Only)

In **dictatorship countries**, an additional section appears **before** the President section. Located under `<h2 class="section">Dictator</h2>`:

<details>
<summary>HTML example</summary>

```html
<h2 class="section">Dictator</h2>
<div class="indent">
    <div class="infoholder">
        <div class="entity rightpadded">
            <div class="avatarholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9556415"
                   class="icon" title="xR0Nx">
                    <img src="https://cdnt.erepublik.net/.../avatar.jpg" />
                </a>
                <div class="xprank">162</div>
            </div>
            <div class="nameholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9556415"
                   class="dotted" title="xR0Nx">
                    xR0Nx
                </a>
            </div>
        </div>
    </div>
</div>
```

</details>

| Data | CSS Selector / Pattern | Example |
|------|----------------------|---------|
| Dictator name | `h2:contains("Dictator") ~ div .entity a.dotted` title attr | `xR0Nx` |
| Citizen ID | `.entity a.icon` href -> `/en/citizen/profile/{citizenId}` | `9556415` |
| Level | `.entity .xprank` text | `162` |

> **Note:** The Dictator section does **not** have "Election results" or "Next elections" links -- dictators are not elected. The `dictatorships` JavaScript variable (documented in [Global JS Variables](README.md#dictatorships-and-empires)) lists all current dictatorship country IDs.

> **Dictatorship effects on other sections:** When a country is a dictatorship, the **Congress section** is empty -- the table has headers only, with no party rows (Congress is dissolved under dictatorship). See [edge-cases.md](edge-cases.md#politics-page--dissolved-congress-dictatorship) for details.

### 1. President

Displays the current Country President with avatar, name, level, and profile link. Located under `<h2 class="section">President</h2>`:

<details>
<summary>HTML example</summary>

```html
<div class="indent">
    <div class="infoholder">
        <div class="entity rightpadded">
            <div class="avatarholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/8482827"
                   class="icon" title="Dominar Rygel XVI">
                    <img src="https://cdnt.erepublik.net/.../avatar.jpg" />
                </a>
                <div class="xprank">37</div>
            </div>
            <div class="nameholder">
                <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/8482827"
                   class="dotted" title="Dominar Rygel XVI">
                    Dominar Rygel XVI
                </a>
            </div>
        </div>
        <div class="smallholder">
            <a href="//www.erepublik.com/en/main/presidential-elections/24/1770278400">
                <span class="std_global_btn mediumSize blueColor">Election results</span>
            </a>
            <a href="//www.erepublik.com/en/presidential-candidates/country-24">
                <span class="std_global_btn mediumSize blueColor">Next elections</span>
            </a>
        </div>
    </div>
</div>
```

</details>

| Data | CSS Selector / Pattern | Example |
|------|----------------------|---------|
| President name | `.infoholder .entity a.dotted` title attr | `Dominar Rygel XVI` |
| Citizen ID | `.entity a.icon` href -> `/en/citizen/profile/{citizenId}` | `8482827` |
| Level | `.entity .xprank` text | `37` |
| Avatar | `.entity a.icon img` src | CDN URL |
| Election results URL | `.smallholder a` (1st) href | `/en/main/presidential-elections/{countryId}/{timestamp}` |
| Next elections URL | `.smallholder a` (2nd) href | `/en/presidential-candidates/country-{countryId}` |

> **Note:** The `hovercard=1` attribute on citizen links triggers AngularJS hovercard popups that use the `/en/main/citizen-profile-json-personal/{citizenId}` endpoint (documented in API_SOCIAL.md).

### 2. Political Titles (Cabinet)

Lists appointed government positions. Located under `<h2 class="section">Political titles</h2>` in a `<div class="political-titles">` container:

<details>
<summary>HTML example</summary>

```html
<div class="political-titles">
    <div class="entity entity-wrapper">
        <p class="main-title">Prime Minister</p>
        <div class="avatarholder">
            <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9474388"
               class="icon" title="Monsieur Manchukuo">
                <img src="https://cdnt.erepublik.net/.../avatar.png" />
            </a>
            <div class="xprank">43</div>
        </div>
        <div class="nameholder">
            <a hovercard=1 href="//www.erepublik.com/en/citizen/profile/9474388"
               class="dotted" title="Monsieur Manchukuo">
                Monsieur Manchukuo
            </a>
        </div>
    </div>
    <!-- More entity-wrapper divs for each title -->
</div>
```

</details>

| Data | CSS Selector | Example |
|------|-------------|---------|
| Title name | `.entity-wrapper p.main-title` | `Prime Minister` |
| Citizen name | `.entity-wrapper a.dotted` title attr | `Monsieur Manchukuo` |
| Citizen ID | `.entity-wrapper a.icon` href -> `/en/citizen/profile/{citizenId}` | `9474388` |
| Level | `.entity-wrapper .xprank` text | `43` |

Example cabinet (USA, eDay 6654):

| Title | Citizen | ID | Level |
|-------|---------|-----|-------|
| Prime Minister | Monsieur Manchukuo | 9474388 | 43 |
| Minister of Defense | Herr Vootsman | 1714685 | 1357 |
| Minister of Foreign Affairs | svs1109 | 9375724 | 270 |
| Governor | Yui MHCP001 | 3404889 | 122 |
| Minister of Education | Hachiman Kokeshi | 9109594 | 61 |

> **Note:** Political titles are **appointed by the president** and vary by country -- not all countries use the same titles. The number and names of titles are completely customizable.

### 3. Congress

Displays top 5 party representation in Congress. Located under `<h2 class="section">Congress</h2>` in a `table.political.largepadded`:

<details>
<summary>HTML example</summary>

```html
<table class="political largepadded">
    <tr>
        <th width="210px"></th>
        <th width="85px"></th>
        <th width="305px"></th>
    </tr>
    <tr>
        <td><span class="fakeheight">
            <img src="//cdnt.erepublik.net/.../party_avatar.png"
                 class="icon" width="55" height="55"/>
            <a href="//www.erepublik.com/en/party/we-the-people-19/1" class="dotted">
                We The People
            </a>
        </span></td>
        <td><span class="special">27</span> %</td>
        <td>
            <span class="emplevel" style="width: 55%">&nbsp;</span><br />
        </td>
    </tr>
    <!-- More party rows -->
</table>
<a href="//www.erepublik.com/en/main/congress-elections/24/0/1769328000">
    <span class="std_global_btn mediumSize blueColor">Election results</span>
</a>
```

</details>

| Data | CSS Selector | Example |
|------|-------------|---------|
| Party name | `table.political a.dotted` text | `We The People` |
| Party URL | `table.political a.dotted` href -> `/en/party/{slug}-{partyId}/1` | `/en/party/we-the-people-19/1` |
| Party avatar | `table.political img.icon` src | CDN URL |
| Seats % | `table.political span.special` text | `27` |
| Bar width | `span.emplevel` style `width` (= percentage x 2) | `55%` for 27% seats |
| Election results | Last `<a>` in Congress section | `/en/main/congress-elections/{countryId}/0/{timestamp}` |

Example data (USA, eDay 6654):

| Party | Slug | Party ID | Seats % | Bar Width |
|-------|------|----------|---------|-----------|
| We The People | we-the-people | 19 | 27% | 55% |
| The Black Sheep Party | the-black-sheep-party | 2397 | 25% | 50% |
| Rough Riders | rough-riders | 6026 | 20% | 40% |
| Federalist Party | federalist-party | 2263 | 15% | 30% |
| Socialist Freedom Party | -socialist-freedom-party | 3653 | 12% | 25% |

> **Party URL pattern:** `/en/party/{partyNameSlug}-{partyId}/1` -- the slug is lowercase with hyphens, and the party ID is appended after a hyphen. Note the leading hyphen in `"-socialist-freedom-party"` -- this appears to be a data quirk (extra space in the original name).

## Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/main/presidential-elections/{countryId}/{timestamp}` | Presidential election results |
| `/en/presidential-candidates/country-{countryId}` | Current presidential candidates |
| `/en/main/congress-elections/{countryId}/0/{timestamp}` | Congress election results |
| `/en/party/{partySlug}-{partyId}/1` | Party page (slug + ID) |
| `/en/citizen/profile/{citizenId}` | Citizen profile |

## Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible without authentication (but Cloudflare may challenge bot-like requests)
- **No embedded JavaScript data variables** -- all political data is rendered directly in HTML and must be parsed from the DOM
- The only JS variable of note is `countrySelector` with `urlTab: "country/politics"`
- The `hovercard=1` HTML attribute on citizen `<a>` tags is used by AngularJS to trigger profile hovercards
- Political titles are **appointed by the president** and vary by country -- not all countries use the same titles or number of positions
- The Congress section shows only the **top 5 parties** -- total may not add to 100% if there are additional smaller parties
- The bar chart width (`span.emplevel` style) is approximately `seats% x 2` (e.g., 27% -> 55% width)
- Party names may be **truncated with `...`** in the display (e.g., "The Black Sheep ..." -> full name: "The Black Sheep Party")
- Election timestamps in URLs are **Unix timestamps** (e.g., `1770278400` for presidential, `1769328000` for congress)
- Country ID appears in election URLs -- for USA it's `24` (see [Society page](society.md) for full country ID reference)
- Congress seat percentages are displayed as **integers** (rounded)

---

## See Also

- [Country Society Page](society.md) -- country ID reference table
- [Country Economy Page](economy.md)
- [Country Military Page](military.md)
- [Country Administration Page](administration.md) -- law proposals
- [Congressional Donations](congress.md)
- [Edge Cases](edge-cases.md#politics-page--dissolved-congress-dictatorship) -- dissolved Congress under dictatorship
