# Getting started

This section takes you from nothing to a running XRouter node: choosing the right build for
your hardware, getting hold of it, writing a first `XROUTER.CFG`, and keeping it running. If
you are new to packet radio itself, start with the [basics](../basics/what-is-packet-radio.md)
first; if you already run packet and just want a node on the air, you are in the right place.

## The XRouter family — pick your platform

XRouter is distributed as a single self-contained binary per platform. The names are
historical, but the three current builds share **one codebase** and one feature set; they
differ only in the operating-system glue:

| Build | Platform | Status |
| --- | --- | --- |
| **XrPi** | Raspberry Pi / ARM (Linux) | Current — actively developed |
| **XrLin** | Linux PC, x86 / x86-64 | Current — *same codebase* as XrPi, different CPU |
| **XrWin** | Windows | Current |
| **XR32** | Windows (legacy) | Frozen around 2013; superseded by XrWin |
| **XRouter (DOS)** | DOS | The original; legacy |

XrPi and XrLin are literally the same source compiled for two processor families (the Pi
build adds GPIO support that x86 lacks). On Windows, **XrWin is the current build** — the
older **XR32**, and the `XR32.doc` Word manual that documents it, are legacy.

Choosing is mostly a matter of the hardware in front of you:

- **A Raspberry Pi** — low power, happy running unattended for months — is the classic
  choice for a permanent node. Follow **[Raspberry Pi (XrPi)](raspberry-pi.md)**.
- **An x86 / x86-64 Linux PC** (including small fanless boxes and lightweight distros) uses
  **[Linux PC (XrLin)](linux-pc.md)**. The install model is identical to the Pi.
- **Windows** uses **[Windows (XrWin)](windows.md)**.

The good news is that the choice matters less than it sounds: once installed, the
configuration file and the way you operate the node are essentially the same everywhere, so
the platform pages share most of their content.

## Where to download

The current and most reliable source for binaries and support is the
**[XRouter support group on groups.io](https://groups.io/g/xrouter)** — the latest builds for
every platform live in its **Files** area, and you must join the group to download them. The
[XRouter downloads page on the OARC wiki](https://wiki.oarc.uk/packet:xrouter:downloads)
lists the builds too.

!!! note "Use the groups.io Files area — older mirrors are stale"
    You may come across XRouter on `ohiopacket.org/xrpi` or `vk2dot.dyndns.org/xrpi`. These
    are historic mirrors: useful for *concepts*, but **out of date on versions** (and many
    `vk2dot` links are now dead). XRouter releases briskly — at the time of writing the
    current family is the **v504** series (for example *v504v*, January 2026) — so always get
    binaries from the groups.io Files area rather than a mirror. See
    [Upgrading and versions](../admin/upgrading.md).

## What you need, in general

Across all three platforms the shopping list is the same:

- **A computer** running one of the supported platforms above. XRouter is undemanding — well
  under a megabyte of disk, no special CPU.
- **A callsign and a spare SSID** for the node (for example `-1`), separate from your
  personal station.
- **A way onto the air** — one of:
    - a hardware **KISS** TNC or a **NinoTNC**;
    - a **soundcard modem** such as [Direwolf](../interfaces/soundcard-modems.md),
      UZ7HO SoundModem or QtSoundModem;
    - or, to start without radio, an internet
      [AXIP / AXUDP link](../interfaces/axip-axudp-axtcp.md) to another node.

You do **not** need an installer, root privileges (on Linux, a capability flag does the job —
see the platform pages), or any AMPRNet allocation to begin.

## The path from here

1. **Install and first-run** on your platform —
   [Raspberry Pi](raspberry-pi.md), [Linux PC](linux-pc.md) or [Windows](windows.md).
2. **Write your configuration** — the cornerstone is
   **[Your first XROUTER.CFG](first-config.md)**, a line-by-line walk-through of a minimal
   working node, including the `IPADDRESS` and case-sensitivity gotchas that catch newcomers.
3. **Keep it running** unattended — [Running as a service](running-as-a-service.md).

From there, the [Configuration](../configuration/index.md) section covers
[global settings](../configuration/global-settings.md),
[interfaces](../configuration/interfaces.md) and [ports](../configuration/ports.md) in depth,
and the [Interfaces & modems](../interfaces/index.md) section helps you connect specific
hardware.

---

**Sources:** [XRouter support group (groups.io)](https://groups.io/g/xrouter) ·
[XRouter downloads, OARC wiki](https://wiki.oarc.uk/packet:xrouter:downloads) ·
[in-program manual, OARC wiki](https://wiki.oarc.uk/packet:xrouter) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/) (conceptual, stale on versions)
