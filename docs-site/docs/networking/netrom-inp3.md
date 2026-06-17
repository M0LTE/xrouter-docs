# NET/ROM, INP3 and L3RTT

Routing is XRouter's signature strength, and this is the page that explains it. It covers
classic NET/ROM routing as XRouter implements it (the nodes table, link quality, the NODES
broadcast) and then the two modern extensions that set XRouter apart: **INP3**, a
link-state routing protocol that measures real round-trip time, and **L3RTT**, the mechanism
that takes those measurements. It finishes with how XRouter *combines* the old and new
metrics when it picks a path, and the interoperability traps to watch for in a mixed network.

If you have never met NET/ROM, read the primer first:
**[NET/ROM: networking and routing](../basics/netrom.md)**. This page assumes you know what
a node, an alias and a "neighbour" are.

## Classic NET/ROM in XRouter

NET/ROM gives every node a table of the other nodes it can reach and, for each, the best
neighbour(s) to hand traffic to. XRouter builds and maintains that table the traditional way.

**The nodes table.** XRouter keeps a table of known destination nodes (callsign + alias) and,
for each, up to three routes: a neighbour, the port to reach it on, and a *quality* figure.
Users list it with `N[odes]`; `N <call>` shows the routes to one node, the active one marked
`>`. The table is capped by `MAXNODES` and the neighbour-route table by `MAXROUTES`. Because
a table rebuilt from scratch off-air can take hours to repopulate, XRouter saves it to the
`XRNODES` file periodically and reloads it at startup, so a restart does not cost you the
whole network.

**Quality.** Every neighbour link has a NET/ROM **quality** in the range 0–255, set per port
with the `QUALITY` keyword (default `10`). Quality expresses how good a link is: a fast,
reliable backbone might be 200+, a marginal user channel much lower. When a node is reachable
via several paths, classic NET/ROM picks the route whose *accumulated* quality is highest.
Setting a port's `QUALITY=0` is the standard way to **disable NET/ROM (and the higher layers)
on that port**, useful on a busy user-only access frequency where you do not want node
broadcasts.

**Which nodes get kept and advertised.** Two thresholds keep the table sane:

- `MINQUAL`: the minimum quality a node must have before XRouter will *add* it to the table
  at all (default `10`). Raise it to keep distant, low-quality "DX" nodes out.
- `MINTXQUAL`: the minimum quality before XRouter will *re-broadcast* a node to its own
  neighbours (default `0`).

**The NODES broadcast.** Periodically each node sends a NODES broadcast advertising the
nodes it knows and the quality at which it can reach them; neighbours fold that into their own
tables. The interval is `NODESINTERVAL`, in minutes (default `60`). You can trigger one
on demand with `BC[ast]` and ask a neighbour to send theirs with `BCP[oll]`.

!!! tip "Be a good neighbour with NODESINTERVAL"
    `NODESINTERVAL` exists both globally and per port. If one awkward neighbour needs more
    frequent broadcasts, set a shorter interval **on that port** rather than lowering the
    global value and flooding the whole network. Faster-than-default global broadcasts are
    poor network manners.

Obsolescence counters (`OBSINIT`, `OBSMIN`) age nodes out of the table when they stop being
heard, and `L3TTL` caps how many hops a NET/ROM layer-3 packet may live for. The full list of
NET/ROM directives and their scope is on
[Ports](../configuration/ports.md) and the directive reference.

## INP3: link-state routing with measured round-trip time

Classic NET/ROM quality is a static, sysop-assigned guess. It does not know that a link has
become congested, or that a path two hops further round is actually faster today. **INP3**
(the Inter-Node Protocol, version 3) fixes that. It is a link-state routing extension that
runs over AX.25 and distributes, for each reachable node, a **measured round-trip time** and
a hop count rather than only a static quality. XRouter then routes on information that
reflects the network as it actually is, not as it was configured.

A few practical points for the sysop:

- **It is on by default.** XRouter ships with INP3 enabled; you have to go out of your way to
  turn it off. The author is blunt about this: she calls disabling it being "a luddite."
- **Two horizons bound how far INP3 information propagates**, both settable globally and per
  port:
    - `MAXHOPS`: the hop-count horizon (default `30`). Nodes beyond this many hops are not
      carried.
    - `MAXTT`: the maximum trip-time horizon. Nodes whose round-trip time exceeds it are not
      carried.
- **The routing horizon is commonly stated as about 600 seconds.** The author and community
  describe an INP3 routing horizon of ~600 s (a figure also associated historically with
  FlexNet). Treat the exact number as author/community-stated rather than something you set
  with a single named keyword; the `MAXTT` units and default differ between documentation
  versions, so verify against your own build before tuning it precisely.

