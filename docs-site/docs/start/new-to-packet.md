# New to packet radio

This page is a roadmap for someone who has never run a packet station. It tells you
what you'll need, in what order to learn things, and what to expect — so that by the
end you have a realistic plan rather than a pile of jargon.

Packet radio is a way of sending data over amateur radio: your computer frames bytes
into AX.25 packets, a modem turns them into tones, and a radio puts them on the air.
A *node* like XRouter sits in the middle, switching and routing those packets between
users and other nodes. You don't need to understand all of that yet — just know that
XRouter is the node software, and a few other pieces have to be in place around it.

## What you'll need

Three things, beyond a computer to run XRouter on:

- **A licensed callsign.** Packet operates on amateur frequencies, so you need an
  amateur radio licence and a callsign of your own. Everything XRouter does is keyed
  to your callsign; there's no anonymous operation on the air.
- **A radio.** A 2 m or 70 cm FM transceiver is the usual starting point for VHF/UHF
  packet. Almost any rig that can key up and pass audio will do for 1200-baud work.
- **A TNC or a soundcard modem.** Something has to turn data into sound and back. That
  can be a hardware **KISS TNC** (including modern boards like the **NinoTNC**), or a
  software *soundcard modem* such as **Direwolf**, **UZ7HO SoundModem** or
  **QtSoundModem** running on the same computer. See
  [KISS, TNCs and soundcard modems](../basics/kiss-tncs-and-modems.md).

!!! tip "No radio yet? You can still start"
    You can bring XRouter up and link it to other nodes over the internet using an
    [AXIP / AXUDP / AXTCP](../interfaces/axip-axudp-axtcp.md) link, with no radio at
    all. It's a good way to learn the software before you add RF.

## Read the basics, in order

Before you configure anything, work through the **[Packet radio basics](../basics/what-is-packet-radio.md)**
primers. They're short, and each later page assumes the earlier ones:

1. [What is packet radio?](../basics/what-is-packet-radio.md) — the big picture.
2. [AX.25 — the data link](../basics/ax25.md) — how packets are addressed and
   acknowledged.
3. [NET/ROM — networking and routing](../basics/netrom.md) — how nodes find paths to
   each other.
4. [KISS, TNCs and soundcard modems](../basics/kiss-tncs-and-modems.md) — how your
   computer talks to the radio.
5. [APRS in brief](../basics/aprs.md) and
   [IP over AX.25 and AMPRNet](../basics/ip-over-ax25.md) — two things XRouter can do
   that you'll meet later; skim these for now.
6. [Nodes, switches and the sysop role](../basics/nodes-and-sysops.md) — what you're
   signing up for as the person who runs the node.

## Then build your first node

With the concepts in hand, follow **[Getting started](../getting-started/index.md)**
for your platform — [Raspberry Pi](../getting-started/raspberry-pi.md),
[Linux PC](../getting-started/linux-pc.md) or
[Windows](../getting-started/windows.md). It walks you from download to first run,
then on to [your first XROUTER.CFG](../getting-started/first-config.md).

## Realistic expectations

Packet is a hobby that rewards patience. XRouter is well documented and quick to get
*running*, but getting a station *talking to other stations* depends on things outside
the software — radio levels, your modem's tuning, what's audible from your location,
and who else is on. Expect to spend an evening or two getting the first connection,
and to lean on the [troubleshooting guide](../admin/troubleshooting.md) and the
[support group](https://groups.io/g/xrouter) along the way. Once it clicks, it stays
running for years.

---

**Sources:** [Hibby's Packet Radio Guide](https://guide.hibbian.org/software/) ·
[XRouter overview, OARC wiki](https://wiki.oarc.uk/packet:xrouter) ·
[XRouter support group (groups.io)](https://groups.io/g/xrouter)
