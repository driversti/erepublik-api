# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This is a **documentation repository** for the unofficial eRepublik API, discovered through reverse engineering. It is part of the Knowledge Base (Second Brain) and contains no executable code.

## Structure

Documentation is organized into domain folders, each with a `README.md` overview and use-case-specific files:

- **README.md** - Master index with quick reference for all endpoints (start here)
- **auth/** - Authentication (login, sessions, cookies)
- **captcha/** - Session verification, anti-bot challenges
- **homepage/** - Main dashboard and embedded data objects
- **world/** - Travel, cities, world map, regions
- **country/** - Society, economy, politics, military, administration pages
- **military/** - Campaigns, battles, deployment, armory, battlefield, websocket
- **social/** - Citizen profiles, friends, wall posts, search
- **notifications/** - Alerts and reports
- **economy/** - Work, marketplace, exchange, companies, inventory
- **rewards/** - Daily objectives, missions, weekly challenges, VIP
- **powerspin/** - Wheel of Fortune
- **reference/** - Country & region ID lookup tables

## Documentation Conventions

- All files use Markdown with GitHub-flavored formatting
- Hashtags for Knowledge Base linking: `#erepublik #api #<module>`
- Each module's README.md includes a template for documenting new endpoints
- Wiki-style links: `[[eRepublik]]` for cross-referencing other KB notes
- cURL examples and JSON responses use collapsible `<details><summary>` blocks
- Cross-references between files use relative paths

## Endpoint Documentation Template

When adding new endpoints, use this structure:

```markdown
## Endpoint Name

**Method:** GET/POST
**URL:** `/en/path/to/endpoint`
**Auth Required:** Yes/No

### Description
### Parameters (table)
### Headers (table)

<details>
<summary>Example Request (cURL)</summary>

```bash
curl ...
```

</details>

<details>
<summary>Example Response</summary>

```json
{ ... }
```

</details>

### Notes
```

## Key Information

- **Base URL:** `https://www.erepublik.com`
- **Auth:** Cookie-based (`erpk` session token)
- **Common Headers:** `Cookie`, `X-Requested-With: XMLHttpRequest`, `Content-Type`
- **Note:** These are unofficial endpoints that may change without notice
