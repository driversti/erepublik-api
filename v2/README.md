# eRepublik API documentation

Unofficial, community documentation of the HTTP and WebSocket API behind [eRepublik](https://www.erepublik.com), a browser-based strategy game. It is written for developers who build tools, bots and userscripts, and is readable straight from GitHub.

> [!WARNING]
> These endpoints are undocumented by the game's authors, reverse-engineered from the web client, and may change or disappear at any time. Many actions spend real in-game resources. Use at your own risk and respect the game's rules.

> [!NOTE]
> This is **version 2**. Every topic has a v2 page; the previous documentation stays in the repository root until v2 replaces it.

## At a glance

| Metric | Value |
|--------|-------|
| Endpoints in the old quick reference | 102 |
| Endpoints documented in v2 (101 of them from the old quick reference) | 354 |
| Topic pages in v2 | 60 |
| Endpoints ✅ Verified / 🔍 From code / ❔ Unconfirmed | 180 / 161 / 13 |
| Last updated | 2026-11-27 |

## Start here

1. [Making requests](guides/requests.md): cookie, CSRF token, headers, status labels.
2. [Authentication](basics/login.md).
3. Pick a topic below.

Good to know: the game runs on `America/Los_Angeles` time, with a daily reset at 00:00 and a weekly reset on Tuesday at 00:00. The game day counts days since 2007-11-21. The rate limit is about 3000 requests per hour per account.

## Where to find things

| Topic | What is inside | Page |
|-------|----------------|------|
| Login and sessions | Login, logout, cookies | [login](basics/login.md) ✨ v2 |
| Verification | Session CAPTCHA | [captcha](basics/captcha.md) ✨ v2 |
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
| Travel | Travel popup, prices, moving | [travel](world/travel.md) ✨ v2 |
| Map and regions | World map data, side panels, region page | [map](world/map.md) ✨ v2 |
| Cities and residence | City panels, search, residence | [cities](world/cities.md) ✨ v2 |
| Country: society and economy | Citizens, regions, treasury, taxes | [society](country/society.md), [economy](country/economy.md) ✨ v2 |
| Country: politics and military | Government, wars, pacts | [politics](country/politics.md), [military](country/military.md) ✨ v2 |
| Government and laws | Administration, citizenship requests, law voting | [administration](country/administration.md), [laws](country/laws.md) ✨ v2 |
| Elections and parties | Results, candidates, voting | [elections](country/elections.md), [parties](country/parties.md) ✨ v2 |
| Alliances | Members, alliance actions | [alliances](country/alliances.md) ✨ v2 |
| Citizen profiles | Profile data, hovercard | [profiles](social/profiles.md) ✨ v2 |
| Friends and blocking | Friend lists, requests, blocklist | [friends](social/friends.md) ✨ v2 |
| Wall and feeds | Posts, comments, reactions | [wall](social/wall.md) ✨ v2 |
| Private messages | Inbox, compose | [messages](social/messages.md) ✨ v2 |
| Notifications | Alerts, reports, pop-ups | [notifications](social/notifications.md) ✨ v2 |
| Search | Search page, citizen search | [search](social/search.md) ✨ v2 |
| Organizations | Organization profiles, money donations | [organizations](social/organizations.md) ✨ v2 |
| Newspapers and articles | Reading, voting, comments, writing, subscriptions | [articles](media/articles.md), [newspapers](media/newspapers.md) ✨ v2 |
| News | News lists and RSS | [news](media/news.md) ✨ v2 |
| Leaderboards | Weekly boards: citizens, units, countries | [leaderboards](rankings/leaderboards.md) ✨ v2 |
| Rankings and updates | Ranking pages, Latest Updates | [rankings](rankings/rankings.md) ✨ v2 |
| Shop | Packs, items, purchases | [shop](economy/shop.md) ✨ v2 |
| Events | Endpoints and data of seasonal events | [An Amazing Journey](events/amazing-journey.md), [Black Friday](events/black-friday.md), [Tournaments](events/tournaments.md), [dormant event code](events/dormant-code.md) ✨ v2 |
| Other shop screens | Gold Store, Supply Shop, gifting, promo popups | [shop screens](economy/shop-screens.md) ✨ v2 |
| VIP and loyalty | VIP points, Loyalty Program, Gold Bonus | [VIP](economy/vip.md) ✨ v2 |
| Work | Job, Work as Manager, overtime | [work](economy/work.md) ✨ v2 |
| Training | Training grounds, upgrades | [training](economy/training.md) ✨ v2 |
| Job market | Offers, apply, resign | [job market](economy/job-market.md) ✨ v2 |
| Employees | Job offer, salaries, staff | [employees](economy/employees.md) ✨ v2 |
| Companies | Companies, holdings, building | [companies](economy/companies.md) ✨ v2 |
| Inventory | Storage, boosters, houses, assembly | [inventory](economy/inventory.md) ✨ v2 |
| Marketplace | Goods, own offers, price snapshot | [marketplace](economy/marketplace.md) ✨ v2 |
| Monetary market | Gold and currency exchange | [monetary market](economy/monetary-market.md) ✨ v2 |
| Game Tokens market | Token offers | [Game Tokens market](economy/game-tokens-market.md) ✨ v2 |
| Client framework | Popups, JavaScript objects, site-wide requests | [framework](basics/framework.md) ✨ v2 |
| Real-time WebSocket | Battle and deployment events | [websocket](basics/websocket.md) ✨ v2 |
| ID tables | Countries and regions | [ID tables](basics/id-tables.md) ✨ v2 |

## Looking for a game screen?

| In the game you want to… | Go to |
|--------------------------|-------|
| Deploy in a battle | [Deployment](military/battlefield/deployment.md) |
| Browse battles (Wars) | [Campaigns](military/campaigns.md) |
| Manage vehicles (Armory) | [Armory](military/armory.md) |
| Run your military unit | [Military units](military/military-units.md) |
| Train | [Training](economy/training.md) |
| Work | [Work](economy/work.md) |
| Collect daily rewards | [Daily objectives](home/daily-objectives.md), [daily missions](home/daily-missions.md) |
| Travel | [Travel](world/travel.md) |
| Vote | [Elections](country/elections.md#vote) |
| Donate to a country | [Country economy](country/economy.md#donate-to-the-country) |
| Buy in the shop | [Shop](economy/shop.md) |
| Play the anniversary map | [An Amazing Journey](events/amazing-journey.md) |

## How the repository is organised

```
v2/
  README.md             you are here
  guides/               concepts shared by many endpoints
  basics/               login, CAPTCHA, client framework, WebSocket, IDs
  home/                 dashboard and daily routine
  military/
    battlefield/        one page per battlefield topic
  economy/              work, companies, markets, shop
  world/                travel, map, cities
  country/              society, government, elections, alliances
  social/               profiles, wall, messages, organizations
  media/                articles, newspapers, news
  rankings/             leaderboards and ranking pages
  events/               seasonal events and tournaments
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
