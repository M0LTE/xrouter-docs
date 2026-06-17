# Project notes — XRouter documentation (gaps, open questions, methodology)

This file is for *you* (the maintainer), not the published site. It records how the docs
were built, what is solid, what is uncertain, and what to do before/after publishing.

## What this is

A from-scratch, cited re-presentation of XRouter's documentation as a MkDocs Material site,
organised by user journey, covering the whole family (XrPi / XrLin / XrWin, with legacy
XR32 / DOS noted). 53 content pages across Start / Basics / Getting started / Configuration /
Interfaces / Networking / Subsystems / Commands / Administration / Reference / Project.

## How it was researched and checked

- **Primary sources mined:** the in-program manual mirrored on the OARC wiki
  (`wiki.oarc.uk/packet:xrouter:docs`, auto-generated from the author's MAN/HLP files — the
  most complete current reference); the author's `ohiopacket.org/xrpi` mirror (good on
  concepts, stale on versions); the XRouter support group `groups.io/g/xrouter` (current
  builds + the author's own guidance); packet-radio.net articles incl. the V502 release
  notes; Hibby's Packet Radio Guide; and protocol references (TAPR AX.25, etc.) for the
  beginner pages. Community experience was drawn from the groups.io archive and the OARC
  packet Discord.
- **Fact-checking:** every page was written from a distilled research corpus, then put through
  three independent adversarial review passes (config/interfaces; networking/subsystems/
  commands; journeys/basics/admin + a site-wide scope sweep). Flagged invented specifics,
  false-precision defaults, mis-citations and one altered quote were corrected. The site
  builds clean under `mkdocs build --strict` (no broken internal links).
- **Citations:** durable links (groups.io permalinks, wiki.oarc.uk, ohiopacket) appear in
  per-page **Sources** lines. OARC Discord is not publicly linkable, so Discord-sourced facts
  were corroborated against durable sources rather than linked.

## Scope decisions honoured (per your brief)

- **No specific deployments.** No OARC/club or individual node deployment is described.
  Examples use placeholder callsigns (`M0XXX`, `G0XYZ`, `GB7XYZ`, etc.).
- **No security findings.** Nothing about the private review is referenced. Hardening is
  covered generically (sysop passwords, `SYSOP=1` cautions, the `IPADDRESS` security
  behaviour, non-root via `setcap`, ACLs/IDS at a high level, closing unused servers).

## Publishing status

1. **Git** — done. The repo was initialised and published to
   [`github.com/m0lte/xrouter-docs`](https://github.com/m0lte/xrouter-docs) (public).
2. **`mkdocs.yml`** — done. `site_url`, `repo_url` and `repo_name` now point at
   `m0lte/xrouter-docs` (the "edit" pencil and canonical URL resolve correctly).
3. **Layout** — the MkDocs project lives in `docs-site/`; the repo root is intentionally
   not the site root. CI runs from `docs-site/` (see `.github/workflows/ci.yml`).
4. **First deploy**: on push to `master` the CI runs `mike deploy 1.0 latest`. After the
   first successful run, enable GitHub Pages on the `gh-pages` branch (Settings → Pages →
   Deploy from branch → `gh-pages` / `root`). See `README.md`.
5. **Version label**: the CI publishes docs version `1.0`. Decide whether to track XRouter
   releases (e.g. bump to match a v504/v505 series) and bump the label when you cut a new
   snapshot so the version switcher keeps older versions available.

## Known gaps and things to verify against your own build

These are deliberately hedged in the text; confirm against the in-program `HELP`/`MAN` and
your actual binary, which are authoritative:

- **Windows (XrWin) specifics are thin.** The legacy `XR32.doc` is a binary Word file that
  could not be parsed with the available tools, and current XrWin install/service mechanics
  are lightly documented upstream. The Windows page covers what's supported (current build vs
  legacy XR32, `COMn` serial, `RESPTIME=200`) and defers install specifics to the groups.io
  group. Consider expanding this once you can confirm steps.
- **Conflicting defaults across manual versions** are shown as "varies / see note" rather than
  asserted: e.g. `RESPTIME` (1500 vs 2000 ms), `CHATQUAL` (150 vs 255), `MAXTT`, `MMASK`
  representation, `SESSLIMIT`. Verify the ones that matter to you.
- **Directives/commands flagged "unverified"**: a handful of directive and command names come
  from a single source or a manual placeholder (the command reference marks these
  "(syntax unverified)"; the directive reference notes single-source items). Confirm names and
  syntax against the program's own `HELP`.
- **Service numbers**: taken from the manual's service list (man9). One number (18 = SMS)
  conflicts with a stray packet-capture reading elsewhere; the manual value was used.
- **REST/MQTT surface**: endpoint paths (`/api/v1/...`) and the MQTT topic pattern come from
  the manual; confirm against your build's actual API, which evolves.
- **Direwolf KISS-over-TCP port (8001)** is Direwolf's own setting, not an XRouter default —
  confirm in your Direwolf config. **VARA/ARDOP** coverage is intentionally minimal (only the
  documented `TNC <port>` attach), as upstream specifics are sparse.
- **Versions move fast.** Research reflects the ~v504 series (e.g. v504v, Jan 2026). Treat
  exact build strings as examples, not canonical.

## Suggested next steps

- Invite **Paula G8PZT** (or the groups.io community) to review for technical accuracy; the
  site is explicitly framed as deferring to the author, and a correction pass would strengthen it.
- Flesh out the Windows page and add screenshots of the console/web UI where helpful.
- Once live, link the site from the groups.io group and the OARC wiki if welcome.

## Build/preview cheatsheet

```bash
cd docs-site
pip install -r requirements.txt
mkdocs serve            # preview at http://127.0.0.1:8000
mkdocs build --strict   # production build (fails on broken links)
```

## Research corpus

The distilled research notes used to write the site (with source URLs and the exact
keyword/command/service tables) were produced during the build. They are not part of the
published site; ask if you'd like them exported alongside the repo for future maintenance.
