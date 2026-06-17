# Interfaces & modems

This section is about getting XRouter onto the air, or onto a link. It covers the ways
XRouter can talk to a radio or to another node, and how each one maps to an **interface
type** in `XROUTER.CFG`. It is written for a sysop who has a node running and now wants to
attach hardware or build a link.

Everything XRouter connects to, a hardware TNC, a soundcard modem, a packet engine, the
internet, is described to the program as an **interface**. An interface says *what kind of
connection this is and how to reach it*; a **port** then sits on top of an interface and is
where AX.25 actually happens (one port usually equals one radio frequency). The two are
configured separately, and the order matters.

!!! warning "Interfaces are defined before ports"
    In `XROUTER.CFG`, every `INTERFACE` block must appear **before** the `PORT` block that
    references it (a port binds to its interface with `INTERFACENUM`). XRouter reads the
    file top to bottom and will reject a port that points at an interface it hasn't seen
    yet. See [Interfaces](../configuration/interfaces.md) and
    [Ports](../configuration/ports.md) for the full block model.

## Which interface do I need?

Start from how your radio or link physically reaches the computer, then pick the matching
interface type. The table maps the common cases to a `TYPE` (and, where relevant, a
`PROTOCOL`) and points to the page that walks through it.

| You have… | Interface `TYPE` | `PROTOCOL` | Page |
| --- | --- | --- | --- |
| A hardware KISS TNC or a **NinoTNC** on a serial/USB port | `ASYNC` | `KISS` | [KISS TNCs](kiss-tncs.md) |
| A **soundcard modem** (Direwolf, UZ7HO SoundModem, QtSoundModem) acting as a KISS TNC | `ASYNC` *or* `TCP` | `KISS` | [Soundcard modems](soundcard-modems.md) |
| An **AGWPE / AGW Packet Engine** (or Direwolf in AGW mode) to drive your radios | `AGW` | n/a  [AGWPE](agwpe.md) |
| An **internet link to another node** (RF can't reach it) | `AXUDP`, `AXIP` or `AXTCP` | n/a  [AXIP / AXUDP / AXTCP](axip-axudp-axtcp.md) |
| A **VARA** (FM/HF) or **ARDOP** modem reached over TCP | (see page) | n/a  [VARA and ARDOP](vara-ardop.md) |

A few notes on choosing:

- **KISS over a wire vs KISS over TCP.** A KISS TNC on a real or virtual serial device uses
  an `ASYNC` interface. A modem that exposes KISS over a TCP socket (Direwolf's KISS-TCP, for
  example) is reached with a `TCP` interface instead. Both speak `PROTOCOL=KISS`; only the
  transport differs. The [soundcard modems](soundcard-modems.md) page covers both.
- **AGW vs KISS for soundcard modems.** Direwolf, UZ7HO SoundModem and QtSoundModem can each
  present *either* a KISS interface *or* an AGW interface. Either works with XRouter, pick
  whichever you find simpler to wire up. If you choose AGW, see [AGWPE](agwpe.md).
- **Internet links need IP enabled.** AXUDP, AXIP and AXTCP are IP-based, so they are subject
  to XRouter's `IPADDRESS` rule (below). VARA and ARDOP are reached over TCP and are affected
  the same way.

!!! danger "Internet and TCP interfaces need a non-zero IPADDRESS"
    If the global `IPADDRESS` is `0.0.0.0` or left undefined, XRouter **deliberately disables
    all IP activity**, AXUDP, AXTCP, AXIP, and TCP-based modem links included. This is an
    intentional security feature. If you don't have an AMPRNet (44Net) address, set a private
    dummy such as `10.1.1.1` so these interfaces work. See
    [Global settings](../configuration/global-settings.md).

## What's not here

XRouter can also do Ethernet/WiFi (`EXTERNAL`), serial SLIP/PPP and tunnels, and it can
*emulate* a TNC or packet engine for other applications. Those are IP- and host-integration
topics rather than radio interfacing; see [The TCP/IP stack](../networking/tcpip.md) and
[Linking nodes and interoperating](../networking/linking-and-interop.md).

---

**Sources:** [In-program manual, OARC wiki: §6 Installation & Configuration](https://wiki.oarc.uk/packet:xrouter:docs:man6)
(interface block model, `TYPE`/`PROTOCOL` values) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter) ·
[ohiopacket.org XRPi mirror: interfaces](https://ohiopacket.org/xrpi/docs/ifaces.htm)
