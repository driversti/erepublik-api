# Writing a topic page

[API docs](README.md) › Contributing

The template for v2 pages. Writing rules, branches and the pull request checklist are in the [main README](README.md#contributing). A finished example: [Deployment](military/battlefield/deployment.md).

## Page skeleton

Copy, then delete what does not apply. File names are lowercase with hyphens; relative links depend on the folder depth.

````markdown
# Topic name

[API docs](../README.md) › [Module](../../module/README.md) › Topic name

Two or three sentences: which game screen this is and what the endpoints do.

<!-- Optional Mermaid diagram, only when the order of calls matters. -->

| Action | Method | Path | Status |
|--------|--------|------|--------|
| [Get things](#get-things) | GET | `/en/module/things/{id}` | 🔍 From code |

Headers, cookie and `_token`: see [Making requests](../guides/requests.md#common-headers). Status labels are explained [there too](../guides/requests.md#status-labels).

---

## Get things

`GET /en/module/things/{id}` · Auth required · 🔍 From code

Purpose, and when the game client calls it.

### Request

| Name | In | Type | Required | Description |
|------|----|------|----------|-------------|
| `id` | path | number | Yes | What it identifies |

```bash
curl 'https://www.erepublik.com/en/module/things/1' \
  -H 'Cookie: erpk=YOUR_SESSION_TOKEN' \
  -H 'X-Requested-With: XMLHttpRequest'
```

### Response

Shortened:

```json
{ "things": [] }
```

<details>
<summary>Full example response</summary>

```json
{ "things": [] }
```

</details>

| Field | Type | Description |
|-------|------|-------------|
| `things` | array | What it holds |

### Errors

Only the errors that are known.

**Source:** script name (version), function; or the date of the real request.

---

## Open questions

| Topic | Earlier notes | Game code | Page follows |
|-------|---------------|-----------|--------------|
````

## Rules of thumb

- Status ✅ Verified carries the date: `✅ Verified 2026-11-27`.
- Put a `> [!WARNING]` directly under the status line of any action that changes or spends something.
- Migrating an old page keeps all of its information; unclear points go to **Open questions**.
- The **In** column is only needed when parameters travel in different places.
