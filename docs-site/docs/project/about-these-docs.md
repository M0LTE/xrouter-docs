# About these docs

This page explains what this site is, how it was built, and — just as important — what
it is *not*. Read it before you trust anything here over the author's own words.

## What this is

This is an **unofficial, community re-presentation** of XRouter's documentation. XRouter
is the packet-radio node software written and maintained by **Paula G8PZT**; she is the
author of both the program and its documentation, and the credit for everything XRouter
does belongs to her. This site does not replace her work. It re-organises and re-explains
it for a reader who is trying to *do* something — get on the air, write a config, fix a
link — rather than for a reader paging through reference sections in order.

Concretely, that means:

- The site is arranged **by user journey** (start here, basics, getting started,
  configuration, subsystems, administration) instead of by manual section number.
- The prose is **original**. Nothing here is copied from the in-program manual or any
  mirror; it is written from scratch to be faithful to how XRouter actually behaves.
- Where it helps, the writing is **seasoned with community experience** — the kinds of
  gotchas and good practice that come up repeatedly in the support group — clearly framed
  as practice, not protocol.

## How it was fact-checked

Every page was written against primary sources and cross-checked, not from memory:

- **The in-program manual** (the `MAN`/`HLP` pages built into XRouter), as
  [mirrored on the OARC wiki](https://wiki.oarc.uk/packet:xrouter:docs). This is the
  closest thing to a canonical reference and is auto-generated from Paula's own files.
- **The author's mirrors** — the historic [ohiopacket.org XRPi site](https://ohiopacket.org/xrpi/)
  and the original vk2dot site — used for concepts and the keyword reference, with the
  knowledge that they lag behind on versions.
- **The support group** at [groups.io/g/xrouter](https://groups.io/g/xrouter), which has
  the current builds and where the author and experienced sysops answer questions.
- **Protocol references** (the TAPR AX.25 specification, Direwolf's documentation and
  similar) for the background pages on AX.25, KISS, APRS and IP-over-radio.

See [Sources and attribution](sources-and-attribution.md) for the full source map, what
each source is good for, and its caveats. Specific keyword names, default values, port
numbers and version strings are only stated where a source supports them; where the
research could not pin something down, the page says so or omits it.

!!! warning "This site is not authoritative"
    XRouter is closed-source freeware under brisk, continuous development, and its defaults
    and features shift between builds. Where this site disagrees with Paula G8PZT or with
    the in-program manual, **the author and the manual are right** — treat any conflict as
    a bug in *these docs* and please report it. The living reference for your build is the
    `MAN` command inside the program.

## Reporting errors

Corrections are genuinely welcome — an unofficial site only stays useful if readers fix it.
If something here is wrong, out of date, or unclear:

- The quickest route is to open an issue or pull request against the documentation
  repository, or to raise it on the [XRouter support group](https://groups.io/g/xrouter).
- See [Contributing](contributing.md) for how to edit a page, build the site locally, and
  the policy we follow when the docs and the author disagree.

## Licensing

Two different things are licensed two different ways, and it matters:

- **This documentation** (the prose, structure and examples on this site) is released under
  the **Creative Commons Attribution 4.0 International (CC BY 4.0)** licence. You may reuse
  and adapt it with attribution.
- **XRouter itself** is **closed-source freeware**, copyright Paula G8PZT, and is **free for
  non-commercial amateur use**. Its licence forbids charging for it and forbids reverse
  engineering; there are no plans to release its source. Nothing on this site grants any
  right over the program — only over these docs.

---

**Sources:** [In-program manual mirrored on the OARC wiki](https://wiki.oarc.uk/packet:xrouter) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
