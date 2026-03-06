# Pomelo WebSocket

#erepublik #api #military #websocket #pomelo

[< Back to Military Module](README.md)

Real-time battle updates via WebSocket connection.

**Related:** [battlefield.md](battlefield.md) | [battlefield-ajax.md](battlefield-ajax.md)

---

## Pomelo WebSocket (Real-time Battle Updates)

**Gateway:** `gate.erepublik.com:3050`
**Auth Token:** Same as `erpk` session cookie value

### Description

The battlefield page establishes a WebSocket connection via the Pomelo framework for real-time updates. This provides:

- Live hit notifications (who hit whom, damage dealt)
- Domination bar updates
- Battle hero changes
- Zone won/lost events
- Deployment progress updates

### Configuration (from page source)

<details>
<summary>Pomelo Configuration JSON</summary>

```json
{
  "pomelo": {
    "gate": "gate.erepublik.com",
    "port": 3050,
    "authToken": "YOUR_SESSION_TOKEN"
  }
}
```

</details>

### JavaScript Bundle

The Pomelo client is loaded from: `//www.erepublik.net/js/pomelo/lib/build/build.{timestamp}.js`

### Notes

- The Pomelo connection is what enables the live battlefield animation (players appearing, shooting, damage numbers)
- Without WebSocket, you'd need to poll `/en/military/battle-console` for updates (see [Battlefield AJAX Endpoints](battlefield-ajax.md))
- The `authToken` matches the `erpk` cookie value