Because INP3 reacts to measured conditions, it can shift traffic onto a better path
automatically, the behaviour users praise XRouter's routing for. The cost is a little extra
control traffic, which is exactly why some sysops dislike it; the author's own view is simply
that *"it is far better than netrom."* (*Paula G8PZT,
[groups.io](https://groups.io/g/xrouter/message/2214)*).

## L3RTT: how XRouter measures link round-trip time

INP3 is only as good as its measurements, and **L3RTT** ("layer-3 round-trip time") is how
XRouter takes them. At intervals set by `L3RTTINTERVAL` (default `300` seconds) the node
exchanges timing packets with its neighbours and records the round-trip time of the link.
Those measured RTTs are what feed INP3's metrics, and as a useful side effect the periodic
exchange also keeps inter-node links alive.

L3RTT can be suppressed on a per-port basis: the `CFLAGS` bitmask has a bit (value 8) that
disables L3RTT on a port, for links where you do not want the extra timing traffic.

## Combining quality and INP3 when choosing a route

So XRouter holds, potentially, *both* a classic NET/ROM quality and live INP3 trip-time data
for a destination. Which wins? XRouter weighs everything together, but **leans on INP3 when
it has it**. In the author's words:

> "When deciding how to route a packet, xrouter makes a complex decision based on all the
> available information, but if inp3 data is available, quality data plays a lesser part."
> *Paula G8PZT, [groups.io](https://groups.io/g/xrouter/message/2237)*

In other words, quality still matters: it is your input for links INP3 cannot measure, and a
fallback where INP3 data is absent. But where XRouter has a real round-trip measurement, that
measurement leads. This is why thoughtfully set `QUALITY` values remain worthwhile even on an
INP3 network: they describe the links INP3 has nothing better to say about.

### AUTOQUAL: automatic route quality

You can also let XRouter derive a port's quality from its measured performance instead of
pinning a number. **AUTOQUAL** is enabled by giving a `QUALITY` (or a `ROUTE ADD` quality) in
the range **256–511** rather than 0–255; XRouter then sets the effective quality automatically
from what L3RTT observes. It is a convenient middle ground between hand-tuning every link and
trusting INP3 alone.

## Interoperating with quality-only and converting neighbours

A pure-XRouter network routes beautifully on INP3. A *mixed* network needs care, because not
every node program speaks INP3 the same way, or at all:

- **Quality-only neighbours (e.g. BPQ).** BPQ supports INP3 but, as the author describes it,
  tends to *prefer* old-style NET/ROM quality to INP3. Where one side routes on quality and
  the other on trip time, the two views of "best path" can disagree.
- **Programs that convert between qualities and run-times (e.g. (X)Net).** (X)Net converts
  NET/ROM qualities to and from run-times. When those conversions feed back into your tables
  they can inject, in the author's phrase, *"ridiculously high bogus netrom qualities …
  distorting the routing"* (*Paula G8PZT,
  [groups.io](https://groups.io/g/xrouter/message/4048)*). In bad cases sysops have described
  the resulting NET/ROM network as effectively unusable.

The practical consequence is that quality figures arriving from foreign nodes cannot always
be trusted at face value, and XRouter has gained options over time to de-rate INP3 qualities
that did not originate from XRouter. None of this means "don't interoperate". Mixed BPQ /
XRouter networks are common and generally fine, but it does mean a sysop in a heterogeneous
network should watch their nodes table for implausible qualities and understand *why* a path
was chosen. The practical side of running such links is covered on
**[Linking nodes and interoperating](linking-and-interop.md)**.

## See also

- [Ports](../configuration/ports.md): `QUALITY`, `MINQUAL`, `MINTXQUAL`, `NODESINTERVAL`,
  `MAXHOPS`, `MAXTT`, `CFLAGS` and the rest, with their scope.
- [Linking nodes and interoperating](linking-and-interop.md): the practical INP3-vs-quality
  guidance for real mixed networks.
- [NetRom-X service numbers](netromx-services.md): addressing services *on* the nodes you
  can now route to.

---

**Sources:** in-program manual on the OARC wiki:
[Configuration Directives (man7)](https://wiki.oarc.uk/packet:xrouter:docs:man7) ·
[Subsystems and features (man9)](https://wiki.oarc.uk/packet:xrouter:docs:man9);
author's INP3/L3RTT concept pages on the ohiopacket mirror:
[INP3](https://ohiopacket.org/xrpi/docs/inp3.htm) ·
[L3RTT](https://ohiopacket.org/xrpi/docs/l3rtt.htm) ·
[AUTOQUAL](https://ohiopacket.org/xrpi/docs/autoqual.htm);
author guidance on routing and interop, groups.io:
[INP3 vs quality](https://groups.io/g/xrouter/message/2237) ·
[bogus qualities / interop](https://groups.io/g/xrouter/message/4048) ·
[600-second horizon](https://groups.io/g/xrouter/message/4055).
