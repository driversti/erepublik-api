# News RSS Feed

#erepublik #api #social #news #rss

Endpoint for retrieving player-written news articles as an RSS feed.

[< Back to Social API](README.md)

**Related:** [Citizen Profiles](profiles.md) | [Search Citizens](search.md)

---

## News RSS Feed

**Method:** GET
**URL:** `/en/main/news/{sorting}/{country}/{category}/{page}/rss`
**Auth Required:** No (public, but session cookie may affect results)

### Description

Returns an RSS/XML feed of news articles (player-written newspapers). Provides article titles, links, publication dates, and description excerpts. Useful for building news readers, monitoring political developments, or tracking player-generated content.

### Path Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| sorting | string | Yes | Sort order: `rated` (by votes/endorsements) or `latest` (newest first) |
| country | string | Yes | Must be `all` -- country-specific values return HTML instead of RSS |
| category | string | Yes | Must be `all` -- category-specific values return HTML instead of RSS |
| page | number | Yes | Page number (starts at 1) |

### Headers

| Header | Value | Required |
|--------|-------|----------|
| Cookie | `erpk=YOUR_SESSION_TOKEN` | No (may personalize results) |

<details>
<summary>Example Request (cURL)</summary>

```bash
curl 'https://www.erepublik.com/en/main/news/rated/all/all/1/rss'
```

</details>

<details>
<summary>Example Response</summary>

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
    <pubDate>Fri, 13 Feb 2026 20:38:50 -0800</pubDate>
    <item>
      <title><![CDATA[Gold farming, Feb 1-8]]></title>
      <link>https://www.erepublik.com/en/article/gold-farming-feb-1-8-2794000/1/20</link>
      <guid>https://www.erepublik.com/en/article/gold-farming-feb-1-8-2794000/1/20</guid>
      <pubDate>Sun, 08 Feb 2026 00:36:08 -0800</pubDate>
      <description><![CDATA[Greetings! I decided to publish another data slice...]]></description>
    </item>
  </channel>
</rss>
```

</details>

### Response Fields (per `<item>`)

| Field | Type | Description |
|-------|------|-------------|
| `title` | string | Article title (CDATA-wrapped) |
| `link` | string | Full URL to the article page |
| `guid` | string | Unique article identifier (same as link) |
| `pubDate` | string | RFC 2822 publication date |
| `description` | string | Article excerpt/summary (CDATA-wrapped, truncated) |

### Notes

- **RSS 2.0 format**: Standard RSS with `content` and `wfw` XML namespace extensions
- **Article URL pattern**: `/en/article/{slug}-{articleId}/{page}/{commentsPerPage}`
- **Sorting**: Two sort orders work: `rated` (by votes) and `latest` (newest first). Other values return 404
- **No filtering** (for article feeds): Country and category path segments must be `all` -- specific values return HTML pages instead of RSS. A separate **military event feed** supports both global (`all`) and country-specific filtering: see [Military News RSS Feed](../military/wars.md#military-news-rss-feed)
- **Pagination**: Use `page` parameter to get older articles. Page `0` is treated as equivalent to page `1` (identical results)
- **Public endpoint**: Works without authentication, but results may differ with a session cookie
- **Use cases**: News aggregation, monitoring political articles, tracking community developments
