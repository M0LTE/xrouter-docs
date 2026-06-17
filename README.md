# XRouter Documentation

An unofficial, community-maintained re-presentation of the documentation for
**XRouter**, Paula G8PZT's AX.25 / NET-ROM / TCP-IP packet-radio node, router,
chat server and APRS digipeater. Built with [MkDocs](https://www.mkdocs.org/) and
[Material for MkDocs](https://squidfunk.github.io/mkdocs-material/), versioned with
[mike](https://github.com/jimporter/mike).

This site is **organised by user journey** and **cited to primary sources**. It is
*not* authoritative: for canonical information, defer to the author and to the
in-program manual. See
[`docs-site/docs/project/about-these-docs.md`](docs-site/docs/project/about-these-docs.md).

> **Written entirely by AI, and not fact-checked by a human.** Every page was written by a
> large language model from primary sources and checked only by that same model. Treat it as
> an unverified secondary source and defer to the author and the in-program manual. See
> [About these docs](docs-site/docs/project/about-these-docs.md#how-this-site-was-written).

## Repository layout

The MkDocs project lives in [`docs-site/`](docs-site/); the repository root is
intentionally **not** the root of the site.

```
.github/workflows/ci.yml   # build check + mike deploy
README.md                  # this file
NOTES.md                   # maintainer notes (not part of the published site)
docs-site/                 # the MkDocs project
  mkdocs.yml               # site config + navigation
  requirements.txt         # mkdocs-material, mike
  docs/                    # all documentation content (Markdown)
    index.md               # home
    start/ basics/ getting-started/ configuration/ interfaces/
    networking/ subsystems/ commands/ admin/ reference/ project/
    stylesheets/extra.css
```

## Quick start

```bash
cd docs-site
python -m venv .venv && source .venv/bin/activate    # optional
pip install -r requirements.txt
mkdocs serve            # live preview at http://127.0.0.1:8000
mkdocs build --strict   # production build into ./site (fails on broken links)
```

## Deployment (mike + GitHub Pages)

[`.github/workflows/ci.yml`](.github/workflows/ci.yml) runs from `docs-site/` and does
two things:

- **On pull requests** it runs `mkdocs build --strict` as a check.
- **On push to `master`** it publishes the build with mike:
  `mike deploy --push --update-aliases 1.0 latest` and `mike set-default --push latest`.

mike publishes to the `gh-pages` branch. After the first successful run, enable
GitHub Pages (Settings → Pages → Deploy from branch → `gh-pages` / `root`). The site
is then served at <https://m0lte.github.io/xrouter-docs/>.

To cut a new documentation version, bump the version label in the workflow (e.g.
`1.0` → `1.1`) so older snapshots remain available through the version switcher in
the page header.

To publish versions manually (from `docs-site/`):

```bash
mike deploy --push --update-aliases 1.0 latest
mike set-default --push latest
mike serve     # preview the versioned site locally
```

## Contributing

See
[`docs-site/docs/project/contributing.md`](docs-site/docs/project/contributing.md). In
short: edit the Markdown under `docs-site/docs/`, run `mkdocs build --strict` locally, and
open a pull request. Corrections that bring the docs closer to the author's behaviour are
especially welcome.

## Licence and attribution

The documentation text is released under **CC BY 4.0**. **XRouter** itself is
copyright **Paula G8PZT** and is free for non-commercial amateur-radio use; it is
closed source. This project is independent of and unaffiliated with the author.
