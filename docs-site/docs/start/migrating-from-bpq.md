# Migrating from BPQ or other node software

This page is for a sysop already running **BPQ32 / LinBPQ** — or another NET/ROM node
program — who is considering XRouter, or who needs the two to interwork. It is meant
to be even-handed: BPQ is the de-facto standard with the larger community and a full
BBS; XRouter is the polished alternative that's strong on routing and quick to
configure. Plenty of networks run both, side by side, without trouble.

## What carries across

Most of what you already know transfers directly, because both speak the same
on-air protocols:

- **Node identity.** A node still has a callsign and an alias. In XRouter these are
  the global `NODECALL` and `NODEALIAS` directives.
- **Ports and radios.** The idea of a port per RF channel is the same. XRouter splits
  it into an `INTERFACE` (the physical or virtual connection) and a `PORT` (the
  point your core attaches to a channel) — see the
  [configuration model](../configuration/index.md).
- **KISS and AGW interfaces.** Hardware KISS TNCs, NinoTNCs and soundcard modems
  (Direwolf, UZ7HO, QtSoundModem) attach much as they do under BPQ. XRouter can be a
  client of an AGW Packet Engine, and can also *emulate* one for other apps.
- **NET/ROM.** Classic NET/ROM nodes-broadcast routing is fully present, so XRouter
  drops into an existing NET/ROM network and exchanges routes with BPQ neighbours.
- **Digipeating**, including APRS N-n digipeating, works as you'd expect.

## What's different

- **`XROUTER.CFG`, not `bpq32.cfg`.** XRouter has its own single config file with its
  own keywords and block structure (`INTERFACE`, `PORT`, `RADIO`, `CONSOLE`, `APPL`,
  `ROUTES`). It's a re-learn, but a small one, and errors are reported with line
  numbers. Start at [Your first XROUTER.CFG](../getting-started/first-config.md) and
  the [configuration index](../configuration/index.md).
- **INP3 is on by default.** Where BPQ routes primarily on NET/ROM quality, XRouter
  also runs the **INP3** link-state extension with round-trip-time measurement, and
  prefers it when available. This is excellent within an INP3 network but needs care
  at the boundary with quality-only neighbours — see the cautions below and
  [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md).
- **NetRom-X service numbers instead of many SSIDs.** Rather than `-2` for the BBS,
  `-11` for chat and so on, XRouter exposes numbered *services* on one callsign
  (service 2 = PMS, service 8 = chat). See
  [NetRom-X service numbers](../networking/netromx-services.md).
- **XRouter's own IP stack.** XRouter carries a complete multi-homed TCP/IP stack of
  its own — community members note IP was designed into XRouter from the start rather
  than grafted on. See [The TCP/IP stack](../networking/tcpip.md).
- **A PMS, not a full BBS.** XRouter has an integral personal mailbox, not a full
  store-and-forward BBS. As the author puts it, "if you want to run a BBS, then BPQ is
  the one for you." To get a full BBS with XRouter, run **FBB** alongside it and
  connect the two via **WA8DED host mode** (`DEDHOST`). See
  [the PMS page](../subsystems/pms-mailbox.md).
- **Closed source.** XRouter is closed source (free for non-commercial amateur use).
  For some sysops that's a non-issue; for others it's the deciding factor. It's worth
  weighing before you commit.

## How to interoperate

You do not have to choose — BPQ and XRouter link happily, over RF or the internet:

- **Internet links** use [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md),
  the same family BPQ uses, so an existing AXUDP partner keeps working. Most reported
  link problems trace to firewall or NAT issues at one end, not the software.
- **Routing at the boundary needs thought.** Mixing INP3 and quality-only neighbours
  can distort routing if left unmanaged: BPQ tends to prefer classic NET/ROM over
  INP3, and other software converts between qualities and run-times differently, which
  can inject misleading qualities. Recent XRouter builds de-rate non-XRouter INP3
  qualities to compensate. Read
  [Linking nodes and interoperating](../networking/linking-and-interop.md) and the
  routing notes on [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md) before you
  bridge two dissimilar networks.

!!! tip "Run both for a while"
    A low-risk way to evaluate XRouter is to stand one up alongside your BPQ node,
    link them with an AXUDP port, and watch how each routes before you migrate any
    users.

---

**Sources:** [XRouter overview, OARC wiki](https://wiki.oarc.uk/packet:xrouter) ·
[Hibby's Packet Radio Guide — software comparison](https://guide.hibbian.org/software/) ·
[INP3/quality interop, groups.io](https://groups.io/g/xrouter/message/4048) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
