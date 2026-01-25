# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This is a **documentation repository** for the unofficial eRepublik API, discovered through reverse engineering. It is part of the Knowledge Base (Second Brain) and contains no executable code.

## Structure

- **API_TOC.md** - Table of contents and overview (start here)
- **API_AUTH.md** - Authentication (login, sessions, cookies)
- **API_MILITARY.md** - Combat, battles, wars, weapons
- **API_SOCIAL.md** - Citizens, friends, messages, notifications
- **API_ECONOMY.md** - Marketplace, work, companies, currencies

## Documentation Conventions

- All files use Markdown with GitHub-flavored formatting
- Hashtags for Knowledge Base linking: `#erepublik #api #<module>`
- Each module file includes a template for documenting new endpoints
- Wiki-style links: `[[eRepublik]]` for cross-referencing other KB notes

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
### Example Request (cURL)
### Example Response
### Notes
```

## Key Information

- **Base URL:** `https://www.erepublik.com`
- **Auth:** Cookie-based (`erpk` session token)
- **Common Headers:** `Cookie`, `X-Requested-With: XMLHttpRequest`, `Content-Type`
- **Note:** These are unofficial endpoints that may change without notice
