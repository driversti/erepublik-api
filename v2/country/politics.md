# Politics

[API docs](../README.md) › Country and politics › Politics

The **Politics** tab of a country: the president (and the dictator, where there is one), the appointed political titles and the largest parties in Congress.

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Politics page](#politics-page) | GET | `/en/country/politics/{country}` | ✅ Verified 2026-11-27 |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels). Path rules, page header and country selector: [Society](society.md#common-to-all-country-pages).

Related: [Elections](elections.md) · [Political parties](parties.md) · [Congress proposals and laws](laws.md)

---

## Politics page

`GET /en/country/politics/{country}` · No auth (earlier notes) · ✅ Verified 2026-11-27

```bash
curl 'https://www.erepublik.com/en/country/politics/Poland' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

The page has no data variables; everything is parsed from HTML.

### Dictator

Only in dictatorship countries (earlier notes): an `h2.section` **Dictator** before the president, with the same citizen block and no election links. Under a dictatorship the Congress table keeps its header row but has no parties.

### President

```html
<h2 class="section">President</h2>
<div class="indent"><div class="infoholder">
  <div class="entity rightpadded">
    <div class="avatarholder">
      <a hovercard="1" href="//www.erepublik.com/en/citizen/profile/{citizenId}" class="icon" title="PlayerName">
        <img src="https://cdnt.erepublik.net/…"></a>
      <div class="xprank">59</div>
    </div>
    <div class="nameholder"><a hovercard="1" href="…/en/citizen/profile/{citizenId}" class="dotted" title="PlayerName">PlayerName</a></div>
  </div>
  <div class="smallholder">
    <a href="//www.erepublik.com/en/main/presidential-elections/35/1793865600">Election results</a>
    <a href="//www.erepublik.com/en/presidential-candidates/country-35">Next elections</a>
  </div>
</div></div>
```

| Data | Selector |
|------|----------|
| Name | `.entity a.dotted` `title` |
| Citizen ID | `.entity a.icon` href |
| Level | `.entity .xprank` |
| Avatar | `.entity a.icon img[src]` |
| [Election results](elections.md#presidential-results) | `.smallholder a` (first) |
| [Next elections](elections.md#presidential-candidates) | `.smallholder a` (second) |

The `hovercard` attribute opens a popup fed by the [citizen profile endpoints](../social/profiles.md).

### Political titles

`div.political-titles`, one `.entity-wrapper` per title: `p.main-title` plus the same citizen block as above. Five titles were listed for Poland: Prime Minister, Minister of Defense, Minister of Foreign Affairs, Governor, Minister of Education. Earlier notes: the president appoints them and the set differs between countries.

### Congress

The five largest parties, in `table.political.largepadded`:

```html
<tr>
  <td><span class="fakeheight">
    <img src="//cdnt.erepublik.net/…" class="icon" width="55" height="55">
    <a href="//www.erepublik.com/en/party/{slug}-{partyId}/1" class="dotted">Party name</a></span></td>
  <td><span class="special">27</span> %</td>
  <td><span class="emplevel" style="width: 55%">&nbsp;</span></td>
</tr>
```

| Data | Selector | Notes |
|------|----------|-------|
| Party name | `a.dotted` | Long names may be cut with `...` |
| [Party page](parties.md#party-page) | `a.dotted` href | ID follows the last hyphen of the slug |
| Share of seats | `span.special` | Whole percent; the five need not add up to 100 |
| Bar | `span.emplevel` width | About twice the percentage |
| [Election results](elections.md#congress-results) | Link under the table | `/en/main/congress-elections/{countryId}/0/{timestamp}` |

The timestamps in the election links are Unix times of the election day.

**Source:** real request, 2026-11-27; dictatorship layout and bar rule — earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed 2026-11-27 | Page follows |
|-------|---------------|---------------------|--------------|
| Party slugs | A leading hyphen occurs (`-socialist-freedom-party-3653`) | Also seen (`-terror-{id}`) on the candidates list | Treat the slug as opaque |
| Dictator section | Documented for a dictatorship | No dictatorship page rechecked | Earlier notes |
