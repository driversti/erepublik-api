# eRepublik API Documentation

Unofficial API documentation for [eRepublik](https://www.erepublik.com), a browser-based strategy MMO. All endpoints have been discovered through reverse engineering of the game's web client.

> ⚠️ **Disclaimer:** These are unofficial, undocumented endpoints. They may change or break at any time without notice. Use at your own risk.

## What's Inside

| Module | Description |
|--------|-------------|
| [Authentication](API_AUTH.md) | Login flow, sessions, cookies |
| [CAPTCHA & Verification](API_CAPTCHA.md) | Anti-bot challenges, session verification |
| [World & Travel](API_WORLD.md) | Countries, regions, travel, map data |
| [Country](API_COUNTRY.md) | Politics, society, economy, military pages |
| [Military](API_MILITARY.md) | Combat, battles, wars, battlefield data |
| [Social](API_SOCIAL.md) | Citizen profiles, friends, messages |
| [Notifications](API_NOTIFICATIONS.md) | Alerts and reports |
| [Economy](API_ECONOMY.md) | Marketplace, work, companies, currencies |
| [Rewards](API_REWARDS.md) | Daily objectives, missions, weekly challenges |
| [Reference Data](API_REFERENCE.md) | Country & region ID lookup tables (74 countries, 574 regions) |

See [API_TOC.md](API_TOC.md) for a full endpoint quick-reference table.

## Key Details

- **Base URL:** `https://www.erepublik.com`
- **Auth:** Cookie-based sessions (`erpk` token)
- **Common headers:** `Cookie`, `X-Requested-With: XMLHttpRequest`, `Content-Type`

## Example Request

```bash
curl 'https://www.erepublik.com/en/main/citizen-profile-json-global/1234567' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

## Contributing

Found a new endpoint? Contributions are welcome! Each endpoint should be documented with:

- HTTP method and URL
- Required parameters and headers
- Example request (cURL)
- Example response (JSON/HTML)
- Any relevant notes or gotchas

## License

This documentation is provided as-is for educational purposes.
