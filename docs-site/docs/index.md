# XRouter documentation

XRouter is a packet-radio **node, router, chat server and APRS digipeater** in a single
program, written and maintained by **Paula G8PZT**. From one small executable it provides
an AX.25 / NET-ROM packet switch with the INP3 link-state routing extension, its own
multi-homed TCP/IP stack, a personal mailbox (PMS), a multi-channel chat server, an APRS
digipeater and IGate, and a family of built-in internet servers: Telnet, an HTTP server
with a REST API, FTP, an MQTT broker and more.

It has been in continuous development since 1996, is free for non-commercial amateur use,
and is closed source. It is widely regarded in the packet community as the main alternative
to G8BPQ's BPQ32 / LinBPQ, and is often described as quick to get on the air and strong at routing.

!!! info "Not sure where to begin?"
    Jump to **[Start here](start/index.md)** and pick the path that matches you, whether
    you've never run a packet station, you know packet but are new to XRouter, or you're
    moving across from BPQ or another node program.

## One program, several builds

XRouter is distributed as a single self-contained binary per platform. The names are
historical; the current builds share one codebase:

| Name | Platform | Status |
| --- | --- | --- |
| **XrPi** | Raspberry Pi / ARM (Linux) | Current, actively developed |
| **XrLin** | Linux PC, x86 / x86-64 | Current; *same codebase* as XrPi, different CPU |
| **XrWin** | Windows | Current |
| **XR32** | Windows (legacy) | Frozen around 2013; superseded by XrWin |
| **XRouter (DOS)** | DOS | The original; legacy |

XrPi and XrLin are literally the same source compiled for two processor families (the Pi
build simply adds GPIO support that x86 lacks). **Everything in this site applies to all
current builds unless a page says otherwise**; where Windows or legacy builds differ, the
page calls it out.

!!! note "About the XR32 manual"
    The well-known `XR32.doc` Word manual documents the *legacy* Windows build. It is still
    a useful conceptual reference, but the current Windows program is **XrWin**, and the
    living reference for all builds is the in-program manual (see
    [Sources and attribution](project/sources-and-attribution.md)).

## What it does

In one process, XRouter gives a sysop:

- An **AX.25 / NET-ROM packet switch** with connected-mode sessions, digipeating, and
  modern AX.25 features (extended sequence numbers, selective reject).
- **Routing** by classic NET/ROM quality *and* the **INP3** link-state extension with
  round-trip-time measurement (L3RTT), so the node can choose good paths automatically.
- **NetRom-X**, XRouter's service-numbering scheme: one node callsign exposing up to
  65535 numbered services (mailbox, chat, info and so on), like well-known TCP ports.
- Its own **TCP/IP stack** alongside the host's, for IP-over-AX.25, AMPRNet (44Net),
  tunnels, NAT and RIP, useful whether or not you run amateur IP.
- A **PMS mailbox**, a **chat server** (interworking with BPQ RoundTable and JNOS
  Ping-Pong), and an **APRS digipeater / IGate**.
- Built-in **Telnet, HTTP (with REST API), FTP, Finger, MQTT** and other servers, so the
  node can be operated and monitored locally or remotely.

It talks to radios through hardware **KISS** TNCs, **soundcard modems** (Direwolf, UZ7HO
SoundModem, QtSoundModem), **AGWPE**, **VARA** and **ARDOP**, and links to other nodes over
RF or the internet via **AXIP / AXUDP / AXTCP**.

## Where to start

<div class="grid cards" markdown>

- :material-radio-tower: **[New to packet radio](start/new-to-packet.md)**

    Start with the [basics](basics/what-is-packet-radio.md): what packet is, AX.25,
    NET/ROM, KISS and modems, then stand up your first node.

- :material-console: **[New to XRouter](start/new-to-xrouter.md)**

    You know packet. Go straight to [Getting started](getting-started/index.md), the
    [configuration model](configuration/index.md) and the [command set](commands/node-prompt.md).

- :material-swap-horizontal: **[Migrating from BPQ](start/migrating-from-bpq.md)**

    Coming from BPQ or another node program? See what maps across, what's different
    (INP3, NetRom-X, the IP stack), and how to interoperate.

</div>

## About this site

This is an **unofficial, community-maintained re-presentation** of XRouter's documentation,
organised by what a reader is trying to do and cited to primary sources. It is **not
authoritative**. The author, Paula G8PZT, is the authority on how XRouter behaves; the
in-program manual (mirrored on the OARC wiki) is the canonical reference. Where this site
disagrees with the author or the program, treat **the author as right**, and please
[contribute a correction](project/contributing.md). See
[About these docs](project/about-these-docs.md) for how the site was made and what it is not.

!!! note "Written entirely by AI"
    Every page on this site was written entirely by an AI (a large language model) from the
    primary sources listed below, and checked by that same model against them. **No human has
    fact-checked or verified any of it.** Treat the site as a useful but unverified secondary
    source, not the author's own words, and confirm anything that matters against the
    in-program manual. See
    [How this site was written](project/about-these-docs.md#how-this-site-was-written).

## Versions and where to get it

XRouter releases briskly; at the time of writing the current family is the **v504** series
(for example *v504v*, January 2026). The most reliable place to obtain current builds and
support is the **[XRouter support group on groups.io](https://groups.io/g/xrouter)** (latest
binaries live in its *Files* area). Older mirrors exist but lag behind. See
[Upgrading and versions](admin/upgrading.md) and
[Sources and attribution](project/sources-and-attribution.md).

---

**Sources:** [XRouter support group (groups.io)](https://groups.io/g/xrouter) ·
[In-program manual mirrored on the OARC wiki](https://wiki.oarc.uk/packet:xrouter) ·
[ohiopacket.org XRPi mirror](https://ohiopacket.org/xrpi/) ·
[Hibby's Packet Radio Guide](https://guide.hibbian.org/software/)
