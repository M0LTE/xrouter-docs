# New to XRouter

This page is the fast path for someone who already understands packet, AX.25,
NET/ROM, KISS, the sysop role, and just wants XRouter up and configured. It points
you straight at the practical pages and summarises what makes XRouter different from
node software you may already know.

## The fast path

1. **Install for your platform.** Start at
   [Getting started](../getting-started/index.md) and follow the page for
   [Raspberry Pi (XrPi)](../getting-started/raspberry-pi.md),
   [Linux PC (XrLin)](../getting-started/linux-pc.md) or
   [Windows (XrWin)](../getting-started/windows.md). XRouter is a single
   self-contained binary with no installer; you'll be running in minutes.
2. **Write the config.** Everything lives in one file. Work through
   [Your first XROUTER.CFG](../getting-started/first-config.md), then the
   [configuration model](../configuration/index.md) for the full picture of GLOBAL
   settings and the `INTERFACE`, `PORT`, `RADIO`, `CONSOLE`, `APPL` and `ROUTES`
   blocks.
3. **Learn the prompt.** Drive the node from the
   [command prompt](../commands/node-prompt.md); the
   [command reference](../commands/command-reference.md) and
   [sysop access](../commands/sysop.md) pages cover the rest.

!!! tip "The config is one file, read once at startup"
    XRouter reads `XROUTER.CFG` only at program start and reports errors **with the
    line number**, so the workflow is edit-one-thing, restart, repeat. Interfaces
    must be defined **before** the ports that reference them. On Linux, config and
    data file names are **UPPER CASE** and case-sensitive.

## What's distinctive about XRouter

If you're coming from another node program, these are the things that will feel
different, each links to its full page:

- **INP3 link-state routing is on by default.** As well as classic NET/ROM quality,
  XRouter times every packet for a round-trip-time metric (L3RTT) and uses the
  **INP3** extension to choose good paths automatically. The author is a firm
  believer in it, in her words you "have to disable it if you want to be a luddite."
  See [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md).
- **NetRom-X service numbers instead of SSID sprawl.** Rather than a separate SSID
  per service, one node callsign exposes up to 65535 *numbered* services, like
  well-known TCP ports, service 2 is the PMS, service 8 is chat, and so on. So
  `C[onnect] G8PZT 2` reaches that node's mailbox. See
  [NetRom-X service numbers](../networking/netromx-services.md).
- **Its own TCP/IP stack.** XRouter carries a complete multi-homed IP stack of its
  own, alongside the host's, ARP, a router, DNS, NAT, RIP and IPIP/IPUDP tunnels,
  designed in from the start rather than bolted on. See
  [The TCP/IP stack](../networking/tcpip.md).
- **Built-in servers, no add-ons.** A [PMS mailbox](../subsystems/pms-mailbox.md), a
  [chat server](../subsystems/chat.md) (interworking with BPQ RoundTable and JNOS
  Ping-Pong), an [APRS digipeater and IGate](../subsystems/aprs-igate.md), and a
  family of [HTTP (with REST API), MQTT and other TCP servers](../subsystems/http-rest-mqtt.md)
  are all part of the one program.

!!! note "A PMS, not a full BBS"
    XRouter ships an integral **personal mailbox (PMS)**, not a full BBS. If you need
    a full BBS, the usual approach is to run **FBB** alongside XRouter and connect it
    via WA8DED host mode (`DEDHOST`). See [the PMS page](../subsystems/pms-mailbox.md).

## Good to know

XRouter is **closed source**, free for non-commercial amateur use, and released
briskly, at the time of writing the current family is the **v504** series. Get
current builds and support from the
[XRouter group on groups.io](https://groups.io/g/xrouter); see
[Upgrading and versions](../admin/upgrading.md). When you're ready to put a node into
service, read [Security and hardening](../admin/security-hardening.md) first.

---

**Sources:** [XRouter overview, OARC wiki](https://wiki.oarc.uk/packet:xrouter) ·
[in-program manual (OARC wiki)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
