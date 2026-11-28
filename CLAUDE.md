# eRepublik API docs

Single source of truth for every coding agent: `AGENTS.md` and `GEMINI.md` are symlinks to this file.

Documentation-only repository (no executable code, nothing to build, test or deploy) for the unofficial eRepublik API, reverse-engineered from the game's web client. Part of the Knowledge Base. Start at `README.md` (master index + endpoint quick reference).

## Layout

Topic pages live in domain folders: `basics/` login, CAPTCHA, client framework, WebSocket, ID tables · `guides/` shared request rules · `home/` dashboard and daily routine · `military/` (with `battlefield/`) · `economy/` · `world/` · `country/` · `social/` · `media/` articles and news · `rankings/` · `events/` seasonal events.
`docs/` holds design notes; `samples/` holds captured game JS for reference and is git-ignored.

## Adding or updating content

- Follow the page template in [CONTRIBUTING.md](CONTRIBUTING.md) and the writing rules in [README.md](README.md#contributing).
- One page per topic or game screen; each endpoint is described exactly once, other places link to it.
- A new page also goes into the tables in the root `README.md`; update its metrics when endpoint counts change.
- Give every endpoint a status (✅ Verified / 🔍 From code / ❔ Unconfirmed) and a source.
- Headers and `_token` are described once in [guides/requests.md](guides/requests.md).
- Never commit real session cookies (`erpk`) or tokens; currency and gold balances are always `0`.

## Key facts

- Base URL `https://www.erepublik.com`; auth is cookie-based (`erpk` session token).
- Common headers: see [guides/requests.md](guides/requests.md#common-headers).
- These endpoints are unofficial and may change without notice.
