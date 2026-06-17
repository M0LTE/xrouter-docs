# Linking nodes and interoperating

This page is the practical companion to the routing pages: how you actually **link** XRouter
to other nodes, over RF and over the internet, and how to **interoperate** cleanly with the
other node programs you will meet on a real network (BPQ, (X)Net, JNOS and FBB). It is aimed at
a sysop joining an existing network or extending their own, and it is deliberately even-handed:
mixed networks are the norm, and they work.

For the mechanics of the internet link types themselves, this page points you at
**[AXIP / AXUDP / AXTCP linking](../interfaces/axip-axudp-axtcp.md)**; for the routing theory it
points back to **[NET/ROM, INP3 and L3RTT](netrom-inp3.md)**.

## Two ways to link: RF and internet

A link to another node is, at bottom, just another XRouter **port**:

- **Over RF**, a port on a radio frequency you share with the neighbour. The neighbour appears
  in your nodes table once you hear its NODES broadcasts, and routing takes over from there. The
  per-port AX.25 settings ([AX.25 on XRouter](ax25.md)) and the port's `QUALITY` are how you
  describe that link to the routing layer.
- **Over the internet**, an `AXIP`, `AXUDP` or `AXTCP` interface and port that carries AX.25
  between you and a distant node across the net. These are how most modern backbone links are
  built. The full how-to (choosing between them, NAT/firewall behaviour, the `IPLINK` and
  `UDPLOCAL`/`UDPREMOTE` keywords) is on
  **[AXIP / AXUDP / AXTCP linking](../interfaces/axip-axudp-axtcp.md)**. Remember that, being
  IP-based, internet links require a non-zero `IPADDRESS` to function at all (see
  [The TCP/IP stack](tcpip.md)).

Either way, once the link is up the routing layer treats the neighbour the same. What differs
between *programs* is how that neighbour expresses routing information, which is where interop
care comes in.

## Interoperating: INP3 vs quality-only neighbours

XRouter routes on INP3's measured round-trip times by default; many neighbours do not. The
[routing page](netrom-inp3.md) explains the mechanism; here is what it means in practice when you
link to a node running something else.

- **BPQ neighbours.** Linking BPQ and XRouter is routine, the author's own summary is that
  *"there's never been a problem linking BPQ and XRouter."* BPQ supports INP3 but tends to
  **prefer classic NET/ROM quality** to INP3 when it has both. The two ends can therefore form a
  perfectly good link while occasionally disagreeing about which onward path is "best". That is
  usually benign; it only matters when a quality figure is misleading.

- **The runtime/quality conversion caution.** Some programs, **(X)Net** in particular, convert
  NET/ROM qualities to and from run-times. When those converted figures propagate into your
  tables they can manifest as implausibly high qualities that pull traffic the wrong way. The
  author has described exactly this: *"ridiculously high bogus netrom qualities … distorting the
  routing"*, *Paula G8PZT, [groups.io](https://groups.io/g/xrouter/message/4048)*. In severe
  cases sysops have called the resulting NET/ROM network unusable.

!!! warning "Watch your nodes table in a mixed network"
    If your network mixes INP3 (XRouter), quality-only (BPQ) and converting ((X)Net) nodes,
    keep an eye on `N[odes]` for qualities that look too good to be true, and lean on
    `MINQUAL` / `MINTXQUAL` and sensible per-link `QUALITY` values to keep the table honest.
    None of this is a reason not to interoperate, it is a reason to understand *why* a route
    was chosen. See [NET/ROM, INP3 and L3RTT](netrom-inp3.md).

There is no single switch that "fixes" a heterogeneous network; the goal is a sane, well-bounded
nodes table at each node. Set `MAXHOPS`/`MAXTT` horizons appropriately, give real links honest
qualities, and the mixed network settles down.

## Chat interworking

XRouter's chat server is built to bridge the historically separate amateur chat networks, so a
linked XRouter does not strand its users on an island:

- **BPQ RoundTable**, XRouter reserves chat **channel 101** as the RoundTable gateway,
  interworking with the W0RLI / BPQ RoundTable conference network.
- **JNOS "Ping-Pong"**, XRouter's chat also interworks with the JNOS "Tampa Ping-Pong"
  Converse network.

Chat peers are linked with `CHATLINKS` (over NET/ROM or TCP), and links must be **bilateral**,
each side has to list the other's chat callsign, and you should only link servers that are
already in your nodes table and not too distant or slow. The full chat configuration is on
**[Chat server](../subsystems/chat.md)**.

## Running a full BBS alongside XRouter (FBB via host mode)

XRouter ships an integral **PMS** (Personal Message System), not a full BBS. If you want a full
BBS (message areas, bulletins, full forwarding), the established approach is to run **FBB**
alongside XRouter and connect the two through XRouter's **WA8DED / DEDHOST host-mode** emulation.
XRouter presents itself to FBB as a WA8DED-style multi-channel TNC; FBB drives it as its packet
engine while XRouter continues to do the switching and routing. The author confirms the pairing
works: *"I and others have run fbb with Xrpi 32 bit via dedhost."* (community, OARC).

In outline you define an `ASYNC` interface with `PROTOCOL=DEDHOST`, give it the channel count FBB
needs (`CHANNELS`, up to 32) and an application number (`APPLNUM`) matching an `APPL` block, and
point FBB at that host-mode port. The interface and `APPL` block details live with the rest of
the host-mode configuration on [Interfaces](../configuration/interfaces.md) and
[Built-in TCP servers](../subsystems/built-in-servers.md); the PMS itself is on
[PMS mailbox](../subsystems/pms-mailbox.md).

!!! note "PMS or BBS?"
    For a personal mailbox and light traffic, the built-in PMS is enough and needs no extra
    software. Reach for FBB-over-host-mode only when you genuinely need a full BBS, it is more
    to run and maintain.

## A note on even-handedness

XRouter is one of several good node programs, and packet networks are healthier for mixing them.
BPQ is the most widely deployed and includes a full BBS; (X)Net and TheNet/TheNetNode have long
European histories; JNOS brings its own TCP/IP-and-BBS lineage. XRouter's distinctive strengths
are its routing (INP3 + L3RTT) and its designed-in IP stack, but a network is a shared thing,
and the considerate sysop tunes their node to play well with whatever the neighbours run, rather
than expecting the neighbours to change.

## See also

- [AXIP / AXUDP / AXTCP linking](../interfaces/axip-axudp-axtcp.md), the how-to for internet
  links.
- [NET/ROM, INP3 and L3RTT](netrom-inp3.md), the routing behaviour behind the interop notes.
- [Ports](../configuration/ports.md), `QUALITY`, `MINQUAL`, `MAXHOPS`, `MAXTT` and the per-link
  settings.
- [Chat server](../subsystems/chat.md) and [PMS mailbox](../subsystems/pms-mailbox.md), the
  services referenced above.

---

**Sources:** in-program manual on the OARC wiki,
[Subsystems and features (man9)](https://wiki.oarc.uk/packet:xrouter:docs:man9) (host mode,
chat interworking, services) ·
[Configuration Directives (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7);
author guidance on interop, groups.io,
[bogus qualities / (X)Net conversion](https://groups.io/g/xrouter/message/4048) ·
[mixed INP3 / quality networks](https://groups.io/g/xrouter/message/2971);
chat-network interworking, OARC wiki,
[chat server](https://wiki.oarc.uk/packet:xrouter:chatserver).
