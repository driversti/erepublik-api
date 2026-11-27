# eRepublik API documentation

Unofficial, community documentation of the HTTP and WebSocket API behind [eRepublik](https://www.erepublik.com), a browser-based strategy game. It is written for developers who build tools, bots and userscripts, and is readable straight from GitHub.

> [!WARNING]
> These endpoints are undocumented by the game's authors, reverse-engineered from the web client, and may change or disappear at any time. Many actions spend real in-game resources. Use at your own risk and respect the game's rules.

> [!NOTE]
> This is **version 2**, being migrated page by page. Anything marked "old" below links to the previous documentation.

## At a glance

| Metric | Value |
|--------|-------|
| Endpoints in the old quick reference | 102 |
| Endpoints documented in v2 (45 of them from the old quick reference) | 87 |
| Topic pages in v2 | 18 |
| Endpoints ✅ Verified / 🔍 From code / ❔ Unconfirmed | 46 / 37 / 4 |
| Last updated | 2026-11-27 |

## Start here

1. [Making requests](guides/requests.md): cookie, CSRF token, headers, status labels.
2. [Authentication](../auth/README.md) (old).
3. Pick a topic below.

Good to know: the game runs on `America/Los_Angeles` time, with a daily reset at 00:00 and a weekly reset on Tuesday at 00:00. The game day counts days since 2007-11-21. The rate limit is about 3000 requests per hour per account.

## Where to find things

| Topic | What is inside | Page |
|-------|----------------|------|
| Login and sessions | Login flow, cookies | [auth](../auth/README.md) (old) |
| Verification | CAPTCHA and session unlock | [captcha](../captcha/README.md) (old) |
| Dashboard | Home page data, news | [dashboard](home/dashboard.md) ✨ v2 |
| Daily objectives | Activity chests | [daily objectives](home/daily-objectives.md) ✨ v2 |
| Daily missions | Mission list and claim | [daily missions](home/daily-missions.md) ✨ v2 |
| Weekly challenge | Prestige reward ladder | [weekly challenge](home/weekly-challenge.md) ✨ v2 |
| Energy and food | Refill energy | [energy](home/energy.md) ✨ v2 |
| Power Spin | Wheel of Fortune | [power spin](home/power-spin.md) ✨ v2 |
| Badges and auto-posts | Referral banners, award posts | [badges](home/badges.md) ✨ v2 |
| Campaigns (Wars screen) | Battle lists, filters, presets | [campaigns](military/campaigns.md) ✨ v2 |
| Battlefield: page and entry | Battle state, side, travel, division | [page and entry](military/battlefield/page.md) ✨ v2 |
| Battlefield: live data | Domination, battle console, campaign map | [live data](military/battlefield/live-data.md) ✨ v2 |
| Battlefield: deployment | Automated fighting, fuel | [deployment](military/battlefield/deployment.md) ✨ v2 |
| Battlefield: boosters, bombs, PvP | Boosters, bombs, battle effects, PvP | [boosters, bombs, PvP](military/battlefield/boosters-bombs-pvp.md) ✨ v2 |
| Wars and resistance wars | War page, resistance funding, battle end time | [wars](military/wars.md) ✨ v2 |
| Armory and protectors | Vehicles, enrollment, protector levels | [armory](military/armory.md) ✨ v2 |
| Military units | Unit page, members, finances, management | [military units](military/military-units.md) ✨ v2 |
| Military: everything else | News feed | [military](../military/README.md) (old) |
| World and travel | Map, regions, cities | [world](../world/README.md) (old) |
| Country | Society, economy, politics, laws | [country](../country/README.md) (old) |
| Social | Profiles, friends, wall, search | [social](../social/README.md) (old) |
| Notifications | Alerts and reports | [notifications](../notifications/README.md) (old) |
| Shop | Packs, items, purchases | [shop](economy/shop.md) ✨ v2 |
| Events | Endpoints and data that exist only during an event | [An Amazing Journey](events/amazing-journey.md), [Black Friday](events/black-friday.md) ✨ v2 |
| Economy: everything else | Work, training, markets, companies, inventory | [economy](../economy/README.md) (old) |
| Rewards: everything else | VIP | [rewards](../rewards/README.md) (old) |
| Client framework | Popups, JavaScript objects | [framework](../framework/README.md) (old) |
| ID tables | Countries and regions | [reference](../reference/README.md) (old) |

## Looking for a game screen?

| In the game you want to… | Go to |
|--------------------------|-------|
| Deploy in a battle | [Deployment](military/battlefield/deployment.md) |
| Browse battles (Wars) | [Campaigns](military/campaigns.md) |
| Manage vehicles (Armory) | [Armory](military/armory.md) |
| Run your military unit | [Military units](military/military-units.md) |
| Train | [Training grounds](../economy/training.md) (old) |
| Work | [Work](../economy/work.md) (old) |
| Collect daily rewards | [Daily objectives](home/daily-objectives.md), [daily missions](home/daily-missions.md) |
| Travel | [Travel](../world/travel.md) (old) |
| Buy in the shop | [Shop](economy/shop.md) |
| Play the anniversary map | [An Amazing Journey](events/amazing-journey.md) |

## How the repository is organised

```
v2/
  README.md            you are here
  guides/              concepts shared by many endpoints
  home/                dashboard and daily routine
  military/
    battlefield/       one page per battlefield topic
  economy/             work, markets, shop
  events/              seasonal and one-day events
```

- One folder per game area, matching the topics above.
- **One page per topic or game screen**; related endpoints are described together.
- A sub-folder appears only when a topic does not fit on one comfortable page.
- **Each endpoint is described exactly once**; every other place links to it.
- In-game menus are mirrored with links, not folders.

Design decisions are recorded in [docs/superpowers/specs](../docs/superpowers/specs/) (in Ukrainian).

## How to read a topic page

1. A short intro, often with a flow diagram.
2. A table of the page's endpoints with method, path and status.
3. One section per endpoint: request, response, errors, pitfalls and **Source**.
4. **Open questions**, where sources disagree.

| Status | Meaning |
|--------|---------|
| ✅ Verified | Confirmed with a real request on the given date |
| 🔍 From code | Derived from the game's JavaScript, not confirmed by a request |
| ❔ Unconfirmed | Origin unknown |

## Reading locally

There is nothing to install or build.

```bash
git clone https://github.com/driversti/erepublik-api.git
```

Open the folder in any editor with a Markdown preview. Collapsed examples, diagrams and notes are designed for GitHub, so some editors show them differently.

## Contributing

Found a new endpoint or a mistake? Open a pull request or an issue. New pages follow the [page template](CONTRIBUTING.md).

### Writing rules

- English, GitHub-flavored Markdown, relative links.
- For each endpoint give: method and path, auth, status, purpose, parameters, a short cURL, a shortened response with a field table (full response inside `<details>`), known errors and the source.
- Link to [Making requests](guides/requests.md) instead of repeating common headers.
- Put a `> [!WARNING]` above actions that spend resources.
- Never invent behaviour. If sources disagree, add it to **Open questions**.
- **Never commit real cookies or tokens**, and set currency and gold balances in examples to `0` (shop prices stay).

### Branches (during the v2 migration)

| Change | Where |
|--------|-------|
| Small fix | Commit to `docs/v2` |
| Larger work (a new page or module) | Branch `docs/v2-<topic>`, merged into `docs/v2` |
| Migration finished | `docs/v2` is merged into `main` and replaces the old files |

Outside contributors: open a pull request against `docs/v2`.

### Before you open a pull request

- [ ] The endpoint is described only once.
- [ ] Links work in the GitHub preview.
- [ ] Status and source are filled in.
- [ ] No secrets or personal balances.
- [ ] The metrics above are updated.
