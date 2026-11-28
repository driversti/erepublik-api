# News lists and RSS

[API docs](../README.md) › Media › News lists and RSS

The News screen: lists of [articles](articles.md) filtered by order, category and country, your subscriptions, and the military feed of automatic events. Both the article lists and the military feed also exist as RSS.

#erepublik #api #media #news #rss

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Article list](#article-list) | GET | `/en/main/news/{sorting}/{category}/{country}/{page}` | ✅ Verified 2026-11-27 |
| [Subscriptions](#subscriptions) | GET | `/en/main/news/subscriptions/{page}` | ✅ Verified 2026-11-27 |
| [Article RSS](#article-rss) | GET | `/en/main/news/{sorting}/{category}/{country}/{page}/rss` | ✅ Verified 2026-11-27 |
| [Military news](#military-news) | GET | `/en/main/news/military/{country}/{page}` | ✅ Verified 2026-11-27 |
| [Military news RSS](#military-news-rss) | GET | `/en/main/news/military/all/{country}/{page}/rss` | ✅ Verified 2026-11-27 |

Headers and cookie: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

> [!NOTE]
> The segment order differs between the three families: article lists use `{category}/{country}`, the military page takes only `{country}`, and the military RSS has a fixed `all` before the country.

---

## Article list

`GET /en/main/news/{sorting}/{category}/{country}/{page}` · Auth optional · ✅ Verified 2026-11-27

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `sorting` | path | string | Yes | `rated` (most voted) or `latest` |
| `category` | path | string | Yes | `all` or a [category](#categories) slug |
| `country` | path | string | Yes | `all` or a country permalink (`Poland`, `Czech-Republic`) |
| `page` | path | number | Yes | `1`–`5`; `0` gives the first page |

```bash
curl 'https://www.erepublik.com/en/main/news/rated/all/all/1'
```

The response is HTML, ten articles per page:

| In each `div.item` | Meaning |
|--------------------|---------|
| `.rankholder .value` | Votes |
| `a.dotted` | Title, linking to `/en/article/{slug}-{articleId}/1/20` |
| `.article_details small` | Relative age, category link, country flag and name |

`/en/main/news/international` shows the same listing as `rated/all/all`.

### Categories

| ID | Name | Slug |
|----|------|------|
| 1 | First steps in eRepublik | `first-steps-in-erepublik` |
| 2 | Battle orders | `battle-orders` |
| 3 | Warfare analysis | `warfare-analysis` |
| 4 | Political debates and analysis | `political-debates-and-analysis` |
| 5 | Financial business | `financial-business` |
| 6 | Social interactions and entertainment | `social-interactions-and-entertainment` |

### Errors

404 for an unknown sorting, category or country, for page `6` and above, and for a missing segment such as `/news/rated/all/1`. `/news/{sorting}/all` without country and page returns 200; it contained no list items for `latest`.

**Source:** real requests, 2026-11-27.

---

## Subscriptions

`GET /en/main/news/subscriptions/{page}` · Auth required · ✅ Verified 2026-11-27

Articles from the newspapers you [subscribe to](newspapers.md#subscribe-or-unsubscribe), in the same markup as the [article list](#article-list), ten per page.

```bash
curl 'https://www.erepublik.com/en/main/news/subscriptions/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN'
```

News pages also carry the list of your subscriptions (`div.asubs li#newspaper_{newspaperId}`, count in `div.your_subs strong`) with an unsubscribe control.

Without a session the response is 404. There is no RSS variant (404).

**Source:** real requests, 2026-11-27.

---

## Article RSS

`GET /en/main/news/{sorting}/{category}/{country}/{page}/rss` · No auth · ✅ Verified 2026-11-27

The [article list](#article-list) as RSS 2.0, with the same parameters.

```bash
curl 'https://www.erepublik.com/en/main/news/latest/all/Poland/1/rss'
```

### Response

Shortened to one item, with placeholders:

```xml
<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0"
  xmlns:content="//purl.org/rss/1.0/modules/content/"
  xmlns:wfw="//wellformedweb.org/CommentAPI/">
  <channel>
    <title><![CDATA[eRepublik News]]></title>
    <link>https://erepublik.com/en/main/news/rated/all/all/1/rss</link>
    <description><![CDATA[eRepublik News]]></description>
    <language>en-us</language>
    <pubDate>Fri, 27 Nov 2026 09:00:00 -0800</pubDate>
    <item>
      <title><![CDATA[Article title]]></title>
      <link>https://www.erepublik.com/en/article/article-title-1000000/1/20</link>
      <guid>https://www.erepublik.com/en/article/article-title-1000000/1/20</guid>
      <pubDate>Sun, 22 Nov 2026 07:12:36 -0800</pubDate>
      <description><![CDATA[First lines of the article...]]></description>
    </item>
  </channel>
</rss>
```

| Field (per `item`) | Type | Description |
|--------------------|------|-------------|
| `title` | string | Article title |
| `link` | string | Article URL |
| `guid` | string | Same as `link` |
| `pubDate` | string | RFC 2822 date, game time |
| `description` | string | Truncated beginning of the text |

The channel title reflects the filters and page: `eRepublik News`, `eRepublik News about battle orders from Poland`, `eRepublik News - page 2`.

### Errors

404 from page `6`. Page `0` gives the first page.

**Source:** real requests without a session, 2026-11-27.

---

## Military news

`GET /en/main/news/military/{country}/{page}` · Auth optional · ✅ Verified 2026-11-27

Events generated by the game: attacks, regions secured or conquered, resistance wars and congress proposals.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `country` | path | string | Yes | `all` or a country permalink |
| `page` | path | number | Yes | `1`–`5`; `6` returns 404 |

```bash
curl 'https://www.erepublik.com/en/main/news/military/all/1'
```

HTML, ten events per page. Each `div.holder.bordersep` has an icon (its `alt` names the event type), a text link and the game day and time. `/en/main/news/military` without parameters also returns ten events.

The page title ends in `from 1` for the country `all`, because the page number is placed where the country name goes.

### Event types

Seen on 2026-11-27 across pages 1–5 for `all` and `Poland`; the list may be incomplete.

| `alt` | Icon | Links to | Example text |
|-------|------|----------|--------------|
| Region under attack | `military_93.gif` | `/en/military/battlefield/{battleId}` | Lithuania attacked Massachusetts, Cyprus |
| Region secured | `military_41.gif` | `/en/wars/show/{warId}` | Silesia was secured by Poland in the war versus Czech Republic |
| Region conquered | `military_40.gif` | `/en/wars/show/{warId}` | South Dakota was conquered by Greece in the war versus USA |
| Resistance war started | `military_42.gif` | `/en/wars/show/{warId}` | A resistance has started in New Brunswick |
| Donation | `political_115.gif` | `/{lang}/main/law/{country}/{lawId}` | Poland made a donation to Narodowy Bank Polski |
| Donation proposal | `political_propose_default.gif` | law page | A congress donation to … was proposed |
| War proposal | `political_propose_default.gif` | law page | President of Romania proposed a war declaration against Croatia |
| Minimum wage proposal | `political_propose_default.gif` | law page | A new minimum wage was proposed |
| Resource Concession proposal | `political_propose_default.gif` | law page | A Resource Concession law to Serbia has been proposed |

Law links do not always use `/en`: the language prefix varied between events, and some links are protocol-relative full URLs.

**Source:** real requests, 2026-11-27.

---

## Military news RSS

`GET /en/main/news/military/all/{country}/{page}/rss` · No auth · ✅ Verified 2026-11-27

The military feed as RSS 2.0.

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `country` | path | string | Yes | `all` or a country permalink |
| `page` | path | number | Yes | `1`–`5`; `0` gives page 1; `6` and above return 404 |

The segment before `country` must be `all`.

```bash
curl 'https://www.erepublik.com/en/main/news/military/all/Lithuania/1/rss'
```

### Response

<details>
<summary>Example response (earlier notes, March 2026)</summary>

```xml
<?xml version="1.0" encoding="utf-8"?>
<rss version="2.0"
  xmlns:content="//purl.org/rss/1.0/modules/content/"
  xmlns:wfw="//wellformedweb.org/CommentAPI/">
  <channel>
    <title><![CDATA[eRepublik News from Lithuania]]></title>
    <link>https://erepublik.com/en/main/news/military/all/Lithuania/1/rss</link>
    <description><![CDATA[eRepublik News from Lithuania]]></description>
    <language>en-us</language>
    <pubDate>Fri, 27 Mar 2026 09:49:07 -0700</pubDate>
    <item>
      <title><![CDATA[Military Feed]]></title>
      <link>https://www.erepublik.com/en/wars/show/219946</link>
      <pubDate>Fri, 27 Mar 2026 08:52:04 -0700</pubDate>
      <description><![CDATA[<a href="//www.erepublik.com/en/wars/show/219946">Pacifica was secured by Latvia in the war versus Lithuania</a>]]></description>
    </item>
    <item>
      <title><![CDATA[Military Feed]]></title>
      <link>https://www.erepublik.com/en/military/battlefield/883261</link>
      <pubDate>Fri, 27 Mar 2026 02:08:25 -0700</pubDate>
      <description><![CDATA[<a href="//www.erepublik.com/en/military/battlefield/883261">Portugal attacked Gulf of Mexico, Lithuania</a>]]></description>
    </item>
  </channel>
</rss>
```

</details>

| Field (per `item`) | Type | Description |
|--------------------|------|-------------|
| `title` | string | Always `Military Feed` |
| `link` | string | War, battlefield or law page |
| `pubDate` | string | RFC 2822 event time |
| `description` | string | HTML link with the event text |

Items have no `guid`. The channel title is `eRepublik News` for `all` and `eRepublik News from {country}` for one country. Ten items per page. Event kinds are those of the [military page](#event-types); earlier notes list region secured, attack launched and congressional actions.

### Errors

404 for an unknown country, a segment other than `all` before the country, or page `6` and above. `/en/main/news/military/{country}/{page}/rss` (without the fixed `all`) is also 404.

**Source:** real requests without a session, 2026-11-27; earlier notes.

---

## Open questions

| Topic | Earlier notes | Observed 2026-11-27 | Page follows |
|-------|---------------|---------------------|--------------|
| Article RSS filters | `category` and `country` must be `all`, other values return HTML | Category and country filters return filtered RSS | Observation |
| Article RSS segment names | `{sorting}/{country}/{category}` | `{sorting}/{category}/{country}`, as in the page links | Observation |
| Session cookie | May personalise results | Requests were made without it; not compared | — |
