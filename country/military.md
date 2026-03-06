# Country Military Page

#erepublik #api #country #military

[< Back to Country Overview](README.md)

---

Retrieves the country military page as HTML. This is a **web page endpoint**, not a JSON API. The page displays military information for a country including natural enemy status, defence shield, airstrike readiness, active wars, active resistance wars, and mutual protection pacts.

**Method:** GET
**URL:** `/en/country/military/{countryName}`
**Auth Required:** No

## Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| countryName | string | Yes | The country permalink name (e.g., `USA`, `Lithuania`, `Republic-of-Moldova`) |

## Headers

| Header | Value | Required |
|--------|-------|----------|
| User-Agent | `Mozilla/5.0 ...` | Recommended (Cloudflare may challenge bot-like requests) |

## Example Request (cURL)

<details>
<summary>cURL example</summary>

```bash
curl -X GET 'https://www.erepublik.com/en/country/military/USA' \
  -H 'User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36'
```

</details>

## Example Response

Returns a full HTML page (`<title>eRepublik Country Military | USA</title>`) with six sections under `<h2 class="section">` headings.

## Page Sections (parsed from HTML)

### 1. Natural Enemy

Under `<h2 class="section">Natural Enemy</h2>`, preceded by an info message about +10% damage bonus:

<details>
<summary>HTML example</summary>

```html
<table class="info_message">
    <tr>
        <td>The citizens of this country will be provided with a +10% damage bonus
            in the military campaigns against the Natural Enemy.
            (<a href="//wiki.erepublik.com/index.php/Natural_enemy" target="_blank">more info</a>)
        </td>
    </tr>
</table>

<div class="indent">
    <!-- When NO natural enemy: -->
    <div class="attacker" style="padding-top:0px;">
        <div class="nameholder" style="font-size:14px; margin-top:9px;">
            No current Natural Enemy
        </div>
    </div>

    <!-- When natural enemy IS set: -->
    <div class="attacker" style="padding-top:0px; width: 500px">
        <div class="flagholder">
            <a href="//www.erepublik.com/en/country/society/{enemyCountry}">
                <img src="//www.erepublik.net/images/flags_png/L/{enemyCountry}.png" />
            </a>
        </div>
        <div class="nameholder" style="font-size:14px; margin-top:9px;">
            <a class="dotted" href="//www.erepublik.com/en/country/society/{enemyCountry}">
                {Enemy Country Name}
            </a>
        </div>
        Since: Day 6,371, 11:40 (9 months ago)
    </div>
</div>
```

</details>

| Data | CSS Selector | Example (USA) | Example (Poland) |
|------|-------------|---------------|-------------------|
| Natural enemy status | `div.indent > div.attacker > div.nameholder` text | `No current Natural Enemy` | Enemy name (e.g., `Estonia`) |
| Enemy flag | `div.indent > div.attacker img` src | -- | `//www.erepublik.net/images/flags_png/L/{country}.png` |
| Enemy link | `div.indent > div.attacker a` href | -- | `/en/country/society/{country}` |
| Since timestamp | Text after `.nameholder` in `.attacker` div | -- | `Since: Day 6,371, 11:40 (9 months ago)` |

> **Note:** When a natural enemy IS set, the section includes a "Since" timestamp showing when the natural enemy was declared, with both the eRepublik game day and a human-readable relative time (e.g., "9 months ago"). The flag image uses the `/flags_png/L/` (Large) size, not XL.

### 2. Defence Shield

Under `<h2 class="section">Defence Shield</h2>`, with an info message explaining the mechanic:

<details>
<summary>HTML example</summary>

```html
<table class="info_message">
    <tr>
        <td>The Defence Shield protects your country against attacks.<br />
            When a region is attacked, your country receives a damage bonus equal to
            the Shield Capacity divided by the number of regions owned.</td>
    </tr>
</table>
<div class="indent">
    <div class="attacker shield_system" style="padding-top:0px;">
        <div class="defence_shield">
            Defence Shield: 0 damage left
        </div>
    </div>
</div>
```

</details>

| Data | CSS Selector | Example (USA) |
|------|-------------|---------------|
| Shield capacity | `div.defence_shield` text | `Defence Shield: 0 damage left` |

> **Parsing:** Extract numeric value from `Defence Shield: {N} damage left` -- when 0, the shield is depleted.

