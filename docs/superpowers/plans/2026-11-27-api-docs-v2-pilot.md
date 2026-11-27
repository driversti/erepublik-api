# API docs v2: Deployment Pilot Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** One finished topic page (battle deployment) that the author can judge on GitHub before anything else is migrated.

**Architecture:** Hand-written Markdown under `v2/`. Shared request conventions live in one guide; the topic page links to it. Old files stay untouched.

**Tech Stack:** GitHub-flavored Markdown, Mermaid. No generators or build scripts.

**Spec:** `docs/superpowers/specs/2026-11-27-api-docs-v2-design.md` (Ukrainian)

## Global Constraints

- Docs in English; only what GitHub renders; relative links.
- Each endpoint described exactly once; other places link to it.
- No real cookies or tokens; currency and gold balances are always `0` (shop prices stay).
- Status per endpoint: `✅ Verified` (real request, with date), `🔍 From code`, `❔ Unconfirmed`.
- Migrate without losing information; mark contradictions, never guess.
- `> [!WARNING]` on actions that spend resources.
- Git: small changes go straight to `docs/v2`; large ones on `docs/v2-<topic>`, merged into `docs/v2` after the author's OK. No push.
- No live requests to the game.

## Review Focus

- Old sections disagree about `startDeploy` parameter names: the page must show what the game code sends, and list what the code does not settle.
- A reader copies a cURL that spends fuel, energy or weapons: a warning sits above it.
- An old fact has no home on the new page: it is moved, not dropped.
- Anchor and relative links work on GitHub, not only in an editor.
- A "From code" claim cannot be traced: its Source line names file and function.

---

### Task 1: Request conventions guide

**Files:**
- Create: `v2/guides/requests.md`

**Interfaces:**
- Produces: anchors `#base-url`, `#session-cookie`, `#csrf-token`, `#common-headers`, `#status-labels`.

- [ ] **Step 1:** Write the page from root `README.md` ("Common Headers"), `auth/README.md` and the constraints above: those five headings, nothing else.
- [ ] **Step 2:** Check: `grep -c '^## ' v2/guides/requests.md` prints `5`.

### Task 2: Deployment topic page

**Files:**
- Create: `v2/military/battlefield/deployment.md`
- Read: `military/deployment.md`, `military/frontend-reference.md`, deployment mentions in `framework/endpoints.md` and `military/README.md`, `samples/erpk_angular_controllers.1772186525.js`, `samples/erepublik.1772186520.js`

**Interfaces:**
- Consumes: anchors from Task 1 as `../../guides/requests.md#…`.

Page order: title and breadcrumb; 2–3 sentence intro; Mermaid flow (inventory → start → cancel or finish → report); endpoint table (method, URL, action, status); one section each for `fightDeploy-getInventory`, `fightDeploy-startDeploy`, `fightDeploy-cancelDeploy`, `fightDeploy-deployReportPopup`; "Fuel" (mechanics plus reading it from `/en/military/campaigns`).

Each endpoint section: method and URL, auth, status; purpose; parameter table and short cURL; shortened response with field table, full response in `<details>`; errors if known; pitfalls; source (file, function, file version date).

- [ ] **Step 1:** In the JS samples, find each of the four calls and record the exact parameters sent and response fields used.
- [ ] **Step 2:** Write the page. Merge both `startDeploy` sections into one using Step 1; anything the code does not settle goes in a "Open questions" list under that endpoint.
- [ ] **Step 3:** Endpoint headings are unique: `grep -c '^## .*' v2/military/battlefield/deployment.md` matches the planned sections, and each of the four URLs has exactly one section.
- [ ] **Step 4:** Compare old against new: every parameter and response field named in `military/deployment.md` is on the page or listed under "Open questions" as wrong, with the evidence.
- [ ] **Step 5:** Secrets: `grep -nE 'erpk=[A-Za-z0-9]{16,}|cf_clearance=[A-Za-z0-9._-]{20,}' -r v2` prints nothing.
- [ ] **Step 6:** Every relative link target exists and every anchor matches a heading in its file.
- [ ] **Step 7:** Commit on `docs/v2-pilot`; show the page to the author before merging.

### Task 3: Main README

**Files:**
- Create: `v2/README.md`

**Interfaces:**
- Consumes: paths from Tasks 1 and 2.

- [ ] **Step 1:** Write sections: about and disclaimer; metrics; repository map (marking what is not migrated yet, with links to the old module); game menu links; how to read a page (status labels link to the guide); authoring rules; reading locally; contributing (branches, checklist).
- [ ] **Step 2:** Length: `wc -w v2/README.md` is at most 1200.
- [ ] **Step 3:** Metric numbers match the files; every relative link target exists.

---

After approval, a separate plan covers `v2/CONTRIBUTING.md` and the remaining modules.
