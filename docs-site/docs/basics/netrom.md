# NET/ROM — networking and routing

[AX.25](ax25.md) can carry a packet across a gap if you name every digipeater by hand.
NET/ROM removes that burden: it lets nodes route traffic automatically across a multi-hop
network, so a user can ask for a distant node by name and let the network find the way. This
page covers nodes and aliases, NODES broadcasts and quality, hop-by-hop acknowledgement,
and the modern INP3 extension.

## What NET/ROM is

NET/ROM adds a simple **network and transport layer** (OSI layers 3 and 4) on top of
connected-mode AX.25, so packets can be routed automatically instead of relying on
hand-specified digipeater paths. It began life as a commercial replacement EPROM for the
TNC-2, produced by Software 2000 in California: you bought a NET/ROM ROM per band and linked
the TNCs through their serial ports. The protocol long outlived that hardware — John Wiseman
**G8BPQ** reimplemented it in software (the basis of BPQ32/LinBPQ), the Linux kernel gained
a native implementation, and XRouter (Paula G8PZT) is another independent member of the same
family.

## Nodes, aliases and neighbours

A NET/ROM **node** is identified by both a **callsign-SSID** and a friendly **alias** — for
example alias `LEEDS` with callsign `GB7XYZ`. You connect to a node and can then ask it to
connect onward to another node by alias or callsign. A node's **neighbours** are the nodes
it can reach in a single AX.25 hop over a radio port or link.

## NODES broadcasts and quality

Each NET/ROM node periodically transmits a **NODES broadcast** — an AX.25 UI frame — listing
the nodes it knows about and a **quality** figure for reaching each one. Neighbours receive
these broadcasts, build and maintain their routing tables, and derate the advertised quality
as it propagates further out. In this way every node gradually learns multi-hop routes and
which neighbour is the best next hop towards any destination.

Quality is a number from 0 to 255. The exact values are conventions rather than protocol
law: a neighbour on your own site might be advertised high and values derate by hop. Badly
chosen qualities — for instance everyone broadcasting 255 — cause "route hijacking" and
bloated, unconnectable node tables. NET/ROM's poor reputation is usually misconfiguration by
sysops, not a flaw in the protocol.

## Why it beats plain digipeating

The decisive improvement is **hop-by-hop acknowledgement**. In a NET/ROM network adjacent
nodes acknowledge packets between themselves, so a lost frame is retried only over the one
marginal link that dropped it — not across the entire path, as plain digipeating requires.
Add **automatic, adaptive routing** (a route can change to a better one, even mid-connection)
and friendly **aliases** instead of memorised digipeater chains, and NET/ROM is far more
robust and usable over a real network of many imperfect RF hops.

## INP3 — the modern extension

**INP3** ("Inter-Node Protocol 3") is a more modern way for neighbouring nodes to exchange
routing information. Where NET/ROM broadcasts *quality* in UI frames, INP3 **unicasts**
updates to each neighbour over the normal connected inter-node link, and its primary metric
is **trip time** (with hop count as a secondary metric). It propagates bad news — a lost or
worsened route — promptly. NET/ROM and INP3 can coexist on one network because they track
different metrics. XRouter implements INP3 (alongside its own round-trip-time measurement),
which is a large part of why the community rates it for routing.

## On XRouter

XRouter is a full NET/ROM node with the INP3 extension. For how it is configured and tuned,
see [NET/ROM, INP3 and L3RTT](../networking/netrom-inp3.md); for XRouter's service-numbering
scheme that exposes many services behind one callsign, see
[NetRom-X service numbers](../networking/netromx-services.md).

---

**Sources:**
[XRouter/XRPi INP3 documentation](https://ohiopacket.org/xrpi/docs/inp3.htm) ·
[OARC packet history (G4KLX)](https://wiki.oarc.uk/packet:history) ·
[NET/ROM qualities (N1URO)](https://packet-radio.net/netrom-qualities/) ·
[Packet radio (Wikipedia)](https://en.wikipedia.org/wiki/Packet_radio)