### 3. Airstrike Readiness

Under `<h2 class="section">Airstrike</h2>`. Displays two circular gauge widgets (jQuery Knob) showing progress toward airstrike requirements. The Country President can use an airstrike to declare war on countries without shared borders.

<details>
<summary>HTML example</summary>

```html
<div class="indent borders">
    <!-- Energy Units gauge -->
    <div>
        <input id="health_units" data-req="245000" class="knob"
               data-width="90" data-height="90"
               data-borderColor="#D6D6D6" data-displayPrevious=false
               data-fgColor="#9ADE53" data-bgColor="#FFF" data-skin="tron"
               value="13" data-thickness=".25"
               data-readOnly=true readonly=readonly />
        <span><strong>Energy Units required:</strong>
            <em id="health_unit_cur">34,090</em> / 245,000
        </span>
    </div>

    <!-- Currency gauge -->
    <div>
        <input id="currency_req" data-req="240000" class="knob"
               data-width="90" data-height="90"
               data-borderColor="#D6D6D6" data-displayPrevious=false
               data-fgColor="#EC9E7A" data-bgColor="#FFF" data-skin="tron"
               value="100" data-thickness=".25"
               data-readOnly=true readonly=readonly />
        <span><strong>Currency required:</strong>
            <em id="currency_req_cur">1,448,095,127</em> / 240,000
        </span>
    </div>
</div>
```

</details>

| Data | HTML Element | Attribute | Example (USA) |
|------|-------------|-----------|---------------|
| Energy required | `input#health_units` | `data-req` | `245000` |
| Energy current | `em#health_unit_cur` | text content | `34,090` |
| Energy gauge % | `input#health_units` | `value` (0-100) | `13` |
| Currency required | `input#currency_req` | `data-req` | `240000` |
| Currency current | `em#currency_req_cur` | text content | `1,448,095,127` |
| Currency gauge % | `input#currency_req` | `value` (0-100) | `100` |

