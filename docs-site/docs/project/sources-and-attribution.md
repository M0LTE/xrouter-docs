# Sources and attribution

## Credit

XRouter, the program and its documentation, is the work of **Paula G8PZT**. The node
software, its design, its in-program manual and the decades of development behind it are
hers. This site is a re-presentation of that material; every accurate thing in it traces
back to her work or to the wider packet-radio community she is part of. Any errors are
this site's own.

XRouter has been in continuous development since 1996, is free for non-commercial amateur
use, and is closed source.

## Source map

The table below lists the sources used to write and check this site, what each is good
for, and the caveats to keep in mind. Versions, defaults and feature lists were taken from
these, preferring the current, maintained sources over the older mirrors.

| Source | Good for | Caveats |
| --- | --- | --- |
| [In-program manual, mirrored on the OARC wiki](https://wiki.oarc.uk/packet:xrouter) | The canonical reference: command set, configuration directives, system files, subsystems. The current, maintained online copy. | Auto-generated from the author's `MAN`/`HLP` files by a parser, so it mirrors the in-program manual closely but carries a few source typos; individual pages were last revised at different dates. |
| [ohiopacket.org/xrpi mirror](https://ohiopacket.org/xrpi/) | A well-laid-out conceptual reference and a full keyword/command listing; the historic "official XRPi home page". Maintained by **Matt KB8UVN**, who set it up in 2020 when the original author-pointed site went down. | **Stale on versions**, it reflects the v501c (2019) era. Excellent on concepts and structure, unreliable for current build numbers, defaults that have since changed, or recently added features. |
| Original vk2dot.dyndns.org/xrpi site ("Rod's" site) | The author's originally-pointed-to documentation home. | **Mostly dead links** now; effectively superseded by the OARC wiki and the ohiopacket mirror. Listed for provenance, not as a live source. |
| [XRouter support group (groups.io/g/xrouter)](https://groups.io/g/xrouter) | The single most up-to-date source for current builds (in the *Files* area) and for support; where the author and experienced sysops post. | Membership/login required to download. Forum threads are discussion, not formal documentation, corroborate specifics against the manual. |
| [packet-radio.net](https://packet-radio.net/) articles + the XRLin V502 release-notes text | Real-world config detail, release-note feature history, and field captures of services and version strings. | The WordPress site is JavaScript-rendered and often will not return article text to simple fetchers; the linked plain-text release notes are the richer, citable artefact. |
| [Hibby's Packet Radio Guide](https://guide.hibbian.org/) | A modern, practical, beginner-oriented guide, including its own "install/configure XRouter" path, and even-handed software comparisons. | A third-party guide, not an XRouter primary source; good for orientation and newcomer framing. |
| Protocol references, [TAPR AX.25 v2.2 spec](https://www.tapr.org/pdf/AX25.2.2.pdf), [Direwolf](https://github.com/wb2osz/direwolf), [aprs.org](http://www.aprs.org/), [AMPRNet background](https://en.wikipedia.org/wiki/AMPRNet) | The background pages on AX.25, KISS, APRS and IP-over-radio. | General amateur-radio references, not specific to XRouter; used only for fundamentals. |

!!! note "Why the OARC wiki is the primary reference here"
    The OARC wiki's XRouter manual is **auto-generated from Paula G8PZT's own `MAN` and
    `HLP` files** by a parsing script, so it tracks the in-program manual rather than being
    an independent re-write. That is why this site treats it as the canonical online
    reference and defers to it (and to the `MAN` command in the program) on any point of
    fact. OARC wiki content is published under CC BY-SA 4.0.

## How this differs from the licence of the docs themselves

This site's own prose is CC BY 4.0 (see [About these docs](about-these-docs.md#licensing)).
The sources above carry their own licences, notably the OARC wiki (CC BY-SA 4.0) and the
XRouter program itself (closed-source freeware, free for non-commercial amateur use). Citing
and learning from those sources is what this site does; it does not relicense them.

---

**Sources:** [In-program manual on the OARC wiki](https://wiki.oarc.uk/packet:xrouter) ·
[ohiopacket.org XRPi mirror (Matt KB8UVN)](https://ohiopacket.org/xrpi/) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter) ·
[Hibby's Packet Radio Guide](https://guide.hibbian.org/) ·
[TAPR AX.25 v2.2 specification](https://www.tapr.org/pdf/AX25.2.2.pdf)
