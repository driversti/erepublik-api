# eRepublik API docs

Single source of truth for every coding agent: `AGENTS.md` and `GEMINI.md` are symlinks to this file.

Documentation-only repository (no executable code, nothing to build, test or deploy) for the unofficial eRepublik API, reverse-engineered from the game's web client. Part of the Knowledge Base. Start at `README.md` (master index + endpoint quick reference).

## Layout

Domain folders, each with a `README.md` overview plus use-case files:
`auth/` login/sessions · `captcha/` session verification · `homepage/` dashboard data · `world/` travel, cities, map · `country/` society/economy/politics/military pages · `military/` campaigns, battles, deployment, websocket · `social/` profiles, friends, wall · `notifications/` · `economy/` work, market, companies, inventory · `rewards/` objectives, missions, VIP · `powerspin/` · `framework/` main JS bundle (popups, client objects) · `reference/` country/region ID tables.
`samples/` holds captured game JS for reference and is git-ignored.

## Adding or updating content

- Add a new endpoint to the matching module folder, following [docs/endpoint-template.md](docs/endpoint-template.md); then add it to that module's `README.md` and to the quick-reference tables in the root `README.md` (also update the module file counts there).
- Markdown, GitHub-flavored; relative links between files; hashtags `#erepublik #api #<module>` near the top; wiki links like `[[eRepublik]]` for other KB notes.
- cURL examples and JSON responses go in collapsible `<details><summary>` blocks.
- Never paste real session cookies (`erpk`) or tokens into examples; use placeholders.

## Key facts

- Base URL `https://www.erepublik.com`; auth is cookie-based (`erpk` session token).
- Common headers: `Cookie`, `X-Requested-With: XMLHttpRequest`, `Content-Type`.
- These endpoints are unofficial and may change without notice; mark anything unverified as such.