> **Note:** Airstrike requirements are **country-specific**. Compare:
> - USA: 245,000 energy units, 240,000 currency
> - Lithuania: 45,750 energy units, 270,000 currency
> - Albania: 103,250 energy units, 66,666.67 currency
>
> The `value` attribute is an integer 0-100 representing gauge fill percentage. The gauge color is green (`#9ADE53`) for energy and orange (`#EC9E7A`) for currency. The `data-req` can be a **floating-point number** (e.g., Albania's currency: `66666.666666667`) -- the display rounds it.

### 3b. Airstrike Donation Form (Logged-in Only)

When authenticated (level 25+), below the airstrike gauges, a donation form appears for contributing Food and Currency to the airstrike effort. This is similar to the [Economy page](economy.md) donation form but **without Gold**:

<details>
<summary>HTML example</summary>

```html
<table class="donate_table" cellpading="0" cellspacing="0">
    <tr>
        <!-- Food donation with quality selector (same as economy page) -->
        <td><b class="green">Food:</b></td>
        <td><input type="text" id="food_amount" name="food_amount" value="10" /></td>
        <td>
            <!-- Food quality selector: q1 through q7 images -->
            <div class="food_type">
                <img src=".../q1_30x30.png" data-quality="1" alt="" />
                <!-- ... -->
                <img src=".../q7_30x30.png" data-quality="7" alt="" />
            </div>
        </td>
        <td>
            <a onclick="donateFood_onClick();" title="Contribute">
                <span>Contribute</span>
            </a>
        </td>
    </tr>
    <tr>
        <!-- Currency donation -->
        <td><b class="orange">Currency:</b></td>
        <td><input type="text" id="currency_amount" name="currency_amount" value="20" /></td>
        <td>
            <span class="currency_cl">
                <img src="//www.erepublik.net/images/flags_png/XL/Lithuania.png"
                     alt="" width="24" /><strong>LTL</strong>
            </span>
        </td>
        <td>
            <a onclick="donateCurrency_onClick();" title="Contribute">
                <span>Contribute</span>
            </a>
        </td>
    </tr>
</table>
```

</details>

> **Note:** The airstrike donation form uses `donateFood_onClick()` and `donateCurrency_onClick()` (without `_eTab` suffix, unlike the [Economy page](economy.md)). The `info_message` above mentions the level 25 requirement and that "The Country President can use the Airstrike to declare war and attack a country that you do not have borders with." The `SERVER_DATA.countryId` is set inline for the donation JS handlers.

### 4. Active Wars

Under `<h2 class="section">Active wars in {countryName}</h2>`. Wars are listed in a `table.political.largepadded`:

<details>
<summary>HTML example</summary>

```html
<h2 class="section">Active wars in USA</h2> <!-- adaugat text -->
<div class="indent">
    <table class="political largepadded">
        <tr>
            <th width="530px"></th>
            <th width="65px"></th>
        </tr>
        <!-- War entry (repeated for each war) -->
        <tr>
            <td>
                <div class="warholder-small">
                    <div class="attacker">
                        <div class="flagholder">
                            <a href="//www.erepublik.com/en/country/society/Romania">
                                <img src="//www.erepublik.net/images/flags_png/XL/Romania.png"
                                     alt="" width="48" style="margin-top:-6px;"/>
                            </a>
                        </div>
                        <div class="nameholder" style="font-size:14px; margin-bottom:1px;">
                            <a class="dotted" href="//www.erepublik.com/en/country/society/Romania"
                               title="">Romania</a>
                        </div>
                        <!-- No allies variant: -->
                        <a id="defender_allies" href="javascript:;" class="none"
                           title="no allies">no allies</a>
                        <!-- Has allies variant: -->
                        <a href="javascript:;" class="show_allies">1 allies
                            <div class="allies_tooltip left_side">
                                <img src="//www.erepublik.net/images/modules/pvp/tip.png"
                                     class="tip" alt="" />
                                <ul>
                                    <li><img src="//www.erepublik.net/images/flags_png/S/Estonia.png"
                                             alt="" />Estonia</li>
                                </ul>
                                <div class="clear"></div>
                            </div>
                        </a>
                    </div>
                    <div class="defender">
                        <!-- Same structure as attacker -->
                    </div>
                    <div class="middle">VS</div>
                </div>
            </td>
            <td>
                <span class="">
                    <a href="//www.erepublik.com/en/wars/show/192366"
                       class="std_global_btn mediumSize blueColore">
                        <span>details</span>
                    </a>
                </span>
            </td>
        </tr>
        <!-- More war entries... -->
    </table>

    <!-- "All wars" form at the bottom -->
    <form action="//www.erepublik.com/en/military/campaigns" method="POST"
          name="wars_filters_form" id="wars_filters_form">
        <input type="hidden" name="_token" value="{csrfToken}" id="fwt" />
        <input type="hidden" name="fwt" value="conquest" id="fwt" />
        <input type="hidden" name="fws" value="all" id="fws" />
        <input type="hidden" name="fwc" value="all" id="fwc" />
        <a href="javascript:;" onclick="document.getElementById('wars_filters_form').submit()"
           class="std_global_btn smallSize blueColor">All wars</a>
    </form>
</div>
```

</details>

**War entry CSS selectors for parsing:**

| Data | CSS Selector | Notes |
|------|-------------|-------|
| War container | `div.warholder-small` | One per war |
| Attacker name | `div.warholder-small > div.attacker div.nameholder a.dotted` | text content |
| Attacker flag | `div.warholder-small > div.attacker div.flagholder img` | `src` attr -> flag image |
| Attacker country link | `div.warholder-small > div.attacker div.flagholder a` | `href` -> `/en/country/society/{country}` |
| Defender name | `div.warholder-small > div.defender div.nameholder a.dotted` | text content |
| Defender flag | `div.warholder-small > div.defender div.flagholder img` | `src` attr |
| No allies | `a.none[title="no allies"]` | Has class `none` |
| Has allies | `a.show_allies` | Text: `{N} allies` |
| Ally names | `div.allies_tooltip li` | Text content of each `<li>` |
| Ally flags | `div.allies_tooltip li img` | Small flag (`/flags_png/S/`) |
| War details link | `a.std_global_btn[href*="wars/show"]` | href -> `/en/wars/show/{warId}` |
| War ID | Extract from details link | Integer from URL path |

**Allies tooltip structure:**

<details>
<summary>HTML example</summary>

```html
<!-- No allies: simple <a> with class="none" -->
<a id="defender_allies" href="javascript:;" class="none" title="no allies">no allies</a>

<!-- Has allies: <a> with class="show_allies" containing tooltip -->
<a href="javascript:;" class="show_allies">1 allies
    <div class="allies_tooltip left_side">
        <img src="//www.erepublik.net/images/modules/pvp/tip.png" class="tip" alt="" />
        <ul>
            <li><img src="//www.erepublik.net/images/flags_png/S/Estonia.png" alt="" />Estonia</li>
            <!-- More allies as <li> elements -->
        </ul>
        <div class="clear"></div>
    </div>
</a>
```

</details>

> **Note:** The tooltip uses small flag images (`/flags_png/S/`) while the main war entry uses XL flags (`/flags_png/XL/`).

**"All wars" form** -- hidden form that POSTs to `/en/military/campaigns` with filter parameters:

| Hidden Field | Value | Description |
|-------------|-------|-------------|
| `_token` | CSRF token | Anti-forgery token |
| `fwt` | `conquest` | War type filter |
| `fws` | `all` | War status filter |
| `fwc` | `all` | Country filter |

Example active wars data (USA, eDay 6654, **32 active wars**):

| # | Attacker | Defender | Allies (Attacker) | War ID |
|---|----------|----------|-------------------|--------|
| 1 | Romania | **USA** | none | 192366 |
| 2 | Canada | **USA** | Estonia (1) | 192823 |
| 3 | Poland | **USA** | Finland (1) | 192904 |
| 4 | Netherlands | **USA** | none | 198537 |
| 5 | Lithuania | **USA** | none | 199687 |
| 6 | Portugal | **USA** | none | 202192 |
| 7 | Australia | **USA** | none | 205693 |
| 8 | Philippines | **USA** | none | 211305 |
| 9 | United Kingdom | **USA** | none | 211788 |
| 10 | **USA** | France | none | 214786 |
| 11 | **USA** | Indonesia | none | 215769 |
| 12 | United Arab Emirates | **USA** | none | 216548 |
| 13 | Argentina | **USA** | none | 217413 |
| 14 | Sweden | **USA** | none | 217753 |
| 15 | Czech Republic | **USA** | none | 217834 |
| 16 | Japan | **USA** | none | 217996 |
| 17 | Thailand | **USA** | none | 219357 |
| 18 | Brazil | **USA** | none | 219510 |
| 19 | Latvia | **USA** | none | 220376 |
| 20 | Greece | **USA** | none | 220994 |
| 21 | Belarus | **USA** | none | 222069 |
| 22 | Republic of Moldova | **USA** | none | 222102 |
| 23 | **USA** | Republic of China (Taiwan) | none | 222124 |
| 24 | Mexico | **USA** | none | 222321 |
| 25 | **USA** | Cuba | none | 222375 |
| 26 | **USA** | China | none | 222719 |
| 27 | **USA** | Paraguay | none | 223166 |
| 28 | Spain | **USA** | none | 223261 |
| 29 | Israel | **USA** | none | 223264 |
| 30 | North Macedonia | **USA** | none | 223310 |
| 31 | Egypt | **USA** | none | 223577 |
| 32 | South Africa | **USA** | none | 223581 |
| 33 | Bulgaria | **USA** | none | 224057 |
| 34 | Colombia | **USA** | none | 224147 |

> **Observation:** The viewed country can appear as either attacker or defender. USA is attacker in wars #10, #11, #23, #25, #26, #27 (attacking France, Indonesia, Taiwan, Cuba, China, Paraguay) and defender in all others.

### 5. Active Resistance Wars

Under `<h2 class="section">Active resistance wars in {countryName}</h2>`. Uses `table.political.padded` (note: `.padded` not `.largepadded` like active wars):

<details>
<summary>HTML example (no resistance wars)</summary>

```html
<h2 class="section">Active resistance wars in Albania</h2>
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="215px"></th>
            <th width="318px"></th>
            <th width="63px"></th>
        </tr>
        <tr>
            <td colspan="3">
                There are no resistance wars in this country.
            </td>
        </tr>
    </table>
</div>
```

</details>

<details>
<summary>HTML example (with resistance wars - Poland)</summary>

```html
<h2 class="section">Active resistance wars in Poland</h2>
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="215px"></th>
            <th width="318px"></th>
            <th width="63px"></th>
        </tr>
        <tr>
            <td class="last">
                <img style="vertical-align:middle"
                     src="//www.erepublik.net/images/flags_png/L/Resistance.png" alt="" />
                Resistance Force of Canada
            </td>
            <td class="last">
                <span class="goright influence_multiplier"
                      title="Canada receives x2.94 Influence due to their Determination to be free.">
                    2.94<span></span>
                </span>
            </td>
            <td class="last">
                <a href="//www.erepublik.com/en/wars/show/224764"
                   class="std_global_btn mediumSize blueColore">
                    <span>details</span>
                </a>
            </td>
        </tr>
    </table>
</div>
```

</details>

**Resistance war entry CSS selectors:**

| Data | CSS Selector | Example |
|------|-------------|---------|
| Resistance flag | `td img[src*="Resistance.png"]` | `/images/flags_png/L/Resistance.png` |
| Resistance force name | `td` text after flag img | `Resistance Force of Canada` |
| Determination multiplier | `span.influence_multiplier` text | `2.94` (citizens of the occupied country receive x2.94 influence) |
| War details link | `a[href*="wars/show"]` | `/en/wars/show/224764` |

> **Note:** The resistance wars table has 3 columns (215px, 318px, 63px) vs active wars table with 2 columns (530px, 65px). The flag used is a special `/flags_png/L/Resistance.png` generic resistance flag, not a country flag. The determination multiplier works the same as on the [Society page](society.md) for occupied regions.

### 6. Mutual Protection Pacts (MPP)

Under `<h2 class="section">Mutual Protection Pacts</h2>`. Uses `table.political.padded`:

<details>
<summary>HTML example (no MPPs)</summary>

```html
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="405px;"></th>
            <th width="195px;"></th>
        </tr>
        <tr>
            <td colspan="3">
                This country has no mutual protection pacts.
            </td>
        </tr>
    </table>
    <a href="//www.erepublik.com/en/list/mpp/0/1"
       class="std_global_btn smallSize blueColor">All Mutual Protection Pacts</a>
</div>
```

</details>

<details>
<summary>HTML example (with MPPs - Poland)</summary>

```html
<div class="indent">
    <table class="political padded">
        <tr>
            <th width="405px;"></th>
            <th width="195px;"></th>
        </tr>
        <tr>
            <td class="last">
                <span class="fakeheight">
                    <img src="//www.erepublik.net/images/flags_png/L/Finland.png"
                         alt="" class="flag"/>
                    <a href="//www.erepublik.com/en/country/society/Finland"
                       class="dotted" title="">
                        Finland
                    </a>
                </span>
            </td>
            <td class="last">
                <img src="//www.erepublik.net/images/parts/icon-pact.gif"
                     alt="" class="icon" />
                Expires in 2 months
            </td>
        </tr>
    </table>
    <a href="//www.erepublik.com/en/list/mpp/0/1"
       class="std_global_btn smallSize blueColor">All Mutual Protection Pacts</a>
</div>
```

</details>

**MPP entry CSS selectors:**

| Data | CSS Selector | Example |
|------|-------------|---------|
| Country flag | `td span.fakeheight img.flag` src | `/images/flags_png/L/Finland.png` |
| Country name | `td span.fakeheight a.dotted` text | `Finland` |
| Country link | `td span.fakeheight a.dotted` href | `/en/country/society/Finland` |
| Pact icon | `td img.icon` src | `/images/parts/icon-pact.gif` |
| Expiry text | `td` text after pact icon | `Expires in 2 months` |

> **Note:** The MPP table has 2 columns (405px, 195px). An "All Mutual Protection Pacts" button links to `/en/list/mpp/0/1`. The expiry text uses relative time (e.g., "Expires in 2 months", "Expires in 25 days") -- the exact date is not shown. Flag images use the `/flags_png/L/` (Large) size.

## JavaScript on this Page

Unlike the [Economy page](economy.md), the Military page has **no structured data variables**. The JS is purely for UI rendering:

| Script | Purpose |
|--------|---------|
| `jquery.knob.js` | Renders circular gauge widgets for airstrike readiness |
| `allies_dropdown.js` | Handles allies tooltip show/hide on hover |
| `countryPage.js` | General country page interactions |
| `countrySelector.js` | Country dropdown selector |

The only JS variable of interest:

<details>
<summary>JavaScript example</summary>

```javascript
var countrySelector = {
    placeholderText: "Choose a country...",
    urlTab: "country/military"
};
```

</details>

The jQuery Knob initialization uses `data-skin="tron"` for the arc-style rendering with custom draw function. The `value` attribute (0-100) drives the gauge fill animation.

Inline JS also includes `selectQuality`, `active_elem`, `current_elem` variables related to a food quality selector (likely for donation UI, only visible when logged in).

## Useful URL Patterns Discovered

| URL Pattern | Description |
|-------------|-------------|
| `/en/wars/show/{warId}` | War details page |
| `/en/list/mpp/0/1` | All Mutual Protection Pacts list |
| `/en/military/campaigns` | All campaigns page (POST with filter params) |
| `/en/country/society/{countryPermalink}` | Country page links use permalink from flag href |

## CSS Selectors Summary for Parsing

| Section | Key Selector | Data |
|---------|-------------|------|
| Natural Enemy | `h2.section:contains("Natural Enemy") ~ div.indent div.nameholder` | Enemy name or "No current Natural Enemy" |
| Defence Shield | `div.defence_shield` | Shield capacity text |
| Airstrike Energy | `input#health_units` | `data-req` (required), `value` (%), `em#health_unit_cur` (current) |
| Airstrike Currency | `input#currency_req` | `data-req` (required), `value` (%), `em#currency_req_cur` (current) |
| Active Wars | `table.political.largepadded div.warholder-small` | One per war |
| War details | `a[href*="wars/show"]` | War ID from URL |
| Resistance Wars | `h2:contains("resistance wars") ~ div table.political.padded` | Content or "no resistance wars" |
| MPP | `h2:contains("Mutual Protection Pacts") ~ div table.political.padded` | Content or "no mutual protection pacts" |

## Additional CSS & JS Resources

| Resource | URL Pattern |
|----------|-------------|
| War list styles | `//www.erepublik.net/css/cmp/war_list.{buildId}.css` |
| jQuery Knob | `//www.erepublik.net/js/jquery.knob.{buildId}.js` |
| Allies dropdown | `//www.erepublik.net/js/wars/allies_dropdown.{buildId}.js` |
| Country page | `//www.erepublik.net/js/country/countryPage.{buildId}.js` |
| Country selector | `//www.erepublik.net/js/country/countrySelector.{buildId}.js` |

## Notes

- This is an **HTML page endpoint**, not a JSON API endpoint
- The page is accessible **without authentication** (but Cloudflare may challenge bot-like requests)
- **No structured JavaScript data variables** -- unlike the Economy page, all data is rendered directly in HTML and must be parsed from the DOM
- Airstrike requirements (`data-req`) are **country-specific values** -- they differ between countries (USA: 245k energy / 240k currency; Lithuania: 45,750 energy / 270k currency)
- The number of active wars varies dynamically -- USA had **34 active wars** at time of documentation (eDay 6654)
- War IDs (e.g., `192366`, `224147`) are sequential integers usable with `/en/wars/show/{warId}`
- Country permalinks in URLs use hyphenated names (e.g., `Republic-of-Moldova`, `United-Arab-Emirates`, `Republic-of-China-Taiwan`)
- The `<h2 class="section">` headings contain the section name and sometimes the country name (e.g., "Active wars in USA", "Active resistance wars in USA")
- HTML source contains **Romanian developer comments**: `<!-- schimbari-->` ("changes"), `<!-- adaugat text -->` ("added text")
- The "All wars" button submits a hidden POST form to `/en/military/campaigns` with filter parameters -- this could be used to programmatically navigate to the campaigns page with preset filters
- The `blueColore` CSS class on war details buttons appears to be a **typo** in the source (should likely be `blueColor`)
- Ally flag images use the small size path (`/flags_png/S/`) while war entry flags use XL (`/flags_png/XL/`)
- Cloudflare challenge script is present at the bottom of the page

---

## See Also

- [Country Society Page](society.md) -- determination multipliers on occupied regions
- [Country Economy Page](economy.md) -- donation form comparison
- [Country Politics Page](politics.md)
- [Country Administration Page](administration.md)
- [Edge Cases](edge-cases.md#military-page--no-active-wars) -- no wars, no resistance wars, no MPPs
